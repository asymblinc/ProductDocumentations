# ASM Package Documentation

Welcome to the comprehensive documentation for the **ASM (Asymbl Search and Match) Package** - a powerful Salesforce-based solution for resume parsing, candidate search, and skills management.

## 📋 Table of Contents

- [Overview](#overview)
- [Package Statistics](#package-statistics)
- [Entity Relationship Diagrams](#entity-relationship-diagrams)
- [Core Documentation](#core-documentation)
- [Object Categories](#object-categories)
- [Integration Architecture](#integration-architecture)
- [Getting Started](#getting-started)
- [Quick Navigation](#quick-navigation)

## 🎯 Overview

The ASM Package is a sophisticated Salesforce application designed to enhance recruitment efficiency through advanced resume parsing, intelligent candidate search, and comprehensive skills management. It integrates seamlessly with leading resume parsing services and provides powerful search capabilities.

### Key Features

- **Resume Parsing**: Advanced resume parsing with Daxtra and RChilli integration
- **Candidate Search**: Intelligent search algorithms with skills matching
- **Skills Management**: Comprehensive skills tracking and assessment
- **Education History**: Detailed education background management
- **Employment History**: Complete professional experience tracking
- **LWC Components**: Modern Lightning Web Components for enhanced user experience
- **Custom Objects**: Specialized objects for education, employment, and skills data

## 📊 Package Statistics

| Metric | Count |
|--------|-------|
| **Apex Classes** | 76 |
| **Triggers** | 3 |
| **Custom Objects** | 3 |
| **Lightning Web Components** | 24 |
| **Custom Metadata Types** | 5 |
| **Resume Parsing Providers** | 2 (Daxtra, RChilli) |
| **Core ERD Diagrams** | 8+ |

## 🗺️ Entity Relationship Diagrams

Our documentation includes comprehensive ERD diagrams organized by functional area:

### Core Business Process ERDs

| ERD Diagram | Description | Key Objects |
|-------------|-------------|-------------|
| [**Resume Parsing Architecture**](ERD_Resume_Parsing.md) | Daxtra and RChilli integration workflows | ASM_Education_History__c, Employment_History__c |
| [**Skills Management**](ERD_Skills_Management.md) | Skills tracking, assessment, and matching | ASM_Skill__c, Skills matching algorithms |
| [**Education History Management**](ERD_Education_History.md) | Education background tracking and validation | ASM_Education_History__c |
| [**Employment History Management**](ERD_Employment_History.md) | Professional experience and career progression | Employment_History__c |

### Technical Architecture ERDs

| ERD Diagram | Description | Coverage |
|-------------|-------------|----------|
| [**Search and Match Engine**](ERD_Search_Match_Engine.md) | Candidate search algorithms and matching logic | Search parameters, matching criteria |
| [**Parsing Integration Objects**](ERD_Parsing_Integration.md) | Resume parsing service integrations | Daxtra, RChilli APIs and data mapping |
| [**LWC Component Architecture**](ERD_LWC_Components.md) | Lightning Web Component structure and interactions | Component relationships, data flow |
| [**Custom Metadata Configuration**](ERD_Custom_Metadata.md) | Configuration objects and settings management | Metadata types and configuration |

### System Extension ERDs

| ERD Diagram | Description | Objects Covered |
|-------------|-------------|-----------------|
| [**Standard Object Extensions**](ERD_Standard_Object_Extensions.md) | Custom fields on standard Salesforce objects | Contact, Account extensions |
| [**Trigger Architecture**](ERD_Trigger_Architecture.md) | Trigger implementations and automation logic | Before/after triggers, bulk processing |

### Audit and Completeness

| ERD Diagram | Purpose |
|-------------|---------|
| [**Missing Objects Complete**](ERD_Missing_Objects_Complete.md) | Documentation of objects not covered in other ERDs |
| [**Audit Missing Objects**](ERD_Audit_Missing_Objects.md) | Comprehensive audit report of documentation gaps |
| [**ERD Index**](ERD_Index.md) | Master index of all ERD diagrams with cross-references |

## 📖 Core Documentation

### Main Package Documentation

**[ASM Package Documentation](ASM_Package_Documentation.md)** - The comprehensive guide containing:

- Complete Apex class inventory and functionality
- Trigger implementation details and logic
- Custom object field-level documentation
- Lightning Web Component specifications
- Resume parsing integration architecture
- Search and match algorithm documentation
- Skills management workflows
- Security and permission model
- Implementation guidelines
- Best practices and recommendations

*This is the primary reference document for understanding the complete ASM package structure and functionality.*

## 🏗️ Object Categories

### 1. Core Custom Objects
- **ASM_Education_History__c**: Educational background and qualifications
- **Employment_History__c**: Professional experience and employment records
- **ASM_Skill__c**: Skills inventory and proficiency tracking

### 2. Apex Architecture
- **76 Apex Classes**: Comprehensive business logic implementation
- **Resume Parsing Controllers**: Daxtra and RChilli integration classes
- **Search Engine Classes**: Advanced candidate search algorithms
- **Skills Management Classes**: Skills assessment and matching logic
- **Utility Classes**: Helper methods and common functionality

### 3. Lightning Web Components
- **24 LWC Components**: Modern user interface components
- **Resume Upload Components**: Drag-and-drop resume processing
- **Search Interface Components**: Advanced search and filter capabilities
- **Skills Assessment Components**: Interactive skills evaluation tools
- **Dashboard Components**: Analytics and reporting displays

### 4. Triggers and Automation
- **3 Triggers**: Event-driven automation
- **Data Validation Triggers**: Ensure data quality and consistency
- **Integration Triggers**: Handle external service callbacks
- **Workflow Triggers**: Automate business processes

## 🔗 Integration Architecture

The ASM package includes robust integration capabilities:

### Resume Parsing Services
- **Daxtra**: AI-powered resume parsing with advanced field extraction
- **RChilli**: Comprehensive resume parsing with skills identification
- **Multi-provider Support**: Fallback mechanisms and provider selection

### Search and Match Engine
- **Intelligent Search**: Advanced search algorithms with fuzzy matching
- **Skills Matching**: Semantic skills comparison and scoring
- **Education Matching**: Degree and qualification equivalency
- **Experience Matching**: Professional background alignment

### Custom Metadata Configuration
- **Parsing Configuration**: Provider-specific settings and mappings
- **Search Parameters**: Configurable search weights and criteria
- **Skills Taxonomy**: Hierarchical skills categorization
- **Integration Settings**: API endpoints and authentication

## 🚀 Getting Started

### For Developers
1. Start with [ASM Package Documentation](ASM_Package_Documentation.md) for complete technical overview
2. Review [ERD Index](ERD_Index.md) for object relationship understanding
3. Check [Parsing Integration ERD](ERD_Parsing_Integration.md) for resume parsing setup
4. Explore [LWC Component Architecture](ERD_LWC_Components.md) for UI development

### For Business Users
1. Begin with [Skills Management ERD](ERD_Skills_Management.md) for skills workflow understanding
2. Review [Resume Parsing Architecture](ERD_Resume_Parsing.md) for parsing capabilities
3. Explore [Search and Match Engine](ERD_Search_Match_Engine.md) for search functionality

### For System Administrators
1. Study [Custom Metadata Configuration](ERD_Custom_Metadata.md) for system setup
2. Review [Standard Object Extensions](ERD_Standard_Object_Extensions.md) for field configurations
3. Check [Audit Missing Objects](ERD_Audit_Missing_Objects.md) for implementation completeness

## 🧭 Quick Navigation

### By Functional Area
- **Resume Parsing**: [ERD](ERD_Resume_Parsing.md) | [Main Docs](ASM_Package_Documentation.md#resume-parsing)
- **Skills Management**: [ERD](ERD_Skills_Management.md) | [Main Docs](ASM_Package_Documentation.md#skills-management)
- **Search & Match**: [ERD](ERD_Search_Match_Engine.md) | [Main Docs](ASM_Package_Documentation.md#search-engine)
- **Education History**: [ERD](ERD_Education_History.md) | [Main Docs](ASM_Package_Documentation.md#education-management)
- **Employment History**: [ERD](ERD_Employment_History.md) | [Main Docs](ASM_Package_Documentation.md#employment-management)

### By Component Type
- **Apex Classes**: See [Main Documentation](ASM_Package_Documentation.md#apex-classes)
- **Lightning Web Components**: [ERD](ERD_LWC_Components.md)
- **Custom Objects**: [ERD Index](ERD_Index.md)
- **Triggers**: [ERD](ERD_Trigger_Architecture.md)
- **Integrations**: [ERD](ERD_Parsing_Integration.md)

### Implementation Resources
- **Complete Component List**: [Main Documentation](ASM_Package_Documentation.md)
- **Missing Elements**: [Audit Report](ERD_Audit_Missing_Objects.md)
- **Configuration Guide**: [Custom Metadata ERD](ERD_Custom_Metadata.md)

---

## 🔍 ASM-Specific Features

### Resume Parsing Capabilities
- **Multi-Format Support**: PDF, DOC, DOCX, TXT resume processing
- **Field Extraction**: Contact information, experience, education, skills
- **Language Support**: Multiple language resume parsing
- **Accuracy Validation**: Quality scoring and confidence levels

### Advanced Search Features
- **Boolean Search**: Complex search queries with operators
- **Fuzzy Matching**: Handle spelling variations and synonyms
- **Semantic Search**: Context-aware skills and experience matching
- **Faceted Search**: Multi-dimensional filtering capabilities

### Skills Intelligence
- **Skills Taxonomy**: Hierarchical skills categorization
- **Skills Matching**: Semantic similarity scoring
- **Gap Analysis**: Identify missing skills and competencies
- **Proficiency Levels**: Skill assessment and rating systems

### Lightning Experience
- **Modern UI**: Lightning Web Components for enhanced user experience
- **Responsive Design**: Mobile-friendly interface components
- **Real-time Updates**: Live search and instant feedback
- **Accessibility**: WCAG-compliant components

---

## 📝 Documentation Notes

- All ERD diagrams use Mermaid syntax for consistent visualization
- Apex class documentation includes method signatures and usage examples
- Lightning Web Component specifications include property and method documentation
- Integration guides provide step-by-step configuration instructions
- Cross-references between documents provide complete navigation paths
- Audit documentation ensures implementation completeness

For questions or contributions to this documentation, please refer to the main repository structure and contribution guidelines.

---

**ASM Package Version**: Latest  
**Last Updated**: July 2025  
**Documentation Coverage**: Comprehensive  
**Integration Status**: Daxtra ✓ | RChilli ✓