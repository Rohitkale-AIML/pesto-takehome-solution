# Pesto Take Home Assessment: Data Engineering Case Study (AdvertiseX)

## Context:

Imagine you are a data engineer working for AdvertiseX, a digital advertising technology company. AdvertiseX specializes in programmatic advertising and manages multiple online advertising campaigns for its clients. The company handles vast amounts of data generated by ad impressions, clicks, conversions, and more. 

### Data Sources and Formats:

- **Ad Impressions:**

	- Data Source: AdvertiseX serves digital ads to various online platforms and websites.
	- Data Format: Ad impressions data is generated in JSON format, containing information such as ad creative ID, user ID, timestamp, and the website where the ad was displayed.

- **Clicks and Conversions:**

	- Data Source: AdvertiseX tracks user interactions with ads, including clicks and conversions (e.g., sign-ups, purchases).
	- Data Format: Click and conversion data is logged in CSV format and includes event timestamps, user IDs, ad campaign IDs, and conversion type.

- **Bid Requests:**

	- Data Source: AdvertiseX participates in real-time bidding (RTB) auctions to serve ads to users.
	- Data Format: Bid request data is received in a semi-structured format, mostly in Avro, and includes user information, auction details, and ad targeting criteria.

## Case Study Requirements: 

- **Data Ingestion:** Implement a scalable data ingestion system capable of collecting and processing ad impressions (JSON), clicks/conversions (CSV), and bid requests (Avro) data. Ensure that the ingestion system can handle high data volumes generated in real-time and batch modes.
- **Data Processing:** Develop data transformation processes to standardize and enrich the data. Handle data validation, filtering, and deduplication. Implement logic to correlate ad impressions with clicks and conversions to provide meaningful insights.
- **Data Storage and Query Performance:** Select an appropriate data storage solution for storing processed data efficiently, enabling fast querying for campaign performance analysis. Optimize the storage system for analytical queries and aggregations of ad campaign data.
- **Error Handling and Monitoring:** Create an error handling and monitoring system to detect data anomalies, discrepancies, or delays. Implement alerting mechanisms to address data quality issues in real-time, ensuring that discrepancies are resolved promptly to maintain ad campaign effectiveness.

## Solution:

To address the data engineering requirements for AdvertiseX, we can design a scalable and efficient data processing pipeline using Apache Spark, Apache Kafka, and Apache Hive. Here's a comprehensive approach to the solution:

### Data Ingestion:

1. **Apache Kafka:** We'll use Apache Kafka as a distributed streaming platform to ingest real-time data from various sources (ad impressions, clicks/conversions, and bid requests).
2. **Kafka Topics:** Create separate Kafka topics for each data source
   - ```ad-impressions``` (for JSON ad impression data)
   - ```clicks-conversions``` (for CSV click and conversion data)
   - ```bid-requests``` (for Avro bid request data)
3. **Kafka Producers:** Develop Kafka producers to publish data to the respective topics. These producers can be integrated with the AdvertiseX ad serving systems, user interaction tracking systems, and real-time bidding platforms.
4. **Batch Data Ingestion:** For batch data ingestion (e.g., historical data), we can use Apache Spark to read the data from the respective sources and write it directly to the corresponding Kafka topics.

### Data Processing:

1. **Apache Spark Structured Streaming:** Use Apache Spark Structured Streaming to consume data from Kafka topics and perform data transformations and enrichment.
2. **Data Parsing and Validation:** Parse the incoming data based on the format (JSON, CSV, Avro) and apply data validation rules to ensure data quality.
3. **Data Transformation and Enrichment:** Perform necessary data transformations and enrichment, such as:
   - Standardize data formats
   - Handle deduplication
   - Correlate ad impressions with clicks and conversions
   - Enrich data with additional metadata (e.g., campaign details, user information)
4. **Apache Hive:** Use Apache Hive as a data warehouse to store the processed and enriched data in an optimized columnar format (e.g., Apache Parquet or Apache ORC).
5. **Hive Partitioning:** Partition the Hive tables based on relevant columns (e.g., date, campaign ID) to improve query performance and optimize for common analytical queries.

### Data Storage and Query Performance:

1. **Apache Hive:** Leverage Apache Hive as the data warehouse solution for storing processed data in an optimized columnar format.
2. **Hive Partitioning and Bucketing:** Partition and bucket the Hive tables based on commonly used columns (e.g., date, campaign ID) to improve query performance and enable pruning of unnecessary data during queries.
3. **Hive Indexing:** Create Hive indexes on relevant columns to further enhance query performance for specific types of queries.
4. **Hive Views and Materialized Views:** Create Hive views and materialized views to simplify complex analytical queries and improve query response times.

### Error Handling and Monitoring:

1. **Apache Spark Structured Streaming Monitoring:** Implement monitoring capabilities within Apache Spark Structured Streaming to detect data anomalies, discrepancies, or delays.
2. **Alerting System:** Integrate an alerting system (e.g., email, Slack, or PagerDuty) with Apache Spark Structured Streaming to receive notifications about data quality issues or pipeline failures.
3. **Data Quality Checks:** Implement data quality checks within the Apache Spark Structured Streaming pipeline to validate data integrity and consistency. These checks can include rules for data completeness, format adherence, and value range validation.
4. **Error Handling and Retry Mechanisms:** Implement error handling and retry mechanisms within the Apache Spark Structured Streaming pipeline to handle transient failures and ensure data processing resiliency.
5. **Apache Kafka Monitoring:** Monitor Kafka topics for potential issues, such as lagging consumers, topic underreplicated partitions, or broker failures.

### Visual Representation:
```plaintext
+---------------+      +------------+              +--------------+        +------------+
| Ad Impression |      | Clicks/    |              | Bid Requests |        | Batch Data |
|     (JSON)    |      | Conversions|              |    (Avro)    |        |  Sources   |
+-------+-------+      +------+-----+              +-------+------+        +------+-----+
        |                     |                            |                      |
        |                     |                            |                      |
        |                     |                            |                      |
        v                     v                            v                      v
+---------------+      +---------------+           +---------------+      +--------------+
|     Kafka     |      |     Kafka     |           |     Kafka     |      |    Apache    |
|   Producer    |      |   Producer    |           |   Producer    |      |    Spark     |
+-------+-------+      +-------+-------+           +-------+-------+      +-------+------+
        |                      |                           |                      |
        |                      |                           |                      |
        v                      v                           v                      |
+---------------+      +--------------------+      +---------------+              |
|  ad-impressions      | clicks-conversions |      | bid-requests  |              |
|     Topic     |      |     Topic          |      |    Topic      |              |
+---------------+      +--------------------+      +---------------+              |
        |                     |                          |                        |
        |                     |                          |                        |
        +---------------+-----+--------------------------+------------------------+
                        |
                        v
            +-----------------------+
            | Apache Spark          |
            | Structured Streaming  |
            +-----------------------+
                        |
                        v
            +-----------------------+
            |     Data              |
            | Transformation        |
            | and Enrichment        |
            +-----------------------+
                        |
                        v
            +-----------------------+
            |       Apache          |
            |        Hive           |
            |     Data Warehouse    |
            +-----------------------+
                        |
                        v
            +-----------------------+
            |  Analytical Queries   |
            |     and Reports       |
            +-----------------------+
                        |
                        v
            +-----------------------+
            |    Error Handling     |
            |    and Monitoring     |
            +-----------------------+
```

## Implementation and Documentation:

**For the implementation of this solution, we will need to:**

- **Set up Apache Kafka:** Install and configure Apache Kafka clusters, create topics, and develop Kafka producers for each data source.
  - Install and configure Apache Kafka clusters following the official documentation: https://kafka.apache.org/documentation/
  - Create the following Kafka topics:
    ```bash
    # Ad Impressions Topic
    bin/kafka-topics.sh --create --topic ad-impressions --bootstrap-server <kafka-broker-host>:<port> --partitions 8 --replication-factor 3

    # Clicks and Conversions Topic
    bin/kafka-topics.sh --create --topic clicks-conversions --bootstrap-server <kafka-broker-host>:<port> --partitions 8 --replication-factor 3

    # Bid Requests Topic
    bin/kafka-topics.sh --create --topic bid-requests --bootstrap-server <kafka-broker-host>:<port> --partitions 8 --replication-factor 3
    ```
  - Develop Kafka producers for each data source:
	- *Ad Impressions Producer (JSON):* Develop a Kafka producer that reads ad impression data in JSON format from the AdvertiseX ad serving system and publishes it to the ad-impressions topic.
	- *Clicks and Conversions Producer (CSV):* Develop a Kafka producer that reads click and conversion data in CSV format from the AdvertiseX user interaction tracking system and publishes it to the clicks-conversions topic.
	- *Bid Requests Producer (Avro):* Develop a Kafka producer that reads bid request data in Avro format from the AdvertiseX real-time bidding platform and publishes it to the bid-requests topic.
- **Develop Apache Spark Structured Streaming Pipeline:** Implement the Apache Spark Structured Streaming pipeline to consume data from Kafka topics, perform data transformations, enrichment, and write processed data to Apache Hive.
- **Configure Apache Hive:** Set up Apache Hive as the data warehouse, create tables with appropriate partitioning and bucketing strategies, and define views and materialized views for common analytical queries.
- **Implement Error Handling and Monitoring:** Develop error handling mechanisms, data quality checks, and monitoring capabilities within the Apache Spark Structured Streaming pipeline and integrate with an alerting system.
- **Write Documentation:** Thoroughly document the entire solution, including the architecture, data flow, dependencies, configurations, and operational procedures.
- **Code and Test:** Write the necessary code for the solution components (Kafka producers, Apache Spark Structured Streaming pipeline, Hive table definitions, error handling, and monitoring) and thoroughly test the solution in a staging environment before deploying to production.

This comprehensive solution leverages the power of Apache Spark, Apache Kafka, and Apache Hive to address the data ingestion, processing, storage, and analytical requirements of AdvertiseX. By following this approach, AdvertiseX can effectively handle high volumes of real-time and batch data, optimize storage and query performance, and ensure data quality through robust error handling and monitoring mechanisms.

