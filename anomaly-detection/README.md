# Anomaly detection with AI

In this application, two types of algorithms were developed to detect anomalies in weather data. The first option is a simple AI algorithm based on the Gaussian equation that is entirely self-made.

The second algorithm is a Machine Learning algorithm called the Isolation Forest algorithm. The reason for creating two types of algorithms was to compare their performance in detecting anomalies. The goal of the AI is to determine if the temperature is above or below normal compared to a historical dataset from Zanzibar. The output of the algorithm is a piece of text that is sent to the database, which is eventually displayed on the dashboard if an anomaly is detected.

By using the API, the date and temperature are retrieved. These data are then processed through the AI algorithm to receive an output indicating whether the temperature is below or above normal. This result is displayed as a message on the dashboard.


## The flow of Data

### Extracting the Data

Using an API request, all the temperature data from the previous day in the database is queried. This data is then formatted into a dataframe to be easily processed with Python. The dataframe, containing all the temperature data, is manipulated to calculate an average, which is then given as input to the AI algorithm along with the correct date.

### AI algorithm

The output of the extracting script is used as input for the AI algorithm. The algorithm calculates whether the temperature input is an anomaly or not. If it is an outlier, a string output is sent to the database using the data store script.

### Storing the results in the database

The output of the AI algorithm is sent to the data storage script. The data is converted into Unix format before being sent to the database.

The script then makes a request to the API at its own endpoint, which stores the string value or `None` value in the database.

A message becomes visible on the dashboard for the specific previous day. If the temperature is an anomaly, a message will pop up on the dashboard indicating that the temperature is very low or high for that time of the year. If the new datapoint isn't an anomaly, no message will be displayed on the dashboard.

![Diagram](img/project-zanzibar-ai.jpg)

## The Logic of the AI Algorithm

This section discusses the logic the algorithm follows to determine if the output is below or above normal.

### 1. Loading Historical Data

The first step in the algorithmic flow is to read the historical dataset containing data from 2018-01-01 to 2024-02-28.

### 2. Preprocessing

After collecting the historical data, the dataset is manipulated to retain only the necessary information. The datetime and 'weeknummer' (week number) are converted to the correct datatype, and a variable containing only the relevant data is created.

The dataset is split into two parts:

- Winter and spring data
- Summer and autumn data

The reason for this split is that using the entire dataset as a single part would result in the algorithm not reacting well due to the significant temperature differences between spring/winter and summer/autumn. Therefore, the dataset was divided into two parts.

### 3. Model Preparation

Two types of models are used in this application: one based on spring and winter data, and another based on summer and autumn data.

A subdataset for these two periods was created to easily distinguish between warmer and colder months.

### 4. Model Building

In this part, the entire AI algorithm based on the Gaussian equation was developed. The epsilon value in the algorithm ensures that any input above or below a certain threshold is classified as an anomaly.

## Setting up the application

1. The first step is to install the required library's inside the requirements.txt file. You can install all this library's using 'pip install -r requirements.txt'.

2. You need to give permissions to the start_container.sh script inside an Linux environment to run the application. You can use 'chmod +x ./start_container.sh' inside the terminal to give permissions to use this script.

3. Build the Dockerfile like in the example 'docker build -t apaqe .'

4. Run the docker container. You can do this like in this example 'docker run -d --name apaqe_ai_container apaqe'

5. Give the 'start container' script the necessary rights to be executed like in this example 'chmod +x /entrypoint.sh' After that run the script 'sudo ./start_container.sh'1. The first step is to install the required libraries listed in the `requirements.txt` file. This can be done using the command `pip install -r requirements.txt`.

2. Permissions need to be given to the `start_container.sh` script to run the application in a Linux environment. Use the command `chmod +x ./start_container.sh` in the terminal to grant the necessary permissions.

3. Build the Dockerfile using the following command: `docker build -t apaqe .`

4. Run the Docker container with the command: `docker run -d --name apaqe_ai_container apaqe`

5. Grant the 'start container' script the necessary rights to be executed using the command: `chmod +x /entrypoint.sh`. After that, run the script with: `sudo ./start_container.sh`


### Prefect

Prefect Server is a tool for developers to track the data flow across various functions in code. It is also excellent for monitoring when specific functions fail within the workflow.

The Prefect Server is deployed simultaneously with the main application script and can be accessed at http://127.0.0.1:4200.

### Observing the Container Activity

There are two easy ways to track the results of the Docker container:

- Use the command `docker ps -a` to check the activity of the container and verify that it is running as expected every 24 hours.

- Use the command `docker logs -f apaqe_ai_container` to view the results of the Docker container and ensure that everything is working properly.

