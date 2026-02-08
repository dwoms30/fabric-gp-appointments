# Data Quality Rules

## Silver.appointments_clean

### Required fields
- practice_code must not be null
- appointment_date must not be null

### Data types
- age: integer
- appointment_date: date

### Allowed values
- current_slot_status ∈ {Left, DNA, Walked Out}
- gender ∈ {Male, Female, Unknown}

### Validation checks
- Count of null appointment_date = 0
- Distinct current_slot_status values = expected set
- All practice_code values join to reference table

## Silver.practice_reference_clean

### Uniqueness
- One row per practice_code

### Required fields
- practice_code must not be null
