# Provider Lookup Project

## Overview

The Provider Lookup Project is a PostgreSQL database built using healthcare provider data from the National Plan and Provider Enumeration System (NPPES).

The project organizes provider, address, and taxonomy information into relational database tables so that provider information can be searched and queried efficiently.

## Data Sources

The project uses:

- NPPES Monthly Downloadable File Version 2
- NUCC Healthcare Provider Taxonomy data

The NPPES data contains information about healthcare providers, including NPIs, names, organizations, addresses, and taxonomy codes.

The NUCC taxonomy data provides information about provider classifications and specializations.

## Database Structure

The database contains four main tables:

### Provider

Stores basic information about each healthcare provider.

Important fields include:

- `id` - Primary key
- `npi` - National Provider Identifier
- `entity_type`
- `first_name`
- `last_name`
- `organization_name`

The NPI is unique for each provider.

### Address

Stores provider address and contact information.

Important fields include:

- `id` - Primary key
- `provider_id` - Foreign key referencing `provider.id`
- `provider_npi`
- `address_type`
- `address_line_1`
- `address_line_2`
- `city`
- `state`
- `postal_code`
- `phone`

Each address is connected to a provider using `provider_id`.

### Taxonomy

Stores healthcare provider taxonomy information.

Important fields include:

- `id` - Primary key
- `taxonomy_code`
- `grouping`
- `classification`
- `specialization`
- `definition`

Each taxonomy code is unique.

### Provider Taxonomy

This table connects providers with their taxonomy classifications.

Important fields include:

- `id` - Primary key
- `provider_id` - Foreign key referencing `provider.id`
- `taxonomy_id` - Foreign key referencing `taxonomy.id`
- `is_primary` - Indicates whether the taxonomy is the provider's primary taxonomy

A provider can have multiple taxonomy classifications.

## Database Relationships

The database uses relational connections between the tables:

- One provider can have multiple addresses.
- One provider can have multiple taxonomy classifications.
- Multiple providers can share the same taxonomy.
- `provider_taxonomy` acts as the connection between providers and taxonomies.

The database uses ID-based primary and foreign keys to maintain these relationships.

## Data Import

NPPES provider data is imported from CSV files into PostgreSQL.

Because the NPPES dataset contains millions of records, the data is processed and organized into the appropriate relational tables.

NUCC taxonomy data is also imported so that taxonomy codes can be connected to readable classifications, specializations, and definitions.

## Data Integrity

The database includes several constraints to maintain data integrity:

- Primary keys for every table
- Unique NPIs in the `provider` table
- Unique taxonomy codes in the `taxonomy` table
- Foreign keys connecting related tables
- `NOT NULL` constraints for required fields
- A unique constraint on the combination of `provider_id` and `taxonomy_id`

These constraints help prevent duplicate or invalid relationships.

## Example Query

The following query can be used to view providers with their taxonomy information:

```sql
SELECT
    p.npi,
    p.first_name,
    p.last_name,
    p.organization_name,
    t.taxonomy_code,
    t.classification,
    t.specialization
FROM provider p
JOIN provider_taxonomy pt
    ON p.id = pt.provider_id
JOIN taxonomy t
    ON pt.taxonomy_id = t.id
LIMIT 20;
