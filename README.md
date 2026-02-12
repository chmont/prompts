# HDMP Platform Architecture Overview

The HDMP (Healthcare Data Management Platform) is built on the Coperor platform, which is a comprehensive master data management solution specifically designed for healthcare and life sciences industries. Here's the architectural breakdown:

## Main Components and Services

### Core Web Services
The platform operates as a collection of interconnected web services :

**Core Services**
- Primary identity management and data stewarding platform 
- Operates on the Identity Model
- Provides token-based matching services and data stewarding console
- Enables human verification of suspect matches before completing merges

**Orchestrator**
- Primary point of entry for data into Coperor 
- Operates against the Transactional/Relational Model
- Leverages Core Services' Identity Management (MDM) capabilities
- Masters relationships across related domains

**Supporting Services** :
- **Identity Management**: Controls user authentication and authorization
- **Data Health**: Monitors data quality and integrity
- **Integration Hub**: Facilitates connections with external systems
- **Service Bus**: Enables reliable communication between platform components
- **Data Replication**: Manages data synchronization and distribution
- **Task Manager**: Coordinates background processes and scheduled operations
- **Console**: Provides the user interface for system monitoring and management
- **Configuration & Security**: Maintains system settings and security policies

## Platform Components (Latest v8.2)
The current platform includes :
- Core Services
- Console
- Orchestrator
- Auditing Service
- Gaine Service Bus
- Profile Replicator
- Integration Hub
- Gaine Task Manager

## Data Models and Architecture

### Dual Model System
The platform uses two distinct but related data models :

**Identity Model** (Core Services)
- Defines data attributes available for matching
- Used for supplemental information in Data Stewarding
- Fully configurable and expandable

**Transactional/Relational Model** (Orchestrator) 
- Includes all attributes from Identity Model plus relationship attributes
- Supports domains that don't need identity management
- Enables foreign key relationships between entities

## Communication and Data Flow

### API Architecture
Coperor's Data API comprises two distinct web services :
- **Orchestrator API**: Handles data ingestion
- **Core Services API**: Manages identity resolution and matching
- **Gaine Service Bus**: Publishes data events via pub/sub pattern

### Data Processing Workflow
1. **Data Ingestion**: Orchestrator serves as the primary entry point 
2. **Identity Resolution**: Core Services processes matching and identity management 
3. **Data Orchestration**: Orchestrator coordinates data processing workflows
4. **Event Distribution**: Service Bus publishes data change events to subscribers

### Inter-Service Communication
- Orchestrator interacts with Core Services for identity management operations
- XML entities are built and submitted to Core Services via web API requests
- Results are processed to identify contributor entities for merging
- All services communicate through the Gaine Service Bus for event distribution

## User Workflows

### Typical Data Workflows
1. **Data Landing**: Raw data is loaded into landing tables
2. **Transformation**: Data is mapped to the appropriate model structure
3. **Processing**: Orchestrator processes profiles through configured pipelines
4. **Identity Resolution**: Core Services performs matching and identity management
5. **Distribution**: Processed data is replicated and distributed as configured
6. **Monitoring**: Console provides interface for system health and data quality monitoring

### Management Interfaces
- **Console**: Web-based interface for system monitoring and management
- **Data Stewarding Console**: Human verification interface for suspect matches
- **Gaine CLI**: Command-line interface for configuration management
- **APIs**: RESTful web services for programmatic integration

## Data Replication Models
The platform supports two replication approaches :
- **Document Model**: For flexible, schema-less data structures
- **Relational Model**: For traditional relational data patterns

This architecture provides a robust, scalable solution for managing complex healthcare data with strong identity management capabilities and flexible integration options.

