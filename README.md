# Building_RSS_Feed_Pipeline_With_OpenSource
Building a Data Pipeline: Integrating Proxy Rotation, Airflow, Flask, CDC_Kafka, MongoDB, Redis, Elasticsearch, and MinIO for Web Scraping

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [Architecture diagram](#architecture-diagram)
* [How it works](#how-it-works)
    * [Data scraping](#data-scraping)
    * [Data flow](#data-flow)

<!-- ARCHITECTURE DIAGRAM -->
## Architecture diagram
![MVP Architecture](./images/RSS_FEED_PIPELINE_DIAGRAM.jpg)

<!-- HOW IT WORKS -->
## How it works
#### Data Scraping
Airflow DAG chịu trách nhiệm thực thi các tác vụ cào dữ liệu sử dụng ngôn ngữ Python.
Nó thực thi định kỳ vào X giờ mỗi ngày để tạo thành các micro-batches.
- Nhiệm vụ đầu tiên là cập nhật **proxypool** (danh sách các proxy). Việc sử dụng danh sách proxy kết hợp với user-agents có thể giúp vượt qua các biện pháp chống cào dữ liệu và tránh bị phát hiện là người cào dữ liệu.

- Nhiệm vụ thứ hai là trích xuất thông tin từ nguồn cung cấp RSS feeds, xác thực chất lượng và gửi dữ liệu đến **Kafka topic A**. Quá trình trích xuất sử dụng proxy đã được xác thực từ **proxypool**.
#### Data flow
- Kafka Connect **Mongo Sink** nhận dữ liệu từ **Kafka topic A** và lưu trữ vào MongoDB (dựa trên trường **_id**).
- **Debezium MongoDB Source** theo dỗi bộ bản sao của MongoDB để phát hiện các document bị thay đổi trong databases and collections, ghi lại những thay đổi đó dưới dạng sự kiện trong **Kafka topic B**.
- Kafka Connect **Elasticsearch Sink** nhận dữ liệu từ **Kafka topic B** và cập nhật vào Elasticsearch. Dữ liệu sao chép giữa topics **A** và **B** đảm bảo rằng MongoDB và ElasticSearch đồng bộ với nhau.
- Kafka Connect **S3-Minio Sink** nhận dữ liệu từ **Kafka topic B** và lưu trữ chúng vào MinIO (high-performance object storage) để bảo đảm tính liên tục của dữ liệu.
