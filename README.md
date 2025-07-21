# Apache Beam DLQ Example

This project demonstrates how to build a resilient data pipeline using [Apache Beam](https://beam.apache.org/) with:

- Structured error handling
- Controlled retries with fallback
- Dead Letter Queue (DLQ) for business and technical failures

## 📖 Article

This article is also available on [Dev.to](https://dev.to/leandrofmarcos) *(pending publish)*  
👉 You can read it directly [here](./articles/apache-beam-dlq.md)

## 💻 How to run locally

```bash
mvn clean compile exec:java -Dexec.mainClass=com.example.beam.ExceptionHandlingPipeline
