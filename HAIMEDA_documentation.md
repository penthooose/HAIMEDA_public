# General Architecture of Application:

## Technical Foundation:

**HAIMEDA** is an Elixir Desktop application built on the Phoenix Framework utilizing Phoenix LiveView for real-time, server-rendered interactive interfaces. The application leverages the Desktop library to create native desktop windows using WxWidgets instead of traditional web browser views, providing a native desktop experience while maintaining the development benefits of web technologies.

### Core Architecture Properties:

- **Elixir/OTP Platform**: Built on Erlang/OTP virtual machine providing fault-tolerance, concurrency, and distributed computing capabilities
- **Phoenix Framework**: Web framework providing MVC architecture, routing, and development tooling for robust web applications
- **Phoenix LiveView**: Server-side rendering with WebSocket-based real-time interactivity, eliminating the need for complex JavaScript frontend frameworks
- **Desktop Integration**: WxWidgets-based native desktop windows through the Desktop library, providing OS-native window management and user experience
- **MongoDB Database**: Document-based data persistence for flexible schema management and complex nested data structures
- **Process-Based Architecture**: Actor model concurrency using lightweight Elixir processes for scalable, fault-tolerant operations

### Application Structure:

#### Desktop Window Management:

The application initializes a single main window (1920x1080, maximized) with custom frame handling and native OS integration. Window configuration includes background color management, icon display, and size constraints for optimal user experience across different screen resolutions.

#### LiveView Architecture:

- **Real-time State Management**: Server-side state synchronization with automatic client updates via WebSocket connections
- **Component-Based UI**: Modular LiveView components enabling reusable interface elements with isolated state management
- **Event-Driven Communication**: Comprehensive event handling system supporting both user interactions and inter-component messaging
- **Session Persistence**: Automatic session recovery and state restoration across application restarts

#### Data Persistence Layer:

- **MongoDB Integration**: Document-oriented storage through the HaimedaCore.Repo module providing connection pooling and query optimization
- **Flexible Schema Design**: Schema-less document storage supporting evolving data structures without migration requirements
- **Collection-Based Organization**: Logical data separation using MongoDB collections for reports, deleted reports, and session data
- **Automatic Cleanup**: Background processes for session maintenance and data lifecycle management

#### Process Supervision:

- **Fault-Tolerant Design**: OTP supervisor trees ensuring application resilience with automatic process restart capabilities
- **Dynamic Process Management**: DynamicSupervisor for AI processing tasks enabling scalable concurrent operations
- **Background Task Management**: Periodic cleanup tasks and maintenance operations running independently of user interface
- **Resource Management**: Controlled process lifecycle with proper resource cleanup and memory management

### High-Level Functionality:

#### Report Management System:

The application provides comprehensive medical device assessment report creation and management capabilities. Users can create, edit, and organize reports through an intuitive interface supporting multiple concurrent editing sessions with automatic persistence and version control.

#### AI-Assisted Content Generation:

Integrated artificial intelligence modules provide automated content generation, verification, and optimization capabilities. The system supports local language models through Ollama integration, enabling privacy-preserving AI assistance without external dependencies.

#### Real-Time Collaborative Interface:

The LiveView-based interface enables real-time collaboration features including live chat with AI assistants, status monitoring, and concurrent editing capabilities. WebSocket connections provide instant updates and synchronization across multiple user sessions.

#### Hybrid Information Processing:

The application combines symbolic AI reasoning with machine learning capabilities for comprehensive information verification and content quality assurance. This hybrid approach ensures both logical consistency and semantic accuracy in generated content.

#### Session-Based Workflow:

Persistent session management enables users to maintain context across application restarts, preserving editor states, user preferences, and active content. The session system supports multiple concurrent reports with isolated workspace management.

### Technical Integration Patterns:

#### Module Orchestration:

The MainController serves as the central coordination hub, managing interactions between specialized AI modules (RIM, RAM, IIVM) while providing unified API access and configuration management throughout the application ecosystem.

#### Database Abstraction:

ContentPersistence module provides comprehensive database operations including complex document updates, version management, and data integrity validation while abstracting MongoDB-specific operations from application logic.

#### Configuration Management:

YAML-based configuration system enabling dynamic parameter adjustment for AI models, database connections, and application behavior without requiring code modifications or application restarts.

#### Error Handling and Recovery:

Comprehensive error management with graceful degradation, automatic retry mechanisms, and user-friendly error reporting ensuring application stability and professional user experience.

The architecture prioritizes maintainability, scalability, and user experience while providing sophisticated AI-assisted functionality for medical device assessment report generation in a desktop application environment.

---

# Modules:

## LLM Service Module (LSM):

The LLM Service Module serves as the central API layer for all Language Learning Model interactions within the HAIMEDA application. It abstracts LLM operations and provides a unified interface for other specialized modules (RIM, RAM, IIVM) to interact with various language models through the Ollama platform.

### Core Functionalities:

- **Model Management**: Automatic integration and lifecycle management of GGUF models
- **Request Processing**: Structured message handling with retry mechanisms and timeout management
- **Prompt Engineering**: Template-based prompt construction with variable substitution
- **Token Management**: German text token counting and estimation algorithms
- **Embedding Generation**: Vector embedding creation for RAG operations
- **Context Management**: LLM context reset capabilities

### Model Support:

- Local fine-tuned models (Llama3 German variants, Leo Mistral German)
- Embedding models (Nomic Embed, All-MiniLM, Jina Embeddings v2)
- Automatic model fallback using similarity matching

### Integration Components:

- **Ollama Service** (`ollama_service.ex`): Ollama platform integration and model lifecycle management
- **LLM Service** (`llm_service.ex`): Main service interface for LLM operations
- **Prompt Builder** (`prompt_builder.ex`): Template-based prompt construction and token estimation

---

## Components:

### Ollama Service:

**File**: `ollama_service.ex`

**Purpose**: Manages GGUF model integration and lifecycle within the Ollama platform.

**Key Capabilities**:

- GGUF to Ollama model conversion with Modelfile generation
- Model existence validation and automatic pulling from registry
- Model name sanitization and overwrite management
- Parameter mapping between LLM and Ollama formats

---

### LLM Service:

**File**: `llm_service.ex`

**Purpose**: Primary interface for LLM operations, providing high-level abstractions for model interactions.

**Key Capabilities**:

- Multi-message conversation processing with configurable parameters
- Simple query execution and prompt-based queries using JSON templates
- Vector embedding generation for single texts and batch processing
- Context management through model reload cycles
- Response parsing and error handling with retry mechanisms

**Default Configuration**:

- Temperature: 0.1, Top-p: 0.4, Top-k: 60, Max Tokens: 4096, Repeat Penalty: 1.2
- 3-attempt retry mechanism with 60-second timeouts
- Automatic model fallback using similarity matching

---

### Prompt Builder:

**File**: `prompt_builder.ex`

**Purpose**: Handles prompt template management and token estimation for German language processing.

**Key Capabilities**:

- JSON prompt template extraction and variable substitution
- LangChain integration for template processing
- German-optimized token counting algorithms for various data formats
- System and user message creation for conversation flows

---

## FBM (Feedback Module):

The Feedback Module establishes a communication channel between the surveyor and the AI system, providing real-time status updates and interactive chat functionality. It processes feedback from various AI modules and presents it through structured UI components in the HAIMEDA interface.

### Core Functionalities:

- **Status Communication**: Real-time status updates from AI modules to the surveyor
- **Interactive Chat Interface**: Multi-sender chat system supporting different AI component types
- **Feedback Processing**: Structured processing of verification results and AI feedback
- **Message Formatting**: HTML and text formatting for enhanced readability
- **UI Integration**: Direct communication with Phoenix LiveView components

### Communication Channels:

- **Status Log**: System-wide status messages with timestamps and type classification
- **Live Chat**: Interactive conversation interface with AI assistants
- **Process Notifications**: Real-time updates during AI processing workflows

### Integration Components:

- **Feedback Module** (`feedback_module.ex`): Core feedback processing and message routing
- **Status Log** (`status_log.ex`): Real-time status display component
- **Live Chat** (`live_chat.ex`): Interactive chat interface with multi-sender support

---

## Components:

### Feedback Module:

**File**: `feedback_module.ex`

**Purpose**: Processes and routes feedback from AI modules to appropriate UI components.

**Key Capabilities**:

- Post-processor result formatting with percentage and score calculations
- IIV feedback processing with critical issue detection (red feedback identification)
- Message parsing and formatting for different display contexts (HTML/text)
- Direct LiveView process communication for real-time updates

**Message Types**:

- Status messages for process tracking and error reporting
- Chat messages with sender classification (system, user, symbolic_ai, sub-symbolic_ai, hybrid_ai)
- Loading state management for long-running AI operations

---

### Status Log:

**File**: `status_log.ex`

**Purpose**: Real-time status display component for system-wide process monitoring.

**Key Capabilities**:

- Timestamped status entries with automatic scrolling
- Type-based color coding (success, warning, error, info, ai)
- Phoenix LiveView integration with append-only updates
- Responsive layout with icon indicators

---

### Live Chat:

**File**: `live_chat.ex`

**Purpose**: Interactive chat interface supporting multiple AI component types.

**Key Capabilities**:

- Multi-sender message display with distinct visual styling per AI type
- Real-time message appending with automatic scroll-to-bottom
- Input handling with keyboard shortcuts and form validation
- Responsive message bubbles with timestamp formatting

**Supported Senders**:

- **User**: Human surveyor inputs
- **System**: Application notifications and welcome messages
- **Symbolic AI**: Rule-based AI feedback (IIV components)
- **Sub-symbolic AI**: LLM-generated responses
- **Hybrid AI**: Combined symbolic and sub-symbolic outputs

---

## RAM (Report Authoring Module):

The Report Authoring Module provides AI-assisted content generation for medical device assessment reports. It leverages template-based prompt engineering and specialized German language models to create professional report chapters, optimize text quality, and assist with content revision based on surveyor feedback.

### Core Functionalities:

- **Chapter Generation**: Template-driven creation of report chapters with context-aware content
- **Text Optimization**: Grammar, style, and readability improvements while preserving content integrity
- **Content Revision**: Integration of missing information and feedback into existing text
- **Text Summarization**: Extraction of key points from technical content
- **User Query Processing**: Context-aware responses using RAG-enhanced information retrieval

### Prompt Engineering Features:

- JSON-based prompt template management with variable substitution
- Hierarchical chapter structure processing with parent-child relationships
- Multi-format context integration (vector search results, database records, metadata)
- German language optimization with specialized token estimation
- Response quality verification and retry mechanisms

### Integration Components:

- **RAM Core** (`ram.ex`): Main module interface for LLM-based content generation
- **Prompt Builder** (`prompt_builder.ex`): Template construction and prompt generation
- **Prompt Builder Helpers** (`prompt_builder_helpers.ex`): Utility functions for text processing and formatting

---

## Components:

### RAM Core:

**File**: `ram.ex`

**Purpose**: Main interface for AI-assisted report authoring operations using fine-tuned German language models.

**Key Capabilities**:

- Chapter creation with configurable previous content modes (summaries/full chapters)
- Context-aware user query processing with RAG integration
- Text optimization and revision with quality verification
- Content summarization with structured output formatting
- Response quality validation and automatic retry mechanisms

**Model Configuration**:

- Default model: `llama3_german_instruct_base` optimized for German technical writing
- Conservative parameters: Temperature 0.0, Top-p 0.5, Top-k 40 for consistent outputs
- Automatic response sanitization and quality verification

---

### Prompt Builder:

**File**: `prompt_builder.ex`

**Purpose**: Constructs specialized prompts for different content generation tasks using template-based approaches.

**Key Capabilities**:

- Multi-type prompt creation (chapter_creation, text_optimization, text_revision, text_summarization, user_request)
- Context-aware prompt construction with previous content integration
- Variable substitution using EEx templating engine
- Support for different content modes (summaries vs. full chapters)
- Metadata and party statement integration for chapter generation

**Prompt Types**:

- **Chapter Creation**: With/without previous content, supporting hierarchical chapter structures
- **Text Operations**: Optimization, revision with missing entities, summarization
- **User Requests**: Context-aware (with RAG) and standalone query processing

---

### Prompt Builder Helpers:

**File**: `prompt_builder_helpers.ex`

**Purpose**: Utility functions for text processing, formatting, and template management in German language contexts.

**Key Capabilities**:

- JSON prompt template extraction with error handling
- Context formatting for vector search results and database records
- Hierarchical chapter structure processing with parent-child relationship management
- German text normalization and cleaning (footnotes, images, special tags removal)
- Report ID formatting and metadata extraction
- Token counting optimized for German language characteristics

**Text Processing Features**:

- Chapter hierarchy analysis with direct children and descendant identification
- Previous content formatting with proper closing/opening chapter management
- Special symbol and tag removal for clean text output
- Newline normalization and content sanitization
- Party statement extraction and formatting for metadata integration

---

## IIVM (Information Integrity Verification Module):

The Information Integrity Verification Module provides symbolic AI-based verification and validation of user inputs and LLM outputs to ensure information integrity in medical device assessment reports. It implements a hybrid approach combining pre-processing validation with post-processing verification to maintain high data quality standards.

### Core Functionalities:

- **Pre-Processing Validation**: Multi-stage validation pipeline combining basic condition checking with symbolic AI reasoning using tableaux logic
- **Post-Processing Verification**: Content validation and consistency checking of LLM-generated outputs
- **Rule-Based Logic**: JSON-configurable rulesets with conditional logic for different chapter types
- **Feedback Generation**: Color-coded feedback messages indicating validation results and required actions
- **State Management**: Logical state tracking and condition evaluation using formal verification methods

### Verification Approach:

- **Symbolic Logic**: Tableaux-based theorem proving for logical consistency verification
- **Condition Hierarchies**: Multi-level condition evaluation (core, meta, aggregate, attribute conditions)
- **Chapter-Specific Rules**: Customizable validation rules based on report chapter types
- **Satisfaction Logic**: Boolean satisfaction evaluation with penalty and scoring systems

### Integration Components:

- **Pre-Processor** (`iivm.ex`, `pre_processing/`): Multi-stage validation pipeline combining basic condition checking with symbolic reasoning
- **Post-Processor** (`post_processing/`): Content verification and consistency checking
- **Ruleset Configuration** (`ruleset.json`): JSON-based rule definitions and validation criteria

---

## Pre-Processor (Submodule):

The Pre-Processor submodule implements a multi-stage validation pipeline that combines basic condition checking with symbolic AI reasoning using tableaux logic. It validates user inputs before LLM processing through a hierarchical approach: first evaluating basic conditions, then applying formal logical reasoning to determine overall satisfaction.

### Core Architecture:

- **Condition Evaluation**: Basic programmatic checks for data presence, format validation, and content requirements
- **Tableaux Solver**: Implements tableau-based logical reasoning for complex condition relationships
- **Universe State Management**: Tracks logical states and variable assignments during proof procedures
- **Logic Engine**: Orchestrates condition evaluation and applies symbolic reasoning to determine satisfaction

### Key Capabilities:

- **Multi-Stage Validation**: Basic condition checking followed by formal logical analysis
- **Chapter-Type Awareness**: Different validation rules for general chapters vs. specialized chapters (e.g., "Technische_Daten")
- **Hierarchical Condition Processing**: Core conditions, meta-conditions, aggregate conditions, and attribute conditions
- **Feedback Generation**: Color-coded validation messages (red for critical, orange for warnings, green for success)
- **Formal Verification**: Tableaux-based theorem proving for logical consistency and completeness

### Symbolic AI Reasoning Engine:

The Pre-Processor employs a sophisticated symbolic reasoning system based on formal logic and set theory to validate input conditions and their relationships.

#### Set-Based Universe Model:

The system constructs a logical universe using sets to represent different condition categories and their relationships:

- **Base Sets**: Fundamental collections including `c_all` (all conditions), `c_sat` (satisfied conditions), `c_eval` (evaluated conditions)
- **Attribute Sets**: Specialized collections for condition properties like `score`, `penalty`, `required`
- **Category Sets**: Organized collections per proof dimension (core conditions, meta conditions, etc.)
- **Derived Sets**: Computed collections like `should_sat` (conditions intended to be satisfied)

#### Condition Classification System:

**Condition Types by Evaluation Strategy**:

- **Binary Conditions**: Simple true/false evaluations for basic data presence checks
- **Comparative Conditions**: Numeric comparisons using operators (minimum, maximum, equal, range)
- **Set-Based Conditions**: Subset relationships and set operations for complex dependency validation
- **Aggregate Conditions**: Functions applied to collections (sum, forall, subset operations)

**Condition Intent Classification**:

- **Positive Conditions** (`results_in: "satisfaction"`): Conditions intended to be satisfied for successful validation
- **Negative Conditions** (`results_in: "dissatisfaction"`): Conditions intended to fail for successful validation (e.g., penalty detection)
- **Required vs. Optional**: Mandatory conditions that must pass vs. advisory conditions that affect scoring

#### Tableaux-Based Proof System:

The symbolic reasoning engine implements a formal tableaux solver that constructs logical proofs for condition satisfaction:

**Logical Formula Construction**:

- Converts JSON condition definitions into formal logical expressions
- Applies distributive laws and logical simplification rules
- Handles complex set operations and quantified formulas
- Supports nested implications and conditional relationships

**Proof Procedure**:

1. **Universe Initialization**: Creates base sets from condition definitions and satisfied condition IDs
2. **Formula Generation**: Transforms each condition into a logical formula with proper quantification
3. **Tableau Construction**: Builds proof trees using systematic decomposition of logical formulas
4. **Satisfiability Checking**: Determines truth values through exhaustive logical analysis
5. **State Management**: Updates universe sets with newly proven conditions for downstream evaluation

#### Configurable Condition Framework:

The system supports extensive customization through the `ruleset.json` configuration file, enabling domain-specific validation rules without code modifications.

**Chapter-Type Specialization**:

```json
"general_chapter": {
  "conditions": {
    "core_conditions": { /* basic validation rules */ },
    "meta_conditions": { /* higher-level dependency rules */ },
    "aggregate_conditions": { /* collection-based validation */ }
  }
},
"Technische_Daten": {
  "conditions": { /* specialized technical validation rules */ }
}
```

**Condition Definition Structure**:

- **ID Management**: Unique identifiers for logical reference and dependency tracking
- **Type Classification**: Evaluation strategy (binary, minimum, maximum, equal, range)
- **Requirement Level**: Required vs. optional status affecting validation strictness
- **Attribute Weights**: Scoring and penalty values for quality assessment
- **Evaluation Functions**: sum, subset, forall operations for complex validation logic
- **Target Dimensions**: Specification of condition categories to evaluate

**Advanced Condition Patterns**:

**Meta-Conditions**: Higher-order validation rules that operate on other condition categories:

```json
"all_required_satisfied": {
  "eval_function": "subset",
  "eval_on": "positive_condition",
  "eval_param": "required",
  "eval_dimensions": ["core_conditions"],
  "value": true
}
```

**Aggregate Conditions**: Statistical operations on condition collections:

```json
"minimum_score": {
  "eval_function": "sum",
  "eval_on": "attribute",
  "eval_param": "score",
  "eval_dimensions": ["core_conditions", "meta_conditions"],
  "type": "minimum",
  "value": 15
}
```

#### Triggered Action System:

The Pre-Processor supports configurable actions that execute when specific conditions are satisfied or failed, enabling dynamic response to validation states.

**Action Types**:

- **Feedback Actions**: User notification with color-coded severity levels
- **Functional Actions**: Dynamic function execution with parameter passing
- **State Modifications**: Updates to validation state and condition sets

**Event-Driven Execution**:

- **On Satisfaction** (`on_event: "satisfaction"`): Actions triggered when condition evaluates to true
- **On Dissatisfaction** (`on_event: "dissatisfaction"`): Actions triggered when condition evaluates to false
- **Conditional Logic**: Actions execute only when their trigger condition matches the evaluation result

**Feedback Generation System**:

```json
"triggered_action": {
  "function": "feedback",
  "args": ["Der Kapiteltitel ist noch nicht gesetzt.", "red"],
  "on_event": "dissatisfaction"
}
```

**Dynamic Function Execution**:
The system supports runtime execution of arbitrary functions defined in the configuration:

```json
"functions": {
  "sum": {
    "function": "&Enum.sum/1",
    "input_type": "list"
  },
  "feedback": {
    "function": "IO.puts(&1)",
    "description": "Display feedback message to user"
  }
}
```

#### Evaluation Pipeline:

**Proof Order Processing**: Conditions are evaluated in a specified sequence to ensure proper dependency resolution:

1. **Keywords/Antonyms**: Pattern-based text analysis
2. **Core Conditions**: Fundamental data validation
3. **Meta-Conditions**: Dependency and relationship validation
4. **Aggregate Conditions**: Statistical and collection-based analysis
5. **Attribute Conditions**: Weighted scoring and penalty assessment

**Logical State Progression**:

- **Precondition Checking**: Ensures all dependent conditions are evaluated before processing higher-level conditions
- **Set Membership Determination**: Classifies conditions into positive/negative and satisfied/unsatisfied categories
- **Attribute Extraction**: Collects scoring, penalty, and requirement values for aggregate evaluation
- **Formula Satisfaction**: Applies tableaux solving to determine logical truth values
- **State Updates**: Modifies universe sets with newly established condition states

### Validation Pipeline:

1. **Basic Condition Evaluation**: Programmatic checks for data requirements and format validation
2. **Satisfied Condition Collection**: Gathering IDs of conditions that pass basic evaluation
3. **Logical Formula Construction**: Building formal logical expressions from condition relationships
4. **Tableaux-Based Reasoning**: Applying symbolic AI to determine overall satisfaction and consistency
5. **Feedback Generation**: Producing actionable feedback based on validation results

### Integration Components:

- **IIVM Core** (`iivm.ex`): Main interface coordinating the validation pipeline
- **Condition Prover** (`condition_prover.ex`): Basic condition evaluation and data validation
- **Logic Engine** (`logic.ex`): Symbolic reasoning orchestration and condition relationship analysis
- **Tableaux Solver** (`tableaux_solver.ex`): Core tableaux logic implementation for formal verification
- **Universe State** (`universe_state.ex`): State management for logical variables and proof tracking
- **Tableaux Structure** (`tableaux.ex`): Data structures and operations for tableaux representation

---

## Post-Processor (Submodule):

The Post-Processor submodule implements a hybrid information integrity verification system combining symbolic AI pattern detection with machine learning-based statement verification to ensure generated content accuracy and completeness.

### Core Architecture:

- **Hybrid Verification**: Combines symbolic reasoning for definitive pattern matching with ML-based subsymbolic AI for complex semantic content
- **Multi-Layered Approach**: Symbolic reasoning handles dates, numbers, identifiers, while ML-based AI processes statements and phrases
- **Verification Pipeline**: Comprehensive workflow from entity detection to final output correction

### Key Capabilities:

- **Multi-Mode Processing**: Supports both automatic LLM output verification and manual content validation
- **Content Labeling**: Systematically categorizes input components and outputs
- **Entity Detection Coordination**: Manages symbolic entity construction across all content types
- **Verification Pipeline Management**: Orchestrates the complete verification workflow
- **State Management Integration**: Interfaces with VerificationStateManager for cross-component data sharing

### Integration Components:

- **Hybrid PostProcessor** (`hybrid_postprocessor.ex`): Main orchestrator module for post-processing pipeline
- **Hybrid Verification Engine** (`hybrid_verification_engine.ex`): Entity comparison engine that performs cross-validation between input and output entities, identifies missing and false entities, and generates comprehensive scoring metrics for verification quality assessment.
- **Statement Verification Engine** (`statement_verification_engine.ex`): Hybrid AI component for statement matching and verification
- **Symbolic Entity Constructor** (`symbolic_entity_constructor.ex`): Pattern-based entity detection system
- **Verification State Manager** (`verification_state_manager.ex`): Centralized state management system
- **Output Correction Controller** (`output_correction_controller.ex`): Advanced content correction system
- **PP Helper Functions** (`PP_helper_functions.ex`): Utility library for pattern extraction and text processing
- **Python Integration Components** (`statement_scoring.py`, `statement_worker_pool.py`): Machine learning backend
- **Gateway API Integration** (`gateway_API.ex`): Communication bridge for Elixir-Python integration

---

## Components:

### Hybrid PostProcessor:

**File**: `hybrid_postprocessor.ex`

**Purpose**: Main orchestrator module that coordinates the entire post-processing pipeline for both LLM outputs and manual textarea content verification.

**Key Capabilities**:

- **Multi-Mode Processing**: Supports both automatic LLM output verification and manual content validation
- **Content Labeling**: Systematically categorizes input components (metadata, chapter info, previous content, party statements) and outputs
- **Entity Detection Coordination**: Manages symbolic entity construction across all content types
- **Verification Pipeline Management**: Orchestrates the complete verification workflow from entity detection to final output correction
- **State Management Integration**: Interfaces with VerificationStateManager for cross-component data sharing

**Processing Flow**:

- Input content labeling and filtering
- Entity detection delegation to SymbolicEntityConstructor
- Content combination and state management updates
- Verification engine invocation with configurable parameters

---

### Hybrid Verification Engine:

**File**: `hybrid_verification_engine.ex`

**Purpose**: Entity comparison engine that performs cross-validation between input and output entities, identifies missing and false entities, and generates comprehensive scoring metrics for verification quality assessment.

**Key Capabilities**:

- **Entity Cross-Validation**: Compares input entities against output entities using type-specific matching strategies
- **Missing Entity Detection**: Identifies input entities that are not properly represented in the output content
- **False Entity Detection**: Identifies output entities that lack corresponding support in the input content
- **Multi-Type Entity Processing**: Handles different entity types with appropriate comparison methods (simple containment for dates/numbers/identifiers, regex matching for phrases, ML-based for statements)
- **Scoring System**: Generates both total coverage percentages and weighted content scores based on entity importance
- **Run Management**: Supports both single-run manual verification and multi-run automatic verification with result ranking

**Entity Comparison Methods**:

- **Simple Containment**: Direct string matching for definitive entity types (dates, numbers, identifiers)
- **Regex Matching**: Pattern-based verification for phrase entities using flexible regex patterns
- **Statement Analysis**: Delegates to StatementVerificationEngine for ML-powered semantic similarity comparison
- **Entity Uniqueness Processing**: Removes duplicate entities with overlapping representations to prevent verification conflicts

**Scoring Metrics**:

- **Coverage Percentages**: Input coverage (entities found in output) and output coverage (entities supported by input)
- **Weighted Content Scores**: Importance-weighted scoring with configurable weights per entity type (dates: 0.5, identifiers: 0.5, numbers: 0.4, phrases: 0.2, statements: 0.2)
- **Overall Quality Metrics**: Combined scores providing comprehensive verification quality assessment
- **Run Ranking**: Orders multiple verification runs by weighted scores and coverage percentages for optimal result selection

**Verification Pipeline**:

1. **Entity Filtering**: Separates entities by type for appropriate comparison strategies
2. **Cross-Validation**: Performs bidirectional entity matching (input→output, output→input)
3. **Status Assignment**: Marks entities as :detected or :not_detected based on comparison results
4. **Missing/False Entity Compilation**: Creates structured maps of verification discrepancies
5. **Score Calculation**: Generates coverage percentages and weighted content scores
6. **Result Storage**: Updates VerificationStateManager with all verification results

---

### Statement Verification Engine:

**File**: `statement_verification_engine.ex`

**Purpose**: Hybrid AI component that combines symbolic classification rules with ML-based similarity scoring for sophisticated statement matching and verification.

**Key Capabilities**:

- **Hybrid Classification Matrix**: Rule-based system with ML score thresholds for different match types (exact, strong, moderate, weak, no match)
- **Batch Processing**: Efficient comparison of all input statements against all output statements
- **Python Integration**: Uses GatewayAPI to leverage Python-based ML scoring via ErlPort
- **Retry Logic**: Implements robust error handling with timeout and retry mechanisms
- **Keyword Analysis**: Extracts and analyzes high-similarity keywords from ML results
- **Match Confidence Scoring**: Provides detailed confidence metrics for each classification decision

**Match Classification Levels**:

- **Exact Match**: Perfect semantic alignment with highest confidence
- **Strong Match**: High semantic similarity with minor variations
- **Moderate Match**: Substantial similarity with notable differences
- **Weak Match**: Limited similarity but detectable relationship
- **No Match**: Insufficient similarity for verification purposes

---

### Symbolic Entity Constructor:

**File**: `symbolic_entity_constructor.ex`

**Purpose**: Pattern-based entity detection system that identifies and constructs verifiable symbolic entities from various content types using deterministic pattern matching.

**Key Capabilities**:

- **Multi-Pattern Detection**: Handles dates, numbers, identifiers, phrases, and statements using specialized pattern libraries
- **Content Type Processing**: Adapts detection strategies based on content source (metadata, chapter info, party statements, etc.)
- **Representation Generation**: Creates multiple format variations for each detected entity to improve matching accuracy
- **Location Tracking**: Records source location and context for each detected entity
- **Content Combination**: Aggregates and organizes content for downstream verification processes

**Entity Types**:

- **Dates**: Various formats including ISO, German, English with comprehensive variation generation
- **Numbers**: Financial amounts, quantities, percentages with locale-specific formatting
- **Identifiers**: Alphanumeric codes, references, legal citations, technical identifiers
- **Phrases**: Short contextual expressions with regex pattern generation
- **Statements**: Complex sentences extracted for ML-based verification

---

### Verification State Manager:

**File**: `verification_state_manager.ex`

**Purpose**: Centralized state management system that maintains verification context, entity registries, and run-specific data across the entire verification pipeline.

**Key Capabilities**:

- **Entity Registry Management**: Tracks input and output entities across verification runs
- **Content Storage**: Maintains combined content collections for pattern matching
- **Run State Tracking**: Manages multi-run verification states and scoring data
- **Missing/False Entity Tracking**: Records verification discrepancies for correction processing
- **Score Management**: Stores and updates coverage and weighted content scores
- **Response Storage**: Maintains current response content for output correction

**State Categories**:

- **Entity Registry**: Input/output entities, combined content, entity counts
- **Run Management**: Current run number, maximum runs, run-specific entities
- **Verification Results**: Missing entities, false entities, run scores
- **Response Data**: Current response content for processing

---

### Output Correction Controller:

**File**: `output_correction_controller.ex`

**Purpose**: Advanced content correction system that generates interactive, editable output with entity-level corrections and user-selectable alternatives.

**Key Capabilities**:

- **Entity Classification**: Categorizes entities as replacements, false positives, or requiring alternatives
- **Alternative Generation**: Creates user-selectable alternatives for incorrect or missing entities
- **Interactive Formatting**: Generates rich document format with colored entity highlighting and dropdown selections
- **Replacement Logic**: Implements sophisticated entity replacement strategies based on type and confidence
- **JSON Document Generation**: Creates structured editor-compatible content with embedded correction metadata

**Correction Categories**:

- **Definitive Replacements**: Automatic replacements for high-confidence corrections (dates, numbers, identifiers)
- **Alternative Selections**: User-selectable options for ambiguous corrections
- **Missing Entity Additions**: Interactive selection lists for content not found in output
- **False Entity Highlighting**: Visual indicators for potentially incorrect content

---

### PP Helper Functions:

**File**: `PP_helper_functions.ex`

**Purpose**: Comprehensive utility library providing pattern extraction, text processing, and entity derivation functions supporting the entire verification pipeline.

**Key Capabilities**:

- **Pattern Extraction**: Specialized functions for dates, numbers, identifiers, phrases using extensive regex libraries
- **Statement Processing**: Text segmentation, bracket content extraction, sub-clause detection
- **Derivation Generation**: Creates multiple format variations for improved entity matching
- **Text Normalization**: Handles punctuation, whitespace, and character encoding issues
- **Overlap Resolution**: Prevents pattern conflicts through sophisticated overlap detection algorithms

**Pattern Libraries**:

- **Date Patterns**: 15+ formats covering ISO, German, English with full/abbreviated months
- **Number Patterns**: Financial, scientific, percentage formats with locale variations
- **Identifier Patterns**: Legal references, technical codes, financial identifiers, international standards
- **Phrase Patterns**: Context-aware extraction with placeholder handling

---

### Python Integration Components:

**Files**: `statement_scoring.py`, `statement_worker_pool.py`

**Purpose**: Machine learning backend providing advanced semantic similarity analysis, natural language processing capabilities, and intelligent statement matching optimization via ErlPort integration.

### ML-Based Semantic Analysis Engine:

**Core Technologies**:

- **SentenceTransformer**: Utilizes the `paraphrase-multilingual-MiniLM-L12-v2` model for multilingual embedding generation optimized for German and English technical content
- **Scikit-learn Integration**: Employs TF-IDF vectorization, cosine similarity calculations, and distance metrics for comprehensive similarity assessment
- **spaCy NLP Pipeline**: Advanced linguistic processing for keyword extraction, named entity recognition, and part-of-speech analysis with German language model support

**Multi-Metric Similarity Assessment**:

**1. Embedding-Based Similarities**:

- **Cosine Similarity**: Primary semantic similarity measure using sentence embeddings (0-100 scale)
- **Euclidean Distance**: Geometric similarity converted to percentage scale with distance normalization
- **Manhattan Distance**: L1 norm-based similarity with adaptive scaling for embedding space characteristics

**2. Lexical Similarity Metrics**:

- **TF-IDF Vectorization**: Term frequency-inverse document frequency analysis for lexical overlap detection
- **Keyword Overlap Analysis**: Semantic keyword matching with position-aware scoring and importance weighting
- **Domain-Specific Terminology**: Specialized scoring for technical and insurance-related vocabulary with entropy-based adjustments

**3. Confidence Assessment Framework**:

- **Inter-Metric Agreement**: Variance analysis across multiple similarity measures to assess result reliability
- **Extremity Weighting**: Higher confidence assigned to very high or very low similarity scores
- **Threshold-Based Classification**: Dynamic confidence adjustment based on score proximity to classification boundaries

### Advanced Keyword Processing System:

**spaCy-Enhanced Extraction**:

- **Linguistic Analysis**: Part-of-speech tagging, lemmatization, and dependency parsing for content word identification
- **Named Entity Recognition**: Extraction of persons, organizations, locations, and domain-specific entities
- **Noun Phrase Chunking**: Identification of meaningful multi-word expressions and technical terms
- **Stopword Filtering**: Intelligent removal of function words while preserving domain-specific terminology

**Semantic Keyword Matching**:

- **Embedding-Based Similarity**: Individual keyword comparison using sentence transformer embeddings
- **Threshold-Optimized Matching**: High-confidence semantic matches (>0.85 similarity) for accurate keyword pairing
- **Importance Weighting**: Length-based and frequency-based keyword prioritization for domain relevance
- **Cross-Lingual Support**: Multilingual keyword matching for German-English technical documentation

### Intelligent Worker Pool Architecture:

**Resource-Aware Optimization**:

- **VRAM Detection**: Dynamic GPU memory analysis using nvidia-smi for optimal worker allocation
- **Adaptive Worker Scaling**: Automatic worker count adjustment based on available VRAM (200MB per worker allocation)
- **CPU-GPU Hybrid Processing**: Intelligent fallback to CPU-based processing when GPU resources are limited
- **Memory Management**: Automatic garbage collection and CUDA cache clearing for sustained performance

**Statement Uniqueness Detection**:

- **Pre-Processing Deduplication**: Identifies unique statements across input and output collections to eliminate redundant processing
- **Statement-to-Index Mapping**: Creates efficient lookup structures for O(1) statement access during batch processing
- **Embedding Reuse Optimization**: Generates embeddings once per unique statement rather than per comparison pair
- **Batch Size Optimization**: Dynamic batch sizing (32 statements) for optimal GPU utilization during embedding generation

**Parallel Processing Pipeline**:

**1. Batch Preparation Phase**:

- **Statement Extraction**: Collects all unique statements from input/output pairs with efficient deduplication
- **Embedding Pre-computation**: Single-pass embedding generation for all unique statements using optimized batching
- **Index Structure Creation**: Builds lookup maps for rapid statement-to-embedding retrieval during comparison

**2. Parallel Comparison Execution**:

- **ThreadPoolExecutor Integration**: Configurable worker pool with adaptive thread allocation based on system resources
- **Pre-computed Embedding Distribution**: Each worker receives pre-generated embeddings, eliminating redundant model calls
- **Progress Monitoring**: Real-time progress tracking with completion rate and throughput metrics
- **Error Resilience**: Individual comparison failure handling with graceful degradation and retry mechanisms

**3. Result Aggregation**:

- **Order Preservation**: Maintains original comparison pair ordering through indexed result collection
- **Format Standardization**: Converts Python results to Elixir-compatible data structures with proper type handling
- **Charlist Prevention**: Ensures all string data is properly formatted to prevent Elixir charlist interpretation issues

### Performance Optimization Strategies:

**Memory Efficiency**:

- **Singleton Model Management**: Single model instance across all worker processes to minimize VRAM usage
- **Incremental Garbage Collection**: Periodic memory cleanup during long-running batch operations
- **CUDA Cache Management**: Automatic GPU memory clearing to prevent out-of-memory errors

**Processing Optimization**:

- **Embedding Caching**: Reuses computed embeddings across multiple comparison sessions
- **Batch Processing**: Groups operations to maximize GPU throughput and minimize I/O overhead
- **Adaptive Timeout Management**: Dynamic timeout calculation based on batch size and system performance

**Comparison Complexity Reduction**:

- **Unique Statement Analysis**: Reduces comparison matrix from O(n\*m) to O(unique_statements) for embedding generation
- **Early Termination**: Implements score-based early stopping for obviously mismatched statement pairs
- **Threshold-Based Filtering**: Pre-filters potential matches using lightweight metrics before expensive ML processing

**Integration Robustness**:

- **Connection Pooling**: Maintains persistent Python process connections for reduced startup overhead
- **Retry Logic**: Implements exponential backoff and multiple retry attempts for transient failures
- **Graceful Degradation**: Falls back to sequential processing when parallel execution encounters issues
- **Resource Cleanup**: Automatic process termination and resource release for memory leak prevention

### Error Handling and Monitoring:

**Exception Management**:

- **Granular Error Capture**: Individual comparison error isolation without affecting batch processing
- **Detailed Error Reporting**: Comprehensive error context including statement content and processing stage
- **Fallback Mechanisms**: Multiple fallback strategies from GPU to CPU processing and from parallel to sequential execution

**Performance Monitoring**:

- **Throughput Metrics**: Real-time processing speed measurement (comparisons per second)
- **Resource Utilization**: VRAM usage tracking and optimization recommendations
- **Processing Statistics**: Detailed timing analysis for embedding generation, comparison execution, and result formatting phases

The Python integration represents a sophisticated hybrid AI system that combines the efficiency of intelligent resource management with the accuracy of advanced machine learning techniques, providing scalable and reliable semantic similarity analysis for large-scale statement verification tasks.

---

### Gateway API Integration:

**File**: `gateway_API.ex`

**Purpose**: Communication bridge enabling seamless integration between Elixir-based symbolic processing and Python-based machine learning components.

**Key Capabilities**:

- **ErlPort Management**: Handles Python process lifecycle and communication
- **Data Serialization**: Manages complex data structure exchange between languages
- **Error Recovery**: Implements retry logic and graceful degradation for Python integration failures
- **Performance Monitoring**: Tracks communication latency and success rates

**Verification Configuration**:

The system supports configurable verification strictness levels:

- **Strict**: High thresholds requiring strong semantic similarity
- **Moderate**: Balanced approach with reasonable similarity requirements
- **Lenient**: Lower thresholds allowing broader content variations
- **Custom**: User-defined threshold configurations for specific use cases

The PostProcessor module represents a sophisticated hybrid AI system that combines the precision of symbolic reasoning with the semantic understanding of machine learning to provide comprehensive content verification and correction capabilities.

---

## RIM (Research and Investigation Module):

The Research and Investigation Module (RIM) serves as the core query processing engine of HAIMEDA, utilizing symbolic AI techniques to decompose user queries and retrieve relevant information from structured databases and vector stores.

### Core Functionality:

**Query Decomposition & Analysis:**

- Uses symbolic word processing to break down user questions into constituent components
- Extracts and categorizes entities including dates, IDs, quantities, locations, and party information
- Applies rule-based pattern matching using regex patterns and indicator words from ruleset.json
- Filters and normalizes query fragments to remove noise and irrelevant terms

**Database Search Strategy:**

- Searches through JSON files (converted from former MDB tables) containing structured insurance data
- Implements a two-tier priority system for table column indicators (high/low priority)
- Performs intelligent record joining across multiple tables based on common columns
- Supports various comparison operators (exact match, contains, range queries) for different data types

**Report ID Discovery & Processing:**

- Identifies potential report IDs through multiple mechanisms:
  - Direct extraction from user queries (GA-prefixed identifiers)
  - Cross-referencing through database searches using other query parameters
  - Sanitization and reformatting of report IDs to standardized formats
- When report IDs are found, triggers RAG (Retrieval-Augmented Generation) processing on corresponding report chapters

**Hybrid Information Retrieval:**

- **Structured Data Mode**: Queries only JSON database tables for metadata and structured information
- **Document Mode**: Combines structured database results with vector similarity search on report chapters
- **Content Size Management**: Dynamically adjusts result sets based on configured context limits

### Module Components:

**RIM.SymbolicWordProcessor:**

- **Purpose**: Core NLP component for query analysis and entity extraction
- **Key Functions**:
  - Pattern-based extraction of dates, numbers, identifiers using comprehensive regex patterns
  - Rule-based indicator matching using configurable rulesets
  - Fragment filtering and normalization
  - Column-value mapping between query terms and database schema

**RIM.RequestHandler:**

- **Purpose**: Orchestrates database operations and record processing
- **Key Functions**:
  - Multi-table query execution with priority-based filtering
  - Intelligent record joining using exact and fuzzy matching algorithms
  - Data formatting and normalization for different column types
  - Result size optimization and content management

**RIM.QueryProcessor:**

- **Purpose**: High-level query orchestration and decision making
- **Key Functions**:
  - Request type determination (report vs. meta-info queries)
  - Query priority assignment and execution planning
  - Vector search coordination for document retrieval
  - Result combination and final processing

**RIM.ResourceAgent:**

- **Purpose**: Centralized data access layer for JSON resources
- **Key Functions**:
  - Lazy loading and caching of JSON database files
  - Consistent data access interface across all modules
  - Resource lifecycle management

**RIM.VectorPersistence:**

- **Purpose**: Interface to vector database for semantic search
- **Key Functions**:
  - String embedding using configured models
  - Similarity search in vector collections
  - Report chapter retrieval and ranking

**RIM.VectorMaintenance:**

- **Purpose**: Automated synchronization and maintenance of vector database with file system changes
- **Key Functions**:
  - File system monitoring and change detection through timestamp and size comparison
  - Incremental vector regeneration for modified or new files
  - Orphaned vector cleanup for deleted files
  - Batch processing optimization for large-scale updates
  - Chapter ordering validation and position management for sequential content

**RIM.ResponseGenerator:**

- **Purpose**: Final response formatting and delivery
- **Key Functions**:
  - Result aggregation from multiple sources
  - Response structure standardization
  - Content filtering based on relevance

### Technical Architecture:

**Data Flow:**

1. User query → Symbolic processing → Entity extraction
2. Entity categorization → Database query generation → Priority assignment
3. Parallel execution of high/low priority queries → Result combination
4. Report ID identification → Vector search trigger (if applicable)
5. Result formatting → Size optimization → Response generation

**Configuration Dependencies:**

- **ruleset.json**: Defines extraction rules, indicators, and column mappings for different entity types
- **exclude_words_list.json**: Contains stop words and irrelevant terms to filter from queries
- **Table Schema Definitions**: JSON structure definitions for data type handling and join operations

**Integration Points:**

- **Vector Database**: MongoDB-based vector storage for document embeddings
- **LLM Service**: Token counting and content size estimation
- **Feedback Module**: Progress indication and status updates during processing

---

## MainController:

**File**: `main_controller.ex`

**Purpose**: Central orchestration hub serving as the high-level API that coordinates all core modules (RIM, RAM, IIV) and manages the complete data pipeline between the user interface and underlying AI systems.

**Architecture Role**: Acts as the primary integration layer that enables modular functionality expansion while maintaining consistent communication patterns across the entire HAIMEDA application ecosystem.

### Core Responsibilities:

- **Module Orchestration**: Coordinates interactions between RIM (Research & Information Management), RAM (Reasoning & Answer Management), and IIV (Information Integrity Verification) modules
- **Data Pipeline Management**: Facilitates bidirectional data flow from UI components to core modules using process IDs (PIDs) for asynchronous communication
- **Configuration Management**: Extracts and manages module-specific configurations from YAML files for consistent parameter handling across all AI components
- **Feedback Coordination**: Integrates with FeedbackModule to provide real-time status updates, progress indicators, and chat responses to the live assistant interface

### Key Integration Patterns:

- **Asynchronous Task Management**: Utilizes Elixir's Task module for non-blocking operations, ensuring UI responsiveness during intensive AI processing
- **Process Communication**: Implements PID-based messaging for reliable communication between LiveView processes and background AI operations
- **Modular Function Delegation**: Routes specific operations to appropriate modules while maintaining consistent error handling and response formatting
- **Configuration Abstraction**: Provides unified configuration access for all modules through centralized YAML parsing and parameter extraction

### Primary Workflows:

1. **Chapter Creation Pipeline**: Orchestrates IIV pre-processing, RAM content generation, and IIV post-processing with comprehensive feedback loops
2. **Content Verification**: Manages manual and automatic verification workflows through IIV integration with configurable verification parameters
3. **Text Processing Operations**: Coordinates optimization, revision, and summarization tasks through RAM module integration
4. **Research Query Processing**: Integrates with RIM for contextual information retrieval and response generation using RAG (Retrieval-Augmented Generation) patterns
5. **LLM Integration Management**: Handles local model verification, integration with Ollama, and dynamic model parameter configuration

### Technical Features:

- **Error Propagation**: Implements comprehensive error handling with detailed logging and user-friendly feedback messages
- **Configuration Flexibility**: Supports multiple LLM configurations for different tasks (Chapter Creation, Text Optimization, Text Revision, Text Summarization, RAG operations)
- **Process State Management**: Maintains loading states, button controls, and UI synchronization across multiple concurrent operations
- **Quality Assurance**: Integrates verification workflows to ensure content quality and information integrity before presenting results to users

The MainController serves as the foundational component that enables HAIMEDA's modular architecture, allowing for seamless addition of new AI modules while maintaining consistent communication protocols and user experience patterns.

### Application Properties Configuration:

**File**: `config/application_properties.yaml`

The MainController manages system-wide configuration through a centralized YAML configuration file that defines parameters for all modules and their interactions. This configuration system enables dynamic parameter adjustment without code modifications and ensures consistent behavior across the entire HAIMEDA application.

#### General Configuration:

```yaml
General:
  - verbose_console_output: false # Controls system-wide logging verbosity for development and debugging
```

**Purpose**: Controls global application behavior settings that affect all modules uniformly.

#### LLM Infrastructure Configuration:

**Purpose**: Manages local language model integration and Ollama platform configuration.

- **path_to_local_LLM_models**: File system path containing GGUF model files for automatic integration into Ollama
- **overwrite_existing_ollama_models_with_same_name**: Controls whether to re-integrate models when identical names exist (resource-intensive operation)

#### RAG (Research & Information Module) Configuration:

**Purpose**: Configures the hybrid search infrastructure combining vector databases with symbolic JSON data processing.

- **path_to_parent_folder_for_RAG_files**: Root directory containing report-specific folders for document ingestion
- **vector_subcollections**: Maps database collection names to file patterns within report folders
  - **meta_vectors**: Extracted metadata from reports stored as JSON
  - **report_vectors**: Complete report documents in Markdown format
  - **single_chapter_vectors**: Individual chapter files with wildcard pattern support
- **chunking_subcollections**: Defines text segmentation strategies per collection type
- **enable_tracking_changed_files**: Controls automatic synchronization between file system and vector database
- **embedding_model**: Specifies the language model for vector generation (supports nomic-embed-text, all-minilm, jina-embeddings-v2)
- **maximum_rag_context_characters**: Limits context size for RAG operations with automatic scaling factor (1/3 additional for MDB results)
- **model_name**: Primary LLM for RAG response generation
- **model_params**: LLM parameters optimized for research query processing

#### Task-Specific LLM Configuration:

**Purpose**: Provides specialized LLM configurations optimized for different content generation tasks performed by the RAM module.

- **Text_Optimization**: Higher creativity parameters (temperature: 0.7) for style and readability improvements
- **Text_Revision**: Conservative parameters (temperature: 0.2) with higher repeat penalty for factual accuracy
- **Text_Summarization**: Minimal temperature (0.1) with reduced max_tokens for concise extraction
- **Chapter_Creation**: Uses default RAG configuration for comprehensive content generation

#### Configuration Management Features:

- **Hierarchical Parameter Access**: The MainController navigates nested YAML structures using path-based key access
- **Runtime Parameter Merging**: Default LLM parameters are merged with task-specific configurations at runtime
- **Dynamic Model Selection**: Automatic model fallback using similarity matching when specified models are unavailable
- **Error Handling**: Comprehensive error recovery for missing or malformed configuration sections
- **Type Safety**: Parameter validation and type conversion for numeric values and boolean flags

#### Integration Patterns:

The MainController extracts configuration parameters through specialized helper functions:

- **extract_RAG_config_from_yaml()**: Retrieves complete RAG configuration for RIM module initialization
- **extract_model_params_from_yaml(task)**: Gets task-specific LLM parameters for RAM operations
- **get_application_properties(keys)**: Generic configuration access supporting nested key paths

This configuration architecture enables the MainController to provide consistent parameter management across all HAIMEDA modules while supporting flexible deployment scenarios and runtime optimization adjustments.

## Other Components Used by Multiple Modules:

### Gateway API:

**File**: `gateway_API.ex`

**Purpose**: General-purpose communication bridge providing seamless integration between Elixir-based modules and Python-based machine learning components across the entire HAIMEDA application.

**Key Capabilities**:

- **Universal Python Integration**: Standardized interface for all modules requiring Python ML functionality
- **ErlPort Process Management**: Handles Python process lifecycle, spawning, and termination
- **Multi-Module Support**: Serves LSM (embeddings), PostProcessor (statement verification), RIM (research analysis), and other modules requiring ML capabilities
- **Data Serialization**: Robust handling of complex data structure exchange between Elixir and Python
- **Connection Pooling**: Efficient resource management for multiple concurrent Python operations
- **Error Recovery**: Comprehensive retry logic and graceful degradation for Python integration failures
- **Performance Monitoring**: Tracks communication latency, success rates, and resource utilization

## Editor Session:

**File**: `editor_session.ex`

**Purpose**: Persistent session management system providing user-specific configuration storage, editor state preservation, and cross-session continuity for the HAIMEDA report editing environment.

**Architecture Role**: Acts as the persistence layer between the GUI components and MongoDB, ensuring user preferences, editor states, and session data survive browser refreshes and application restarts.

### Core Responsibilities:

- **Session Persistence**: Manages complete editor sessions including tab states, active content, and user interface configurations with automatic save/restore functionality
- **User Preference Management**: Stores and retrieves user-specific settings including LLM parameters, verification configurations, and content display preferences
- **State Synchronization**: Maintains consistency between in-memory editor state and persistent MongoDB storage with real-time updates
- **Configuration Validation**: Ensures all session data meets required schemas with default value provisioning and data integrity checks

### Key Features:

- **Multi-User Session Isolation**: Report-specific session storage preventing cross-contamination of user settings and editor states
- **Automatic Session Recovery**: Restores complete editor environments including open tabs, chat histories, and active configurations upon application restart
- **Performance Optimization**: Implements intelligent data trimming for chat messages and logs while preserving essential session continuity
- **Configuration Defaults**: Provides comprehensive fallback systems for missing or corrupted session data with sensible default values
- **LLM Parameter Management**: Specialized handling of language model configurations with validation ranges and type conversion
- **Verification Settings**: Manages AI verification thresholds and matching criteria with display value to internal value mapping

### Session Data Structure:

- **Tab Management**: Preserves tab metadata, content references, and user interface states without storing large content payloads for performance
- **Chat History**: Maintains conversation context with automatic truncation policies and timestamp preservation
- **Activity Logs**: Stores user actions and system events with categorization and retention management
- **Model Configuration**: Handles LLM parameters, selected models, and initialization states with validation and default provisioning
- **Content Preferences**: Manages previous content modes, verification degrees, and display preferences with backward compatibility

### Integration Patterns:

- **MongoDB Storage**: Direct integration with HaimedaCore.Repo for document-based session persistence with automatic schema evolution
- **LiveView Coordination**: Seamless integration with Phoenix LiveView for real-time state synchronization and event-driven updates
- **ContentPersistence Integration**: Coordinates with content management systems for tab loading and state restoration
- **MainController Communication**: Provides session context for AI operations and configuration parameter extraction

The EditorSession module enables HAIMEDA to provide a consistent, personalized editing experience across sessions while maintaining performance through intelligent data management and storage optimization.

## Previous Content:

**File**: `previous_content.ex`

**Purpose**: Contextual content structuring system that organizes and formats previous chapter content to provide AI models with structured context for coherent report generation and chapter continuity.

**Architecture Role**: Serves as the intelligent content preprocessing layer that transforms stored chapter data into hierarchically organized context suitable for AI-driven content generation while maintaining narrative consistency.

### Core Responsibilities:

- **Chapter Hierarchy Analysis**: Automatically determines chapter relationships, parent-child structures, and content dependencies based on chapter numbering systems and content organization
- **Content Mode Management**: Provides flexible content delivery supporting both summary-based and full-content modes for different AI processing requirements
- **Summary Generation Coordination**: Integrates with MainController to generate chapter summaries on-demand for content that lacks existing summaries
- **Context Optimization**: Structures previous chapter content to maximize AI model comprehension while respecting context length limitations

### Key Capabilities:

- **Intelligent Chapter Sorting**: Implements sophisticated chapter number parsing and comparison algorithms supporting multi-level numbering systems (e.g., 1.2.3, 5.1, etc.)
- **Hierarchical Content Classification**: Automatically categorizes chapters as main chapters, sub-chapters, technical sections, or heading-only entries based on content analysis and structural positioning
- **Dynamic Summary Management**: Monitors chapter content for summary availability and automatically generates missing summaries for regular chapters using AI integration
- **Content Type Recognition**: Distinguishes between different chapter types (technical data, regular content, heading-only) to apply appropriate content processing strategies

### Content Processing Features:

- **Position-Based Retrieval**: Utilizes chapter positioning rather than just numbering to ensure correct sequential context for AI processing
- **Selective Content Inclusion**: Filters out heading-only chapters from context while preserving structural hierarchy information
- **Content Length Optimization**: Manages context size by providing appropriate content depth based on processing requirements and model limitations
- **Version-Aware Processing**: Handles multiple chapter versions by selecting current versions and managing version-specific content and summaries

### Chapter Type Management:

- **Regular Chapters**: Full content processing with automatic summary generation when needed
- **Technical Chapters**: Complete content inclusion without summarization to preserve technical accuracy
- **Heading-Only Chapters**: Excluded from content context while maintaining structural hierarchy information
- **Main Chapter Classification**: Identifies opening, closing, and transitional chapter relationships for proper context flow

### AI Integration Workflows:

- **Context Preparation**: Formats previous chapter content into structured data suitable for AI model consumption with proper hierarchy indicators
- **Summary Creation Pipeline**: Coordinates with MainController for automatic summary generation while managing database persistence of generated summaries
- **Content Validation**: Ensures all provided context meets quality standards and maintains consistency with overall report structure
- **Memory Management**: Optimizes content delivery to balance comprehensive context with AI model processing constraints

The PreviousContent module enables HAIMEDA to provide AI models with intelligently structured context from previous chapters, ensuring generated content maintains narrative consistency and logical flow throughout the entire report generation process.

## Content Persistence:

**File**: `content_persistence.ex`

**Purpose**: Comprehensive database abstraction layer managing all content storage, retrieval, and synchronization operations between the editor interface and MongoDB, ensuring data integrity and content versioning across the HAIMEDA application.

**Architecture Role**: Functions as the primary data access layer coordinating between LiveView components and MongoDB storage, providing unified content management with support for complex document structures and real-time synchronization.

### Core Responsibilities:

- **Multi-Category Content Management**: Handles storage and retrieval for general sections, chapter content, and party statements with category-specific data validation and structure management
- **Version Control Operations**: Manages chapter versioning systems including version creation, deletion, and current version tracking with content synchronization
- **Rich Content Processing**: Processes both plain text and TipTap formatted content with structure validation, content verification, and format conversion capabilities
- **Database Transaction Coordination**: Ensures atomic operations across complex content updates with proper error handling and rollback mechanisms

### Content Type Specialization:

- **Chapter Management**: Comprehensive chapter lifecycle including content versioning, metadata handling, summary management, and chapter ordering with position-based organization
- **Party Data Handling**: Specialized processing for person statements and analysis statements with ID-based relationship management and JSON structure validation
- **General Section Processing**: Key-value pair management for report metadata with flexible data structure support and validation
- **Metadata Operations**: Active metadata information management with hierarchical organization and real-time updates

### Advanced Features:

- **Formatted Content Processing**: Sophisticated TipTap document structure handling including entity highlighting, correction elements detection, and content sanitization
- **Chapter Ordering Systems**: Intelligent chapter position calculation based on hierarchical numbering with automatic reordering and position updates
- **Content Type Classification**: Automatic chapter type determination using IIV integration for technical vs. regular content categorization
- **Summary Integration**: Automated summary creation workflow with MainController coordination and persistent storage management

### Data Structure Management:

- **Content Versioning**: Complete version lifecycle management including creation, modification, deletion, and version switching with content preservation
- **Formatted Content Validation**: Ensures TipTap document integrity with structure repair, node validation, and content reconstruction capabilities
- **Legacy Data Compatibility**: Maintains backward compatibility with older content structures while migrating to current data formats
- **Selection List Processing**: Specialized handling of correction elements including selection lists, replacements, and alternatives for content verification workflows

### Database Integration:

- **MongoDB Optimization**: Efficient document operations with strategic field updates, bulk operations, and query optimization for large content collections
- **Transaction Management**: Proper error handling, retry logic, and data consistency maintenance across complex multi-document operations
- **Content Synchronization**: Real-time content updates between editor interface and persistent storage with conflict resolution
- **Performance Tuning**: Intelligent content loading, selective field updates, and optimized query patterns for responsive user experience

### Quality Assurance Features:

- **Content Verification**: Integrated validation workflows ensuring content integrity, format compliance, and data structure consistency
- **Error Recovery**: Comprehensive error handling with graceful degradation, data recovery options, and user-friendly error reporting
- **Content Migration**: Automated migration systems for evolving data structures with backward compatibility and data preservation
- **Correction Mode Detection**: Advanced analysis of content structures to identify and manage AI-generated corrections and suggestions

The ContentPersistence module serves as the foundational data management system enabling HAIMEDA's complex content workflows while maintaining data integrity, supporting real-time collaboration, and providing the reliability required for professional report generation environments.

---

# GUI:

**Purpose**: Phoenix LiveView-based graphical user interface providing real-time, interactive report editing capabilities with integrated AI assistance and collaborative features across the entire HAIMEDA application.

**Architecture**: Multi-component LiveView system centered around a core Editor module that orchestrates all GUI operations, content management, and AI interactions.

**Key Components**:

- **Editor (Core Controller)**: Primary LiveView module managing the entire editing session, coordinating between all GUI components, handling AI integration via MainController, and maintaining real-time state synchronization
- **Tab Management**: Dynamic tab system supporting multiple document sections with lazy loading, session persistence, and content versioning
- **Content Persistence**: Database integration layer handling automatic saves, version control, and content recovery with structured data management
- **Metadata Section**: Interactive metadata editing with key-value pair management, collapsible sections, and real-time validation
- **Parties Section**: Specialized interface for managing person statements and analysis data with dynamic relationship linking
- **Status Log**: Real-time logging system displaying AI operations, system events, and user actions with categorized message types
- **Live Chat**: Bidirectional communication interface with AI assistants supporting chat history, multiple AI agent types, and contextual responses
- **TipTap Editor Integration**: Rich text editing with entity highlighting, version comparison, and collaborative editing features
- **Output Area**: Content display and manipulation component supporting multiple content versions, AI-generated corrections, and interactive editing modes

**Core Capabilities**:

- **Real-time Collaboration**: WebSocket-based live updates enabling simultaneous multi-user editing with conflict resolution
- **AI Integration Framework**: Seamless integration with multiple AI agents (Symbolic AI, Hybrid AI, LLM) through standardized communication protocols
- **Version Control System**: Comprehensive content versioning with comparison tools, rollback capabilities, and automated backup creation
- **Dynamic Content Management**: Intelligent content loading, caching, and persistence with support for complex document structures
- **Interactive AI Assistance**: Context-aware AI suggestions, automated content generation, and real-time verification with user feedback loops
- **Session Management**: Persistent editor sessions with automatic recovery, state synchronization, and cross-browser compatibility
- **Component Communication**: Event-driven architecture enabling loose coupling between components with message passing and state coordination
- **Content Transformation**: Multi-format content handling supporting plain text, formatted content, and structured data with seamless conversion
- **Real-time Validation**: Live content validation, entity recognition, and consistency checking with immediate user feedback

**Technical Features**:

- **Phoenix LiveView Architecture**: Server-side rendering with client-side interactivity, minimizing JavaScript complexity while maintaining responsive UI
- **Event-Driven Communication**: Comprehensive event handling system supporting inter-component messaging, AI integration events, and user interaction processing
- **Database Integration**: Direct database connectivity with optimized queries, transaction management, and data integrity validation
- **Error Recovery**: Robust error handling with graceful degradation, automatic retry mechanisms, and user-friendly error reporting
- **Performance Optimization**: Efficient state management, selective re-rendering, and optimized database operations for responsive user experience
- **Security Integration**: Session-based authentication, input validation, and secure data handling throughout the interface
- **Responsive Design**: Adaptive layout supporting multiple screen sizes and device types with consistent user experience
- **Accessibility Compliance**: WCAG-compliant interface design with keyboard navigation, screen reader support, and high contrast modes

---

## TipTap Editor:

**Files**: `tiptap_editor.ex`, `tiptap_actions.ex`, `tiptap_snippets.ex`, `output_area.ex`, plus JavaScript components (`tip_tap_core.js`, `tip_tap_editor.js`, `tip_tap_entities.js`, `tip_tap_utils.js`, `selection_list.js`)

**Purpose**: Advanced rich text editing system providing real-time content manipulation, entity highlighting, and AI-generated correction management for HAIMEDA's report editing environment with seamless integration between Elixir backend and JavaScript frontend.

**Architecture Role**: Serves as the primary content editing interface bridging the gap between raw text input and structured document output, enabling collaborative editing with AI assistance while maintaining content integrity and entity relationships.

### Core Components:

#### TipTap Editor (Core Component)

- **Content Processing**: Handles bidirectional transformation between plain text and rich formatted content with entity preservation and structural validation
- **Entity Management**: Manages colored entity highlighting, replacement suggestions, and deletion states with real-time visual feedback
- **Format Conversion**: Transforms between TipTap JSON format and plain text while preserving entity information and document structure
- **Content Persistence**: Coordinates with ContentPersistence for automatic content saving and version management

#### TipTap Actions (Event Handling)

- **Entity State Management**: Processes entity deletion, replacement, and restoration events with immediate database persistence
- **Selection List Integration**: Handles complex selection list entities with state synchronization between different entity types
- **Content Synchronization**: Manages real-time content updates between editor instances and database storage
- **Event Coordination**: Routes entity manipulation events between JavaScript frontend and Elixir backend components

#### TipTap Snippets (Utility Functions)

- **Entity Creation**: Provides structured entity creation with proper TipTap mark formatting and replacement option management
- **Document Structure**: Creates complete TipTap documents with entities integrated at specified positions
- **Entity Extraction**: Extracts entity information from formatted content for processing and analysis
- **Content Formatting**: Handles entity positioning, color management, and replacement list formatting

#### Output Area (Content Display)

- **Version Management**: Handles multiple content versions with navigation, comparison, and selection capabilities
- **Content Modes**: Supports different display modes including correction mode, read-only mode, and editing mode
- **AI Integration**: Processes AI-generated content including corrections, optimizations, and new chapter creation
- **Database Coordination**: Manages content persistence with proper version tracking and chapter organization

### JavaScript Integration:

#### Core Editor Functionality

- **TipTap Framework**: Utilizes TipTap rich text editor with custom extensions for entity handling and selection lists
- **Real-time Updates**: Implements WebSocket-based live updates with conflict resolution and content synchronization
- **Entity Rendering**: Custom JavaScript extensions for rendering colored entities with interactive replacement options
- **HardBreak Preservation**: Advanced handling of line breaks and whitespace to maintain document formatting integrity

#### Entity Management

- **Interactive Highlighting**: Provides visual entity highlighting with color coding, deletion states, and confirmation status
- **Replacement Workflows**: Implements click-to-replace functionality with dropdown selection and text input options
- **Selection Lists**: Specialized handling of entity groups with batch operations and state management
- **Visual Feedback**: Real-time visual indicators for entity states including deleted, confirmed, and pending entities

#### Communication Layer

- **Event Broadcasting**: Bidirectional event communication between JavaScript editor and Elixir LiveView processes
- **Content Serialization**: Efficient JSON-based content exchange with proper entity metadata preservation
- **Error Recovery**: Robust error handling with graceful degradation and automatic retry mechanisms
- **Performance Optimization**: Optimized rendering with selective updates and efficient DOM manipulation

### Content Processing Workflows:

#### Entity Lifecycle Management

- **Entity Creation**: Automated entity detection from AI post-processing with proper mark attribution and color assignment
- **State Transitions**: Manages entity states (active, deleted, confirmed) with visual feedback and persistence
- **Replacement Processing**: Handles entity replacement with original text preservation and alternative suggestion management
- **Batch Operations**: Supports bulk entity operations for selection lists and grouped corrections

#### Content Transformation

- **Format Conversion**: Seamless conversion between plain text, TipTap JSON, and MongoDB storage formats
- **Entity Preservation**: Maintains entity information across format transformations and content updates
- **Whitespace Handling**: Intelligent whitespace management around entities to ensure proper rendering
- **Structure Validation**: Validates document structure and repairs inconsistencies in entity placement

#### AI Correction Integration

- **Post-Processor Output**: Receives structured correction data from IIV module post-processing workflows
- **Visual Correction Display**: Renders AI suggestions as interactive entities with replacement options
- **Correction Workflows**: Implements approval/rejection workflows for AI-generated corrections with batch processing
- **Quality Assurance**: Integrates verification status and confidence indicators for AI-generated content

### Advanced Features:

#### Collaborative Editing

- **Real-time Synchronization**: Multi-user editing support with conflict resolution and operational transformation
- **Content Locking**: Prevents concurrent editing conflicts with user-aware locking mechanisms
- **Change Tracking**: Comprehensive change history with author attribution and timestamp tracking
- **Version Control**: Integrated version management with branching, merging, and rollback capabilities

#### Content Verification

- **Entity Validation**: Validates entity consistency and relationships across document sections
- **Content Integrity**: Ensures document structure integrity during editing operations
- **Error Detection**: Identifies and reports structural inconsistencies and entity conflicts
- **Quality Metrics**: Provides content quality indicators and suggestion compliance tracking

#### Performance Optimization

- **Lazy Loading**: Efficient content loading with progressive enhancement for large documents
- **Selective Rendering**: Optimized DOM updates with change detection and minimal re-rendering
- **Memory Management**: Intelligent memory usage with content cleanup and garbage collection
- **Caching Strategies**: Strategic content caching with invalidation policies for optimal performance

### Integration Patterns:

#### Database Layer

- **ContentPersistence Coordination**: Direct integration with MongoDB through ContentPersistence module for content storage
- **Session Management**: Coordinates with EditorSession for user preference and state persistence
- **Version Tracking**: Implements comprehensive version control with chapter-level versioning support

#### AI Module Integration

- **IIV Post-Processing**: Receives and processes output from Information Integrity Verification module corrections
- **MainController Communication**: Coordinates with MainController for AI task orchestration and status reporting
- **Feedback Integration**: Provides real-time feedback through FeedbackModule for user status updates

#### LiveView Ecosystem

- **Component Architecture**: Implements Phoenix LiveView component patterns with proper state management
- **Event Handling**: Comprehensive event system supporting both JavaScript and Elixir event processing
- **Real-time Updates**: WebSocket-based live updates with automatic reconnection and state recovery

The TipTap Editor represents the culmination of HAIMEDA's user interface design, providing a sophisticated editing environment that seamlessly integrates AI assistance with human oversight, enabling efficient and accurate report creation with comprehensive entity management and content verification capabilities.

---
