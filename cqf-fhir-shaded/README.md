# CQF FHIR Shaded JAR for Databricks

This module creates a shaded JAR specifically designed to resolve dependency conflicts when using CQF FHIR Clinical Reasoning libraries in Databricks environments.

## Problem Solved

Databricks clusters often have their own versions of common libraries like Guava, Jackson, and Apache Commons. When using CQF FHIR libraries, you may encounter `IllegalAccessError` or `NoSuchMethodError` exceptions due to version conflicts.

## Solution

This shaded JAR relocates potentially conflicting dependencies to avoid classpath conflicts:

- **Guava**: `com.google.common` → `org.opencds.cqf.shaded.google.common`
- **Jackson**: `com.fasterxml.jackson` → `org.opencds.cqf.shaded.jackson`
- **Apache Commons**: `org.apache.commons` → `org.opencds.cqf.shaded.apache.commons`
- **ANTLR**: `org.antlr` → `org.opencds.cqf.shaded.antlr`

## Usage in Databricks

1. **Build the shaded JAR:**
   ```bash
   mvn clean package -pl cqf-fhir-shaded -am -DskipTests
   ```

2. **Upload to Databricks:**
   - Upload `target/cqf-fhir-shaded-3.24.0-SNAPSHOT-shaded.jar` to your Databricks workspace
   - Install it as a cluster library

3. **Use in your code:**
   Your existing imports remain the same - only internal dependencies are relocated:
   ```scala
   import org.opencds.cqf.fhir.cr.measure.r4.R4MeasureProcessor
   import org.opencds.cqf.fhir.utility.repository.InMemoryFhirRepository
   // ... other imports work as before
   ```

## What's Included

The shaded JAR includes all necessary CQF FHIR Clinical Reasoning components:
- `cqf-fhir-cr` - Clinical Reasoning operations (Measure evaluation, etc.)
- `cqf-fhir-utility` - FHIR utilities and repository implementations
- `cqf-fhir-cql` - CQL engine integration
- `cqf-fhir-api` - Core APIs
- All HAPI FHIR dependencies
- CQL engine and related libraries

## File Size

The shaded JAR is approximately 60-80MB and contains all dependencies needed to run CQF FHIR Clinical Reasoning in isolated environments like Databricks.

## Troubleshooting

If you still encounter dependency conflicts:
1. Check that you're using the `-shaded` JAR file
2. Ensure no other CQF FHIR or conflicting libraries are installed on the cluster
3. Check the Databricks runtime version for known compatibility issues

## Build Requirements

- Java 17+
- Maven 3.6+
- All CQF FHIR modules must be compiled first
