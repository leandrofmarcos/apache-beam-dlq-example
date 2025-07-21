# Building a Resilient Exception Strategy with Apache Beam and DLQ

## Context: when the pipeline stops because of a single record

In a common large-scale data ingestion scenario using Apache Beam, we faced a classic issue: a single malformed record caused the entire pipeline to fail.

It was expected that some records might contain errors, but the pipeline was assuming everything would work perfectly. There was no fallback, no distinction between business or technical errors, and no isolation for problematic data.

This led us to the key question: how do we keep the pipeline healthy even in the presence of predictable failures?

---

## What we needed to solve

- Prevent exceptions from halting the processing of all data
- Separate technical failures (e.g., API errors) from business logic issues (e.g., invalid value)
- Log errors in a structured format for analysis and possible reprocessing
- Implement controlled retry without infinite loops

---

## Proposed Architecture

We implemented an approach based on:

- `ParDo` with `MultiOutputReceiver` and `TupleTag` to separate data flows
- `try/catch` for error classification
- Retry with a max-attempt limit for technical failures
- DLQ (Dead Letter Queue) with `.jsonl` files saved locally

### Flow Diagram:

```mermaid
graph TD
    A[Incoming Data] --> B[ParDo with exception handling]
    B -->|Success| C[Final Transformation and Output]
    B -->|Business Error| D[DLQ - business_error.jsonl]
    B -->|Technical Error| E[DLQ - technical_error.jsonl]
```

---

## Local Example with Apache Beam

### Dependencies in `pom.xml`

```xml
<dependencies>
  <dependency>
    <groupId>org.apache.beam</groupId>
    <artifactId>beam-sdks-java-core</artifactId>
    <version>2.55.0</version>
  </dependency>
  <dependency>
    <groupId>org.apache.beam</groupId>
    <artifactId>beam-runners-direct-java</artifactId>
    <version>2.55.0</version>
  </dependency>
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-simple</artifactId>
    <version>2.0.7</version>
  </dependency>
</dependencies>
```

---

## Running Locally

```bash
mvn clean compile exec:java -Dexec.mainClass=com.example.beam.ExceptionHandlingPipeline
```

Output files will be generated in:
- `output/success.txt`
- `dlq/business_error.jsonl`
- `dlq/technical_error.jsonl`

---

## Conclusion

This simple example demonstrates how to handle different types of errors, protect your pipeline, and maintain full visibility over problematic records. It’s essential for anyone building reliable solutions with Apache Beam.

> A resilient architecture is not one that never fails — it's one that handles failure responsibly.
