# Ecomm_event_driven_dbx_Pipline

## Overview

An event-driven data pipeline built on Databricks for processing e-commerce data in real-time. This pipeline implements incremental data loading, data validation, enrichment, and merge operations using Delta Lake architecture.

## Architecture

This pipeline follows a modular, event-driven architecture with the following stages:

1. **Stage Loading**: Ingests raw data from source systems into staging tables
2. **Data Validation**: Validates data quality and integrity
3. **Data Enrichment**: Enriches data with additional context and transformations
4. **Merge Operations**: Performs UPSERT operations into final Delta tables

## Pipeline Components

### 1. Orders Stage Load (`01_orders_stage_load.ipynb`)
- Loads orders data from external volume into staging table
- Handles incremental data ingestion
- Archives processed files
- Implements error handling and logging

### 2. Customers Stage Load (`02_customers_stage_load.ipynb`)
- Processes customer dimension data
- Supports SCD Type 2 for tracking changes
- Loads into customer staging table

### 3. Products Stage Load (`03_products_stage_load.ipynb`)
- Ingests product catalog data
- Maintains product master data
- Handles product attribute updates

### 4. Inventory Stage Load (`04_inventory_stage_load.ipynb`)
- Loads inventory levels and stock information
- Tracks inventory movements
- Maintains warehouse-level stock data

### 5. Shipping Stage Load (`05_shipping_stage_load.ipynb`)
- Processes shipping and fulfillment data
- Tracks delivery status and logistics
- Maintains shipping history

### 6. Data Validation (`06_data_validation.ipynb`)
- Validates data quality across all staging tables
- Implements business rule checks
- Identifies and flags data quality issues
- Generates data quality metrics

### 7. Data Enrichment (`07_data_enrichment.ipynb`)
- Enriches orders with customer and product details
- Calculates derived metrics
- Applies business transformations
- Joins multiple data sources

### 8. Final Merge Operation (`08_final_merge_operation.ipynb`)
- Performs UPSERT operations using Delta MERGE
- Updates existing records and inserts new ones
- Maintains data lineage
- Implements idempotent processing

## Technologies Used

- **Databricks**: Unified analytics platform
- **Apache Spark**: Distributed data processing
- **Delta Lake**: ACID transactions and time travel
- **PySpark**: Python API for Spark
- **Databricks Workflows**: Job orchestration

## Key Features

✅ **Event-Driven Architecture**: Triggered by file arrival events
✅ **Incremental Processing**: Only processes new/changed data
✅ **Data Quality Validation**: Built-in validation checks
✅ **Error Handling**: Comprehensive error handling and logging
✅ **Scalability**: Distributed processing with Spark
✅ **ACID Transactions**: Guaranteed data consistency with Delta Lake
✅ **Idempotency**: Safe to re-run without duplicates
✅ **Archive Management**: Automated file archival

## Data Flow
### Successful Pipeline Execution Graph
This shows the successful execution of all pipeline stages:
<img width="1895" height="908" alt="Screenshot 2025-12-21 222432" src="https://github.com/user-attachments/assets/6ce6a50f-326c-48fc-95e1-94dff01678ca" />

```
Source Data (External Volume)
    ↓
[File Arrival Trigger]
    ↓
Stage Loading Notebooks (01-05)
    ↓
Staging Tables (Delta)
    ↓
Data Validation (06)
    ↓
Data Enrichment (07)
    ↓
Final Merge (08)
    ↓
Production Tables (Delta)
```

## Setup Instructions

### Prerequisites
- Databricks workspace (GCP/AWS/Azure)
- Access to external volume for source data
- Appropriate compute cluster configuration

### Configuration

1. **External Volume Setup**:
   - Configure external volume path: `/Volumes/gds_workspace/default/external_volume/`
   - Set up separate folders for each data type (orders_data, customer_data, etc.)
   - Create archive directories for processed files

2. **Cluster Configuration**:
   - Runtime: DBR 13.3 or higher
   - Node Type: Standard_DS3_v2 or equivalent
   - Autoscaling: Enabled (recommended)

3. **Database Setup**:
   - Catalog: `gds_workspace`
   - Schema: `default`
   - Tables will be created automatically on first run

### Deployment

1. Clone this repository to your Databricks workspace
2. Configure external volume paths in each notebook
3. Set up file arrival trigger in Databricks Workflows
4. Create a job with tasks for each notebook in sequence
5. Test with sample data files

## Workflow Configuration

The pipeline can be orchestrated using Databricks Workflows:

```yaml
Job: Ecomm_event_driven_dbx_pipeline
Trigger: File arrival at /Volumes/gds_workspace/default/external_volume/trigger_file/

Tasks:
  - Task 1: customer_stage_load
  - Task 2: inventory_stage_load  
  - Task 3: orders_stage_load
  - Task 4: products_stage_load
  - Task 5: shipping_stage_load
  - Task 6: data_validation (depends on Tasks 1-5)
  - Task 7: data_enrichment (depends on Task 6)
  - Task 8: final_merge_operation (depends on Task 7)
```








## Monitoring and Logging

- Each notebook includes print statements for progress tracking
- Error handling captures and logs exceptions
- Delta table history provides audit trail
- Workflow UI shows task-level execution details

## Data Schema

### Staging Tables
- `customer_stage`
- `inventory_stage`
- `orders_stage`
- `products_stage`
- `shipping_stage`

### Production Tables
- TBD based on final merge logic

## Best Practices Implemented

1. **Incremental Processing**: Only processes new files to optimize performance
2. **Error Recovery**: Failed files can be reprocessed without affecting successful ones
3. **Data Archiving**: Processed files moved to archive for audit trail
4. **Schema Evolution**: Delta Lake handles schema changes automatically
5. **Transaction Log**: Complete history of all data changes

## Future Enhancements

- [ ] Add data quality metrics dashboard
- [ ] Implement real-time streaming ingestion
- [ ] Add data lineage tracking
- [ ] Create automated testing framework
- [ ] Add support for CDC (Change Data Capture)
- [ ] Implement data retention policies

## Contributing

Feel free to open issues or submit pull requests for improvements.

## License

MIT License

## Author

Abhay Dawar
- GitHub: [@iamabhaydawar](https://github.com/iamabhaydawar)
- Email: iamabhaydawar@gmail.com

## Acknowledgments

- Built with Databricks and Delta Lake
- Leverages Apache Spark for distributed processing
