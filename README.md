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


3. Crate a AWS Glue Job to move data from s3 bucket to  Amazon Redshift.

Prepare S3 Data: Ensure that your data in the S3 bucket is structured or semi-structured and is accessible by AWS Glue. This may involve organizing your data into folders or adjusting permissions.

Create IAM Roles:
Make sure you have an IAM role for AWS Glue that has the necessary permissions to read from the S3 bucket and write to Amazon Redshift. Attach the AWSGlueServiceRole and AmazonS3FullAccess policies for S3 access and AmazonRedshiftFullAccess for Redshift access.

Create a Redshift Table:
Before running the Glue Job, ensure that you have a Redshift table created to store the data. Follow the steps mentioned earlier to create a Redshift table.

Open AWS Glue Console:
Go to the AWS Glue Console: https://console.aws.amazon.com/glue/.
In the left navigation pane, choose "Jobs."

Create a New Job:

Click on the "Add job" button.
Enter a name for your job.
Choose the IAM role you created earlier.


Add a Data Source (S3):

In the "Data source" section, choose "Amazon S3" as the source.
Specify the S3 path where your data is stored.
Define the schema and any additional options based on your data.


Add a Data Target (Redshift):

In the "Data target" section, choose "Amazon Redshift" as the target.
Provide the Redshift connection details.
Specify the target table name in Redshift.

Run the Job : Click on "Run job" to execute the Glue Job.


4. Schedule AWS Lambda function to download data automatically on specific interval.


















   

