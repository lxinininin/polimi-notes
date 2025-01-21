# Neo4j

* **Main features**:
	* Schema free
	* ACID



# MongoDB

* **Main features**:
	* Schema free
	* No transations
	* No joins
	* Max document size of 16MB
	* Data stored as BSON (Binary JSON)

* **Indexes**: B+ tree indexes



# From GPT

* **Unstructured Data Models**

	* **NoSQL Data Models**:

		* Shift from traditional SQL to flexible, unstructured formats.

		* Key principles:
			* **Schema-on-Read** (vs. Schema-on-Write): Schema is defined when data is read, offering flexibility.
			* **Horizontal Scalability**: Adding more machines for scalability, unlike vertical scaling.

	* **Data Lakes:**

		* Centralized storage for raw structured and unstructured data.
		* Must be organized and indexed to prevent “data swamps.”
		* Supports scalability and analytical workloads.

	* **CAP Theorem:** Distributed systems can only achieve two of three guarantees: **Consistency**, **Availability**, or **Partition Tolerance**.

* **NoSQL Databases**

	* **Graph Databases:**
		* **Graph Theory Basics**:
			* Nodes and edges represent entities and relationships.
			* Metrics like degree, paths, and cycles provide insights.
		* **Neo4J**:
			* Features ACID compliance and schema-less design.
			* Queries are made using the **Cypher** language.

	* **Key-Value Stores:**
		* Optimized for fast lookups using keys.
		* **Redis**:
			* In-memory database with O(1) complexity for most operations.
			* Supports persistence via snapshots or append-only files.
	* **Columnar Databases:**
		* Optimized for analytical workloads.
		* **Cassandra**:
			* Row-based, query-first design.
			* High availability and fault tolerance via decentralized architecture.
	* **Document Databases:**
		* Store data in JSON-like documents for flexibility.
		* **MongoDB**:
			* Supports dynamic schemas.
			* CRUD operations for managing nested documents.

* **Streaming Data Engineering**

	* **Batch vs. Streaming:**
		* **Batch**: Processes large datasets periodically.
		* **Streaming**: Processes data in real-time as it’s generated.
	* **Apache Spark:**
		* Unified analytics engine for big data with in-memory processing.
		* Core components:
			* **RDDs**: Low-level data structures for distributed processing.
			* **DataFrames**: Higher-level, SQL-like abstraction.
			* **Datasets**: Typed APIs for static languages like Scala and Java.
		* **Structured Streaming**:
			* Handles real-time data streams using a table-like abstraction.
			* upports micro-batch and continuous modes.

* **Data Pipelines**

	* **Data Ingestion:**

		* **Web APIs**:
			* RESTful APIs allow programmatic access to data using standard methods (e.g., GET, POST).
			* Authentication via OAuth is common.

		* **Web Scraping**:
			* Extract data directly from web pages when APIs are unavailable.
			* Tools: **Selenium**, **BeautifulSoup**.

	* **Data Wrangling**

		* **Importance of Clean Data:**
			* Ensures data quality metrics: **Accuracy**, **Completeness**, **Uniqueness**, **Timeliness**, and **Consistency**.
			* Poor data leads to inaccurate analysis and decision-making.

		* **Data Cleansing Techniques:**
			* **Parsing**: Convert raw data into structured formats.
			* **Handling Missing Data**:
				* Imputation methods (e.g., average values, advanced algorithms).
				* Deletion (only when necessary).
			* **Outlier Detection**: Identify anomalies using statistical methods or visualization.
			* **Standardization**: Normalize data into consistent formats and units.



