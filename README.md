# Building_RSS_Feed_Pipeline_With_OpenSource
Building a Data Pipeline: Integrating Proxy Rotation, Airflow, Flask, CDC_Kafka, MongoDB, Redis, Elasticsearch, and MinIO for Web Scraping

<!-- ARCHITECTURE DIAGRAM -->
## Architecture diagram
![MVP Architecture](./images/RSS_FEED_PIPELINE_DIAGRAM.jpg)

<!-- HOW IT WORKS -->
## How it works
#### Data Scraping
Airflow DAG chịu trách nhiệm thực thi các tác vụ cào dữ liệu sử dụng ngôn ngữ Python.
Nó thực thi định kỳ vào X giờ mỗi ngày để tạo thành các micro-batches.
- Nhiệm vụ đầu tiên là cập nhật **proxypool** (danh sách các proxy). Việc sử dụng danh sách proxy kết hợp với user-agents có thể giúp vượt qua các biện pháp chống cào dứ liệu và tránh bị phát hiện là người cào dữ liệu.

- Nhiệm vụ thứ hai là trích xuất thông tin từ nguồn cung cấp RSS feeds, xác thực chất lượng và gửi dữ liệu đến **Kafka topic A**. Quá trình trích xuất sử dụng proxy đã được xác thực từ **proxypool**.
#### Data flow
