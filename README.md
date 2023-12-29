1. Create a AWS Lambda function to read from any open source API such https://rapidapi.com/ and store it into s3 bucket.

import json
import requests

def lambda_handler(event, context):
    # Replace the URL with the open-source API endpoint you want to access
    api_url = "[https://jsonplaceholder.typicode.com/todos/1](https://rapidapi.com/)"
    
    # Make a GET request to the API
    response = requests.get(api_url)
    
    # Check if the request was successful (status code 200)
    if response.status_code == 200:
        data = response.json()
        print("API Response:", data)
        return {
            'statusCode': 200,
            'body': json.dumps(data)
        }
    else:
        print("Failed to retrieve data from the API.")
        return {
            'statusCode': response.status_code,
            'body': "Failed to retrieve data from the API."
        }

2.Create a redshift table to store record of s3 files data.

Connect to Redshift: Use a SQL client or any tool that allows you to execute SQL commands against your Redshift cluster.

Create a Redshift Table: 

CREATE TABLE s3_files_data (
    id INT IDENTITY(1,1),
    column1 VARCHAR(255),
    column2 INT,
    column3 TIMESTAMP,
    -- Add more columns as needed
    PRIMARY KEY (id)
);

Load Data from S3: 

COPY s3_files_data
FROM 's3://data/input/'
IAM_ROLE 'your-redshift-iam-role-arn'
DELIMITER ',' -- adjust the delimiter based on your data format
IGNOREHEADER 1 -- if your file has a header row
CSV;

Query the Data: SELECT * FROM s3_files_data;





















   

