Goal: Build a YouTube ETL Pipeline with Airflow & MySQL
Create a step-by-step DIY ETL project that extracts YouTube video information using the YouTube Data API, transforms it with Python, and loads it into MySQL using Apache Airflow for orchestration.
The whole project is containerized and runs on docker containers. Docker containers run mysql and Apache Airflow containers, mysql Table is created on the fly if not existing. In the end, we will use “streamlit”, an open-source Python framework to create a interactive dashboard.
Project Overview & Architecture 
•	A fully automated ETL pipeline that:
•	Extracts YouTube video metadata (e.g., title, views, likes).
•	Transforms the raw data.
•	Loads it into a MySQL database.
•	Orchestrates everything with Apache Airflow.
•	Pipeline runs on Docker containers
Tools Used:
•	Python
•	YouTube Data API v3
•	MySQL
•	Apache Airflow
•	Pandas (for transformation)
•	Dockers
•	streamlit
________________________________________
Before we dive in, let's understand the components and how they interact:
•	YouTube Data API: Our data source for extracting video information.
•	Python Script (Main.py): A custom scripts Which controls the flow of the pipeline including scheduling and dependencies.
•	Apache Airflow: The orchestrator for our ETL process. It will schedule and manage the tasks (extract, transform, load).
•	MySQL Database: Our destination for storing the transformed YouTube video data.
•	Docker & Docker Compose: To containerize all services (Airflow, MySQL, and potentially our Python extractor if it's a standalone service) for easy setup and portability.


The flow will be:
1.	Extract: A Python script (triggered by Airflow) will use the YouTube Data API to fetch video data.
2.	Transform (Feature Engineering): Another Python script (or a task within Airflow) will clean, enrich, and reformat the extracted data.
3.	Load: The transformed data will be loaded into the MySQL database.
4.	Orchestration: Airflow will ensure these steps run in the correct order and on a schedule.
Step 2: Prerequisites
Before starting, ensure you have:
1.	Python 3.x
2.	Docker Desktop: Includes Docker Engine and Docker Compose. Download from https://www.docker.com/products/docker-desktop.
3.	Google Account: Required to obtain a YouTube Data API Key.
Getting Your YouTube Data API Key 
This is crucial for extracting data from YouTube.
1.	Go to Google Cloud Console: Open your web browser and navigate to https://console.cloud.google.com/. Sign in with your Google account.
2.	Create a New Project:
o	Click on the project selector dropdown (usually at the top left, showing your current project name or "My First Project").
o	Click "New Project".
o	Give your project a name (e.g., "YouTube ETL Project") and click "Create".
3.	Enable the YouTube Data API v3:
o	Once your project is created, navigate to the "APIs & Services" section in the left-hand menu, then select "Library".
o	In the search bar, type "YouTube Data API v3" and select it from the results.
o	Click the "Enable" button.
4.	Create API Credentials:
o	After enabling the API, navigate to "APIs & Services" -> "Credentials" in the left-hand menu.
o	Click on "+ CREATE CREDENTIALS" and select "API Key".
o	A pop-up will display your new API key. Copy this key immediately and keep it safe! This key grant access to the YouTube API.




5.	Project structure:
 
Docker Compose Configuration (docker-compose.yaml) 🐳
This file will define our services: Airflow and MySQL.
 

6.	Secure Your Secrets
Use environment variables or Airflow’s Connections and Variables to store:
•	YOUTUBE_API_KEY
•	MySQL credentials: user, password, host, etc.
Example .env file:
YOUTUBE_API_KEY=”your_api_key_here”
DB_USER=airflow
DB_PASSWORD=airflow
DB_HOST=’mysql’
DB_NAME=airflow
Port: '3306'
Database: 'airflow_db'



Airflow DAG
This DAG orchestrates the extraction, transformation, and loading steps.
 
Execution:
Airflow will control the whole pipeline orchestration using DAG (main_etl_youtube.py), which internally runs 3 different python programs namely Extract, Transform and Load.
We can monitor the execution thru Airflow UI and can reschedule or run on-demand any desired program. The UI Console also provides access to Logs.
Initializing Airflow and Running Docker Containers 
1.	Navigate to your project root:
$> cd youtube-etl
2.	Initialize Airflow (first time only): This command will create the necessary directories and initialize the Airflow database within the Airflow containers.
Run following command:
$> docker-compose up -d
 
Wait for a few minutes for the containers to start and for Airflow to initialize its database. 
 
Figure 1: Docker Desktop engine
$> docker ps
 
Figure 2 Docker containers
 You can check the logs:
$> docker logs <container id>
Create an Airflow User: You'll need a user to access the Airflow UI for monitoring and observation.
 
Feel free to change username, email, and password as desired.
Enable and Trigger the DAG:
Apache Airflow UI Console:
Login to localhost:8080
Username/password: airflow/airflow
(This admin user name and password can also be defined in the docker compose file).
 
•	In the Airflow UI, go to the DAGs page.
•	You should see youtube_etl_pipeline.
•	Toggle the DAG to On.
•	Click the "Play" button (Trigger DAG) to manually run it for the first time.
Verifying the Data in MySQL 
1.	Connect to your MySQL container:
$> docker exec -it mysql_db bash (or use app like DBeaver or mysql workbench)
2.	Log in to MySQL:
$> mysql -u root -p
Enter the MYSQL_ROOT_PASSWORD you set in your .env file.
3.	Use your database and check the table:
mysql> USE airflow_db;
mysql> SELECT * FROM youtube_videos LIMIT 10;
 
Figure 3 Database - Table updated
You should see the youtube_videos table and the extracted, transformed data within it!

Streamlit: Streamlit is an interactive web applications — especially for data science, machine learning, and analytics.
 

ETL – The Conclusion
Amazing! The dockerized ETL pipeline is deployed and is running using the YouTube Data API, Airflow, and MySQL. This can be 
The source code of the project is available on github for your reference.
Drop me a message in case you face any challenge.
