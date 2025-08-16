# Weather Backend on OpenShift

## 1. Purpose
This project is a Node.js backend service that provides weather data for a specified city using the OpenWeatherMap API. It is designed to run on OpenShift and demonstrates how to securely incorporate API secrets using OpenShift Secrets.

## 2. Structure of the Project
```
weather-backend/
├── .env                  # Local environment variables (not committed)
├── backend-openshift.yaml # OpenShift deployment configuration
├── Dockerfile            # Container build instructions
├── node_modules/         # Installed dependencies
├── package.json          # Project metadata and dependencies
├── package-lock.json     # Dependency lock file
├── README.md             # Project documentation
├── server.js             # Main Node.js backend code
```

## 3. Steps to Incorporate Secrets Using OpenShift Dev Sandbox

### Step 1: Create a Secret in OpenShift
Store your OpenWeatherMap API key in a secret named `openweathermap-api-key`:
```sh
oc create secret generic openweathermap-api-key --from-literal=OPENWEATHER_API_KEY=your_api_key_here
```

### Step 2: Reference the Secret in the Deployment
In `backend-openshift.yaml`, reference the secret in the container's environment:
```yaml
env:
	- name: OPENWEATHER_API_KEY
		valueFrom:
			secretKeyRef:
				name: openweathermap-api-key
				key: OPENWEATHER_API_KEY
```

### Step 3: Access the Secret in Node.js
The backend reads the API key from `process.env.OPENWEATHER_API_KEY`.
No need to hardcode secrets in your code or repository.

### Step 4: Local Development
Create a `.env` file in the project root:
```env
OPENWEATHER_API_KEY=your_api_key_here
PORT=3000
```
Run the server:
```sh
node server.js
```

## 4. Key Code Snippets

### Load Environment Variables
```js
require('dotenv').config();
```

### Express Server Setup
```js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 8080;
app.listen(PORT, '0.0.0.0', () => {
	console.log(`Backend server listening on port ${PORT}`);
});
```

### Weather API Endpoint
```js
app.get('/api/weather', async (req, res) => {
	const city = req.query.city;
	// ...fetch weather from OpenWeatherMap and respond...
});
```

### Using OpenShift Secrets
In `backend-openshift.yaml`:
```yaml
env:
	- name: OPENWEATHER_API_KEY
		valueFrom:
			secretKeyRef:
				name: openweathermap-api-key
				key: OPENWEATHER_API_KEY
```

In `server.js`:
```js
const OPENWEATHER_API_KEY = process.env.OPENWEATHER_API_KEY;
```

## Local Development
- Create a `.env` file in the project root:
	```env
	OPENWEATHER_API_KEY=your_api_key_here
	PORT=3000
	```
- Run the server:
	```sh
	node server.js
	```

## API Usage
- Example request:
	```sh
	curl "http://localhost:3000/api/weather?city=London"
	```
![weather_london](https://github.com/user-attachments/assets/07330d98-791a-4622-98ba-d376f7af565b)



## OpenShift Deployment Steps
1. Push your code to the GitHub repository.
2. Ensure your `backend-openshift.yaml` is configured as shown above.
3. Deploy using OpenShift console or CLI.

---
For more details, see the comments in `server.js` and `backend-openshift.yaml`.
