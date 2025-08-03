# MultiSystemCodeBase
Multiple System Support Code Base

# TAM Component/Block Diagram for EXA Service Cross Charging Extraction Flow

## Diagram Overview
This Technical Architecture Modeling (TAM) Component/Block Diagram documents the extraction flow in the EXA Service Cross Charging solution, following SAP's standardized modeling approach that combines FMC (Fundamental Modeling Concepts) with UML notation.

## System Architecture Components

### Source System Agents (Active Elements)
- **User** (Human Agent) - Initiates the extraction process
- **/ESRCC/EXTRACTOR** - Report program that serves as entry point
- **/ESRCC/EXTRACT** - Class instance that orchestrates extraction logic
- **/OTPEXT/EXTRACT_DATA** - RFC function module wrapper
- **/OTPEXT/EXTRACT** - Background program for data extraction
- **/OTPEXT/CL_EXTRACTION** - Abstract class reference for API instantiation
- **API Class Instance** - Concrete implementation for data extraction operations

### Host System Agents (Active Elements)
- **/ESRCC/WRITE_DATA_IN_HOST** - RFC function module for host data processing
- **/ESRCC/HOST_DATA_WRITER** - Background program for data persistence
- **/ESRCC/WRITE_DATA_TO_DB** - Abstract class reference for database operations
- **API Class Instance** - Concrete implementation for data persistence operations

### Storages (Passive Data Elements)

#### Source System Storages
- **Logger Object Storage** - Maintains extraction process logs
- **Extraction Metadata Storage** - Stores field lists, where clauses, join information
- **Source Database Tables Storage** - Contains source data for extraction
- **Extracted Data Storage (Batches)** - Temporary storage for cursor-based extraction
- **Enriched Data Storage** - Processed data ready for transfer

#### Host System Storages
- **Log Object Storage (Host)** - Host-side logging information
- **Encoded Data String Storage** - Compressed/encoded data from source
- **Table Metadata Storage** - Schema and structure information
- **Decoded Structured Data Storage** - Decompressed data in structured format
- **Data Model Storage** - Business logic-enriched data model
- **Host Database Tables Storage** - Final persistent storage

### Communication Channels
- **User â†’ Report Program Channel** - User interaction interface
- **Report Program â†’ Class Instance Channel** - Program instantiation
- **Class Instance â†’ RFC Function Channel** - Method invocation
- **RFC Function â†’ Background Program Channel** - Job scheduling
- **Background Program â†’ API Class Channel** - API instantiation
- **Source System â†’ Host System Channel (RFC)** - Inter-system communication
- **Host RFC Function â†’ Background Program Channel** - Host-side job scheduling
- **Host Background Program â†’ API Class Channel** - Host-side API instantiation

### Access Relationships

#### Source System Accesses
- **/ESRCC/EXTRACT** â†’ **Logger Object** (Write Access)
- **API Class Instance** â†’ **Source DB Tables** (Read Access)
- **Background Program** â†’ **Extracted Data** (Write Access)
- **API Class Instance** â†’ **Enriched Data** (Modify Access)

#### Host System Accesses
- **API Class Instance** â†’ **Log Object** (Write Access)
- **API Class Instance** â†’ **Encoded Data** (Read Access)
- **API Class Instance** â†’ **Data Model** (Modify Access)
- **API Class Instance** â†’ **Host DB Tables** (Write Access)

## Process Flow Description

### Phase 1: Extraction Initiation
1. User executes report program /ESRCC/EXTRACTOR
2. Report program instantiates /ESRCC/EXTRACT class
3. Extract method is triggered to begin processing

### Phase 2: Source System Processing
4. Logger object is instantiated for process tracking
5. Relevant extraction information is fetched and logged
6. RFC function module /OTPEXT/EXTRACT_DATA is invoked
7. Background job is scheduled to run program /OTPEXT/EXTRACT

### Phase 3: Data Extraction
8. API class instance is created using /OTPEXT/CL_EXTRACTION reference
9. Execute method builds extraction artifacts (field lists, where clauses, joins)
10. Cursor is opened to extract data in batches from source tables
11. Extracted data is enriched for transfer

### Phase 4: Host System Transfer
12. RFC function module /ESRCC/WRITE_DATA_IN_HOST is triggered
13. Encoded data string and metadata are transferred to host system
14. Background job is scheduled to run program /ESRCC/HOST_DATA_WRITER

### Phase 5: Host System Processing
15. API class instance is created using /ESRCC/WRITE_DATA_TO_DB reference
16. WRITE_TO_DB method is called for data persistence
17. Log object is instantiated using reference information
18. Messages from source side are logged

### Phase 6: Data Persistence
19. Data is decoded using metadata information
20. Data model is instantiated on host side
21. Data is moved from reference structure to data model
22. Data model is enriched with business logic
23. Final data model is persisted to database tables

## System Boundaries
- **Source System Boundary** - Encapsulates all source-side processing components
- **Host System Boundary** - Contains all host-side processing and persistence components
- **RFC Communication Boundary** - Defines the inter-system communication protocol

## Implementation Notes
- Process operates in packet-based mode for performance optimization
- Cursor-based extraction enables handling of large data volumes
- Background job processing ensures non-blocking operations
- Logging is maintained throughout both source and host processing
- Abstract class references enable polymorphic implementations
- RFC wrappers provide standardized inter-system communication

## TAM Compliance
This diagram follows SAP's Technical Architecture Modeling standards:
- Uses conceptual level abstraction for high-level system overview
- Employs standard TAM notation for agents, storages, channels, and accesses
- Maintains clear separation between active and passive elements
- Documents system boundaries and communication protocols
- Supports both structural and behavioral architecture perspectives
