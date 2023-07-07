# liquibase_devops

This project demonstrates how implement liquibase DDL deployment to a generic target database using Azure DevOps YAML pipelines. The solution has been tested for Snowflake but should be adaptable to other database types.

# DevOps Configuration

- DevOps pipeline uses a mix of Azure Key Vault secrets (variable group for demo purposes only) and JSON variable inputs:
    - JSON variable inputs can be used to configure static cross-environment values (e.g database driver) at codebase level.
    - Azure Key Vault secrets can be used to configure dynamic cross-environment values and authentication (e.g. user / pass / database name).

## Variable Group / Secrets

Defined Azure Key Vault variables in variable group for demo purposes, should be moved to Azure Key Vault secrets:

- `SF_USERNAME` - Username for executing database scripts with liquibase
- `SF_PASSWORD` - Password for executing database scripts with liquibase
- `SF_URL` - Example: `jdbc:snowflake://abcdefg-sf12345.snowflakecomputing.com/?db=DEMO_DB&schema=LIQUIBASE_METADATA&warehouse=COMPUTE_WH&role=ACCOUNTADMIN`
- `LIQUIBASE_CUSTOM_LIBRARY_URL` - Custom library URL to download to `liquibase/lib/` (optional)
## Parameters

- `LIQUIBASE_VERSION` - Liquibase version downloaded from GitHub
- `LIQUIBASE_DATABASE` - Liquibase database specifying where connector.json is read from
- `RUN_STATUS` - Flag for running liquibase status
- `RUN_VALIDATE` - Flag for running liquibase validate
- `RUN_UPDATE` - Flag for running liquibase update

# Liquibase 

## Requirements

- Value passed to `changeLogFile` in liquibase CLI must be a relative path to $(Build.SourcesDirectory)
- `database.schema` in SF_URL must already exist as it used to store liquibase `DATABASECHANGELOG` and `DATABASECHANGELOGLOCK` metadata tables

# Connectors

## Snowflake - Requirements

- Must run `ALTER ACCOUNT SET MULTI_STATEMENT_COUNT = 0;` on executing account to support multi-command changsets using JDBC via liquibase.
