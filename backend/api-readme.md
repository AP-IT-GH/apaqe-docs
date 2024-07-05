# FASTAPI DOC

When testing make sure db/other endpoints in routes are available otherwise temporarily comment out the routes you dont need.

## Features
- **VPN Management:** Create, delete, and manage WireGuard VPN gateways.
- **Sensor Data Handling:** Collect and analyze sensor data across various metrics.
- **Sensor Adding:** Adding new sensors to the postgresdb.
- **AI Model data** Sending and receiving data to an AI model.

## Installation and Setup

### Prerequisites
- Python 3.8 or newer
- Docker (container deployment)

### Getting Started
1. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

2. **Environment Setup:**
   - Copy the `.env.example` to `.env` and modify it to suit your environment settings.

3. **Run the application:**
   ```bash
   uvicorn main:app --reload  # Development mode
   uvicorn main:app --host 0.0.0.0 --port 8000  # Production mode
   ```

## API Endpoints
Each endpoint is documented with its purpose, required parameters, and example responses.

## Endpoints

### Swagger UI
Because we use *FastAPI* you can see the Swagger UI at this endpoint.
- **URL:** `/docs`

### Root
- **URL:** `/`
- **Method:** GET
- **Description:** Basic endpoint to check if the server is running.
#### Ping endpoint
- **URL:** `/api/ping`
- **Method:** GET 
- **Description:** Ping endpoint to check if API is running. Returns a JSON response with a "message" key containing "pong".
- **Required:** None
- **Returns:** ```{"message":"pong"}```
### Endpoints wg_easy
*This FastAPI route (`wgeasy_router`) provides endpoints for interacting with a WireGuard Easy API.*

### Endpoints wg_easy
*This FastAPI route (`wgeasy_router`) provides endpoints for interacting with a WireGuard Easy API.*
#### Wireguard Stats Endpoint
- **URL:** `/api/wgeasy/stats`
- **Method:** GET
- **Description:** Fetches WireGuard client statistics from the WireGuard Easy API. Returns a JSON response with "wireguardStats" containing the fetched statistics.
- **Required:** None
- **Returns:** ```{"wireguardStats" : results[]}```
#### Delete Gateway Endpoint
- **URL:** `/api/wgeasy/gateway/{client_id}`
- **Method:** DELETE
- **Description:** Deletes a WireGuard gateway by client ID. Requires the client ID as a path parameter. Returns a JSON response with a "success" key indicating the success of the deletion operation.
- **Required:**  ```client_id: str```
- **Returns:** ```{"success" : "true"}```
#### Post Gateway Endpoint
- **URL:** `/api/wgeasy/gateway`
- **Method:** POST
- **Description:** Creates a new WireGuard gateway with a specified name. Requires a JSON payload with a "name" key indicating the gateway name. Returns a JSON response with a "success" key indicating the success of the creation operation.
- **Required:** ```{"name": "new_gateway_name"}```
- **Returns:** ``` {"success" : "true"}```
#### Get Configuration Endpoint
- **URL:** `/api/wgeasy/configuration/{client_id}`
- **Method:** GET
- **Description:** Fetches the configuration of a WireGuard client by client ID. Requires the client ID as a path parameter. Returns a JSON response with a "configuration" key containing the fetched configuration.
- **Required:** ```client_id: int```
- **Returns:** ``` {"configuration":"your_configuration"}```

### Endpoints Sensors
*All endpoints related to retrieving sensor data and registrating new sensors to postgresdb*
#### Retrieve specific sensor data
- **URL:** `/api/sensor/{sensorid}/{fields}/{start}/{stop}`
- **Method:** GET 
- **Description:** Retrieve specific sensor data. 
- **Required:** ```sensorID: int,fields: str, startdate: datetime, stopdate: datetime ```
- **Returns:** 
```
{
    "id": int,              altijd
    "temperature_C": float, optioneel
    "humidity": int,        optioneel
    "wind_avg_m_s": float,  altijd
    "wind_max_m_s": float,  altijd
    "wind_dir_deg": int     altijd
    "rain_mm", float        optineel
}
```
#### Retrieve all sensor data
- **URL:** `/api/sensor`
- **Method:** GET
- **Description:** Retrieve all sensor data
- **Required:** None
- **Returns:**
```
// Response body format, array of sensors
[{
	id: string, // The unique id of the sensor
	name: string, // The name of the sensor
	status: boolean // Calculated by querying influxdb and checking if last measurement is longer than three times the post interval. Contact Stijn & Hakim for questions
	img_src: string, // absolute url to image (www.image.com/image.png)
	creation_timestamp: number, // The unix timestamp of when the sensor was registered in the system
	group: {
		id: number, // The id of the group
		name: string // the name of the group
	},
	location: { // In decimal degrees notation (dd)
		lattitude: number,
		longitude: number,
	},
}]
```
#### Retrieve specific data
- **URL:** `/api/sensor/{id}`
- **Method:** GET
- **Description:** Retrieve specific data from a specific sensor.
- **Required:** ```id: int```
- **Returns:**
```
{
    id: int
    name: str
    status: bool
    img_src: str
    creation_timestamp: int
    group: {
		  id: number, // The id of the group
		  name: string // the name of the group
	  }
    location: { 
      // In decimal degrees notation (dd)
		  lattitude: number,
		  longitude: number,
	  }
}
```
#### Register new sensor device
- **URL:** `/api/sensor`
- **Method:** POST
- **Description:** Post new sensor data.
- **Required:** 
```
{
    // Request Body
    name: str
    lattitude: int
    longitude: int
    ttn_id: Optional[str]
    img_src: Optional[str]
    group_id: Optional[int]
}
- **Returns:** { "sensor_id": number }
```
#### Update sensor properties
- **URL:** `/api/sensor/{id}`
- **Method:** PATCH
- **Description:** Update sensor properties.
- **Required:** 
```
{
    // Request Body
    name: Optional[str] =  Field(None)
    img_src: Optional[str] = Field(None)
    group_id: Optional[int] = Field(None)
    lattitude: Optional[int] = Field(None)
    longitude: Optional[int] = Field(None)
}
```
- **Returns:** ```{"message" : "updated"}```
#### Retrieve aggregated sensordata
- **URL:** `/api/sensor/{sensorid}/{fields}/{start}/{stop}/{aggregateWindow}`
- **Method:** GET
- **Description:** Retrieve aggregated sensor data
- **Required:** ```sensorid: int, fields: str, start: datetime, stop: datetime, aggregateWindow: str```
- **Returns:** ```{"data" : result}```

### Sensor Groups
*All endpoints related to registering and retrieving sensor groups*
#### Retrieve existing sensor groups
- **URL:** `/api/groups`
- **Method:** GET 
- **Description:** Retrieve all sensor groups
- **Required:** None
- **Returns:** 
```
// Response body format, array of groups
[{
	id: number, // unique identifier of the group
	name: string, // name of the group
	members: number[] // list of ids of sensors that are in this group
}]
```
#### Register new sensor group
- **URL:** `/api/groups`
- **Method:** POST 
- **Description:** Create a new sensor group
- **Required:** 
```
// Request body
{
	name: string,
}
```
- **Returns:** ```{"msg": "created"}```
#### Update a sensor group
- **URL:** `/api/groups/{group_id}`
- **Method:** PATCH
- **Description:** Update a sensor group
- **Required:** 
  - Path Parameter: ```group_id: int```
  - 
  ```
  // Request body
  {
	  name: string
  }
  ```
- **Returns:** ```{"msg": "updated"}```
#### Add sensor to group
- **URL:** `/api/groups/{group_id}`
- **Method:** POST
- **Description:** Add a sensor to a group.
- **Required:**
  - Path Parameter: ```group_id: int``` 
  - 
  ```
  // Request Body
  {
    sensor_id: int
  }
  ```
- **Returns:** ```{"msg": "updated"}```
#### Delete sensor group
- **URL:** `/api/groups/{group_id}`
- **Method:** DELETE 
- **Description:** Delete a sensor group.
- **Required:** ```group_id: int```
- **Returns:** ```{"msg": "deleted"}```

### Sensor Group Statistics
#### Retrieve statistics of group
- **URL:** `/api/group/{id}/statistics` 
- **Method:** GET
- **Description:** Retrieves a list of the average, tracked statistics of the sensors in the specified group
- **Required:** ```id: int```
- **Returns:** 
```
  // Response body format, array of all different types of measurements
  {
	  name: string, // bvb 'Temperature',
	  unit: string, // bvb '°C'
	
	  value: number, // The current value of the sensor
	  yesterdayValue: number, // The average value of yesterday
	  averageWeeklyValue: number, // The average of 7 days age
	
	  percentChange: number, // Compared to the last 24 hours
	  increaseIsGood: boolean, // Is an increase in this value a good or bad thing. Ex: an increase in co2 particles is bad, so false
  }
```

#### Retrieve statistics of a sensor
- **URL:** `/api/sensor/{id}/statistics` 
- **Method:** GET
- **Description:** Retrieves a list of the average, tracked statistics of the specific sensor
- **Required:** ```id: int```
- **Returns:** 
```
  // Response body format, array of all different types of measurements
  {
	  name: string, // bvb 'Temperature',
	  unit: string, // bvb '°C'
	
	  value: number, // The current value of the sensor
	  yesterdayValue: number, // The average value of yesterday
	  averageWeeklyValue: number, // The average of 7 days age
	
	  percentChange: number, // Compared to the last 24 hours
	  increaseIsGood: boolean, // Is an increase in this value a good or bad thing. Ex: an increase in co2 particles is bad, so false
  }
```

### Notifications
*All endpoints related to notifications*
#### Retrieve all notifications
- **URL:** `/api/notifications` 
- **Method:** GET
- **Description:** Retrieve all notifications
- **Required:** None
- **Returns:** 
```
{
  // Response body, array of notifications
	notifications: [{
		id: number,
		timestamp: number, // unix timestamp, the moment when the notification is created
		device: number, // Device ID
		message: string, // the message of the notification
		severity: number, // number from 1-3 (1 = check it out, 2 = semi critical, 3 = WAKE THE FUCK UP)
	}],
	totalPages: number,
}
```
#### Create notification
- **URL:** `/api/notifications` 
- **Method:** POST
- **Description:** Create a new notification.
- **Required:**
```
{
    timestamp: int
    message: str
    severity: int
}
```
- **Returns:** ```{"msg": "done"}```
#### Delete notifications
- **URL:** `/api/notifications/{notification_id}`
- **Method:** DELETE
- **Description:** Delete a notification.
- **Required:** ```notification_id: int```
- **Returns:** ```{"msg": "done"}```

### AI Related endpoints

#### Add prediction
- **URL:** `/api/forecast/new_forecast` 
- **Method:** POST
- **Description:** Add forecast to the database.
- **Required:**
```
{
    forecast: int
    forecast_date: int
}
```

- **Returns:**
```
{
  "forecast" : number,
  "forecast_date" : number
}
```

#### Retreive temperature data
- **URL:** `/api/forecast/get_data` 
- **Method:** GET
- **Description:** Get temperature data of the last 2 days.
- **Required:**
```
{
    id: SENSOR_ID
}
```

#### Send sensor data
- **URL:** `/api/forecast/weather/{start}/{stop}` 
- **Method:** GET
- **Description:** Send all weatherdata from given time input.
- **Required:** ```start: datetime, stop: datetime```
- **Returns:** ```{"data" : result}```


### Authorization related endpoints
#### login endpoint

- **URL:** `/api/auth/login`
- **Method:** GET
- **Description:** Redirects the user to the Authentik authorization endpoint.
- **Required:** Request data from Authentik.

- **Example request:**
```http
GET /api/auth/login
```

- **Example response:**
```
Redirects to the Authentik authorization URL.
```

### Authorization from login

- **URL:** `/api/auth/authorize`
- **Method:** FastAPI ROUTE
- **Description:** Handles the callback from Authentik and stores the user's information in the session.
- **Required:** `\`
- **Example request:**
```http
GET /api/auth/authorize
```
- **Example response:**
```json
{
  "access_token": "<access_token>",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "<refresh_token>",
  "userinfo": {
    "iss": "https://s140013.devops-ap.be/application/o/api/",
    "sub": "John Doe",
    "aud": "",
    "exp": 9999999999,
    "iat": 9999999999,
    "auth_time": 9999999990,
    "acr": "goauthentik.io/providers/oauth2/default",
    "nonce": "",
    "email": "John Doe",
    "email_verified": true,
    "uid": "JohnDoe1"
  }
}
```

### Example protected endpoint

- **URL:** `/api/auth/protected`
- **Method:** GET
- **Description:** A protected endpoint that requires a valid OAuth2 token to access.
- **Required:** 
- **Example request:** 
```http
GET /api/auth/protected
Authorization: Bearer <access_token>
```
- **Example response:**
```json
{
  "message": "This is a protected endpoint!"
}
```

### **Dependencies**

### `token_required`

A dependency function that validates the OAuth2 token provided in the request header.

#### Example Usage
```python
async def protected(request: Request, token: str = Depends(token_required)):
    return {"message": "This is a protected endpoint!"}
```

## Error Handling

The authentication endpoints handle the following errors:
- **401 Unauthorized:** If the token is missing or invalid.

### Export Data Endpoint

- **URL:** `/api/export`
- **Method:** GET
- **Description:** Retrieves a CSV file containing the data within the specified date range. The CSV file is streamed back as a text/csv response with a Content-Disposition header for download.
- **Parameters:**
  - `start_date (optional):` Start date and time (ISO 8601 format) of the data range. Defaults to 72 hours ago from the current time if not provided.
  - `end_date (optional):` End date and time (ISO 8601 format) of the data range. Defaults to the current time if not provided.
  - `page (optional):` Page number for pagination. Defaults to 1.
  - `page_size (optional):` Number of records per page. Defaults to 100.
- **Returns:**
  - 200 OK: Returns a CSV file containing the data within the specified date range.
  - 400 Bad Request: If the request parameters are invalid or missing.
  - 404 Not Found: If no data is found for the specified date range.
  - 500 Internal Server Error: If there's an issue fetching data from InfluxDB.
- **Example Usage:**

  - Export data from last 24h:

    `GET /api/export?start_date=2024-07-03T00:00:00Z&end_date=2024-07-04T00:00:00Z`

  - Export data with pagination (fetching page 2 with 50 records per page):

    `GET /api/export?start_date=2024-07-01T00:00:00Z&end_date=2024-07-04T00:00:00Z&page=2&page_size=50`