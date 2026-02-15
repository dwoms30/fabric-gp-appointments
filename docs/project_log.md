project_log:

  - date: "2026-01-12"
    phase: "Lakehouse Foundation (Bronze Setup)"
    environment:
      - Created Microsoft Fabric Lakehouse with schemas enabled
      - Built within existing workspace due to tenant restrictions
      - Confirmed OneLake-backed storage
      - Noted schemas are implicitly created on first table write
    bronze_structure:
      - Created Bronze folder structure:
          - Files/bronze/appointments
          - Files/bronze/reference
      - Established layered architecture:
          Bronze: raw fidelity
          Silver: typed and validated
          Gold: dimensional model

  - date: "2026-01-13"
    phase: "Bronze Ingestion & Platform Constraints"
    synthetic_dataset:
      - Generated synthetic EMIS-style appointment dataset
      - Included EMIS Number, National Practice Code, Slot Type, Current Slot Status
      - Simulated realistic primary care extract structure
    delta_constraints:
      - Identified Delta Lake restriction on spaces/special characters in column names
      - Applied minimal column standardisation at Bronze ingestion
      - Preserved original semantics
    validation:
      - All Bronze columns stored as STRING
      - Row counts matched source CSV
    reference_ingestion:
      - Dataflow Gen2 blocked due to tenant licensing/identity restrictions
      - Implemented Spark-based ingestion instead
      - Loaded output to bronze.practice_reference_raw
    platform_observations:
      - Spark-created tables not immediately visible in SQL endpoint
      - Validated via Spark SHOW TABLES and Lakehouse UI
      - Documented propagation delay behaviour

  - date: "2026-01-23"
    phase: "Silver Layer Implementation"
    schema:
      - Created explicit silver schema
    appointments_transformation:
      - Cast age to INT
      - Parsed appointment_date to DATE
      - Standardised gender values
      - Enforced null rules
      - Standardised appointment statuses
    reference_transformation:
      - Applied deduplication
      - Enforced null constraints
      - Ensured join-readiness
    validation:
      - Null checks performed
      - Value constraints validated
      - Confirmed SQL endpoint visibility

  - date: "2026-02-08"
    phase: "Silver Hardening"
    improvements:
      - Confirmed Silver tables are dimension-ready
      - Standardised naming conventions
      - Cleaned transformation notebooks
      - Revalidated joins and constraints

  - date: "2026-02-15"
    phase: "Week 3 – Gold Layer Engineering & Incremental Pipeline"
    objective: "Implement production-style incremental Lakehouse architecture"

    gold_model:
      - Created gold schema
      - Designed star schema:
          - gold.dim_practice
          - gold.dim_date
          - gold.fact_appointments
      - Introduced surrogate keys: practice_key, date_key
      - Partitioned fact table by date_key
      - Registered Delta tables in catalog

    architecture_separation:
      initialisation_notebook:
        - Create schema
        - Initial partitioned fact build
        - Create pipeline_metadata table
        - Insert initial watermark
      operational_notebook:
        - Read watermark
        - Apply 7-day sliding window
        - Build incremental aggregation
        - MERGE into fact table
        - Conditionally update watermark

    incremental_processing:
      - Replaced overwrite logic with Delta MERGE upsert
      - Implemented watermark-driven incremental loading
      - Added sliding window for late-arriving data
      - Verified idempotent pipeline behaviour

    data_quality_safeguards:
      - Assert no null surrogate keys
      - Validate status counts sum to total appointments
      - Prevent zero or negative totals
      - Prevent watermark advancement on failure

    outcome:
      - Production-style incremental data pipeline
      - Partitioned Delta fact table
      - MERGE-based upsert logic
      - Watermark-controlled state management
      - Defensive data validation
      - Clear separation of setup and execution logic
