# Salesforce Agentforce/Einstein GenAI Components Research

*Reviewed and lightly corrected 2026-10-30 against `ara` repo, branch `master`, commit `ba1083bd605581d3da5ca881a9e10db9e86fb878`.*

## Scope Note

Unlike the other files in this folder, this document is **general Salesforce platform research** on the Agentforce/Einstein GenAI metadata model (GenAiFunction, GenAiPromptTemplate, GenAiPlugin) rather than ARA-specific inventory — it was found to be substantively accurate as platform documentation and has been retained, with corrections limited to (a) removing a terminology slip around file extensions and (b) replacing the generic/hypothetical "Recruiting Use Cases" section with ARA's actual plugin inventory so the research is grounded in what this package really implements. For ARA's real component counts and per-component documentation, see `ARA_GenAI_Functions_Documentation.md`, `ARA_GenAI_Plugins_Documentation.md`, and `ARA_GenAI_Prompt_Templates_Documentation.md`.

## Executive Summary

Salesforce's Agentforce represents an AI agent platform that leverages three core metadata types to enable AI-powered automation: **GenAiFunction**, **GenAiPromptTemplate**, and **GenAiPlugin**. These components work together to create intelligent, conversational AI agents capable of understanding context, reasoning through complex tasks, and executing actions within the Salesforce ecosystem. ARA is a real-world, in-production example of all three being used extensively (44 functions, 16 prompt templates, 11 plugins as of this writing).

## Core Components Overview

### 1. GenAiFunction
**Purpose**: Represents executable actions that AI agents can perform within Salesforce.

**Key Characteristics**:
- Defines specific tasks or operations (e.g., updating records, sending emails, retrieving information)
- Links to Apex classes or Prompt Templates through the `invocationTarget` property, with `invocationTargetType` of `apex` or `generatePromptResponse`
- Includes input/output schemas defined as JSON Schema files (`input/schema.json`, `output/schema.json`) using Salesforce's `lightning:type` extensions (e.g. `lightning__recordInfoType`, `lightning__dateTimeStringType`, `lightning__richTextType`) rather than generic JSON Schema primitives
- Stored as a folder per function under `genAiFunctions/`, each containing a `.genAiFunction-meta.xml` plus `input/` and `output/` schema subfolders

**Technical Details**:
- **invocationTarget**: Specifies the Apex class or Prompt Template to invoke
- **input/output schemas**: JSON Schema files defining parameters and expected results, with rich natural-language `description` fields that function as inline prompting instructions to the reasoning engine (confirmation gates, formatting rules, parsing rules)
- **confirmation requirements**: Enforced via instruction text in the schema/plugin rather than a dedicated boolean flag in every case — ARA's functions frequently embed explicit "do not proceed without confirmation" language directly in field descriptions and plugin instructions

### 2. GenAiPromptTemplate
**Purpose**: Provides structured, reusable instructions (prompts) for generative AI models to ensure consistent outputs.

**Key Characteristics**:
- Created using Salesforce's Prompt Builder
- Can reference Salesforce data (fields, data graphs, contextual RAG)
- Securely routed through Salesforce's trust layer for data privacy
- Supports a `type` attribute — ARA exclusively uses `einstein_gpt__flex` (Prompt Builder's Flex template type) across all 16 of its templates, rather than Field Generation or Record Summary types
- Supports localization with language settings

**Technical Details**:
- **templateVersions**: Contains the actual prompt instructions, input parameters, and grounding context
- **relatedEntity / inputs**: Specifies which Salesforce objects/fields are referenced
- **language**: Defines localization settings

### 3. GenAiPlugin
**Purpose**: Logically groups related GenAiFunctions and actions into agent topics for modular deployment.

**Key Characteristics**:
- Organizes functions into functional areas/"Topics" (ARA: Recruiting, Interview_Management, Candidate_Summary, Email_Management, Recruitment_Marketing, JobFitAssessment, Sourcing_Management, Schedule_Voice_Interview, Reschedule_Voice_Interview, Interview_Reminder, Screening_Process)
- `pluginType` is commonly `Topic` in practice (all 11 of ARA's plugins use this value)
- Provides context and instructions for grouped actions via `genAiPluginInstructions` — in ARA these instructions are frequently hundreds of lines long, encoding explicit step-by-step workflows, validation gates, and formatting mandates rather than brief guidance
- `aiPluginUtterances` supplies example phrases for topic-routing/training (present on several but not all ARA plugins)

**Technical Details**:
- **genAiFunctions**: List of functions associated with the plugin
- **genAiPluginInstructions**: Natural language guidelines for task execution
- **scope**: A natural-language description of what the topic covers, used by the reasoning engine to route user intent to this plugin vs. others

## Architecture and Integration

### Component Relationships
1. **Plugins categorize functions**: GenAiPlugins group related GenAiFunctions into logical topics
2. **Functions execute logic**: GenAiFunctions perform specific actions (Apex) or delegate to generation (Prompt Template)
3. **Templates guide behavior**: GenAiPromptTemplates provide contextual instructions that guide agent responses

### Apex Integration
- **Invocable Apex classes**: The dominant integration pattern observed in ARA — Apex classes implementing the function's business logic, invoked directly by `apex`-type GenAiFunctions
- **Inbound REST webhooks** (distinct from "agent-invocation REST APIs"): ARA has 2 `@RestResource` Apex classes (`ARA_VoiceInterviewWebhookHandler`, `ARA_CandidateScreeningWebhookHandler`) that receive callbacks from an external voice-calling provider — these are not part of the Agentforce invocation model itself, but a separate integration surface
- **Named Credentials / Remote Site Settings**: ARA uses 1 `RemoteSiteSetting` (`Voice_Agent_Remote_Setting`) for its outbound voice-provider callouts

### Reasoning Engine
Salesforce's Atlas Reasoning Engine serves as the AI core that:
- Synthesizes structured and unstructured data
- Decomposes complex queries into subtasks
- Dynamically selects appropriate plugins (topics) and functions based on each plugin's `scope` and utterances
- Enables multi-step action chaining, as seen in ARA's confirmation-gated workflows (e.g. Email_Management's 4-step validation-gate sequence)

## Patterns Observed in ARA Specifically

### 1. Deliberate Topic Boundary Narrowing
Several ARA plugins' `description`/`scope` fields explicitly state what they must **not** handle, to prevent the reasoning engine from routing a request to the wrong topic — e.g. Interview_Reminder explicitly disclaims scheduling/submission/feedback duties; Schedule_Voice_Interview and Reschedule_Voice_Interview both require the literal word "voice" in the user's initial prompt to activate, deferring to Interview_Management otherwise.

### 2. Confirmation-Gated Writes
Every ARA plugin capable of a persistent write (save, send, post externally) encodes an explicit "ask for confirmation before proceeding" instruction, rather than relying on a platform-level approval primitive.

### 3. Sub-task Decomposition via Dedicated Prompt Templates
Rather than one large prompt per plugin, ARA frequently isolates a narrow sub-task (date/time parsing, SOQL generation, notes cleanup, screening-question reframing) into its own `GenAiPromptTemplate`, composed into the larger workflow by the consuming Apex class or plugin instructions.

### 4. Reuse of Platform (Non-ARA) Functions
`EmployeeCopilot__GetRecordDetails`, `EmployeeCopilot__QueryRecords`, and `EmployeeCopilot__IdentifyObjectByName` — standard Salesforce Copilot platform actions — are reused across 7 of ARA's 11 plugins rather than being reimplemented as ARA-specific functions.

## Implementation Considerations

### Deployment Characteristics
1. **Dependency Management**: GenAiFunctions require their referenced Apex classes/Prompt Templates to be present in the target org; ARA's package.xml bundles all of these together for packaging
2. **Schema/Metadata Coupling**: JSON schema files and the `.genAiFunction-meta.xml` must stay in sync — a mismatch is a common deployment failure mode
3. **Package Dependency**: ARA's functions/plugins directly reference `bpats__`-namespaced ATS objects (`bpats__Job__c`, `bpats__Contact_List__c`), making ARA's Agentforce layer dependent on ASYMBL-ATS being installed first

## Conclusion

Salesforce's Agentforce GenAI components represent a powerful framework for building intelligent, conversational AI agents, and ARA is a substantial production example of the pattern: 44 functions, 16 prompt templates, and 11 plugins (topics) working together, with an unusually strong emphasis on explicit confirmation gates and deliberate topic-boundary narrowing to keep the reasoning engine routing user intent correctly across a large number of closely related recruiting workflows.
