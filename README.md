
# Damage Detection Service API Documentation

## Overview

This API provides a damage detection service for images. It uses machine learning models for prediction and is built using Flask.

## Base URL

All API requests should be made to:

```
https://<your-server-ip>/
```

### Rate Limiting

The API is rate-limited to 5 requests per minute per IP address.

## Routes

### 1. Hello World

- **Endpoint**: `/`
- **Method**: `GET`
- **Response**: A simple text greeting.

#### Example Request

```bash
curl -X GET "https://<your-server-ip>/"
```

#### Example Response

```
Hello, World!
```

### 2. Predict Damage

- **Endpoint**: `/predict`
- **Method**: `POST`
- **Request Body**: Multipart form data with a field named `file` containing the image to be analyzed.
- **Response**: JSON object with predictions and output image in base64 format.

#### Example Request

Using `curl`:

```bash
curl -X POST "https://<your-server-ip>/predict" -F "file=@<path-to-image>"
```

Using Python with `requests`:

```python
import requests

files = {'file': open('<path-to-image>', 'rb')}
response = requests.post('https://<your-server-ip>/predict', files=files)
```

#### Example Response

```json
{
  "predictions": [
    {
      "bbox": [106.45, 116.19, 174.25, 247.29],
      "label": "dent",
      "score": 0.78
    },
    {
      "bbox": [388.59, 178.55, 432.50, 193.75],
      "label": "scratch",
      "score": 0.50
    }
  ],
  "output_image_base64": "<base64_encoded_image>"
}
```

## Client Examples

### JavaScript using Fetch API

```javascript
async function makePrediction() {
  const url = 'https://<your-server-ip>/predict';
  const formData = new FormData();
  
  const inputElement = document.querySelector('#fileInput');
  formData.append('file', inputElement.files[0]);

  const response = await fetch(url, {
    method: 'POST',
    body: formData
  });

  if (response.ok) {
    const jsonResponse = await response.json();
    console.log(jsonResponse);
  } else {
    console.error(`Error: ${response.status}`);
  }
}
```

### PHP using cURL

```php
<?php
$curl = curl_init();

$url = "https://<your-server-ip>/predict";
$filePath = "<path-to-image>";

$cfile = curl_file_create($filePath, 'image/jpeg', 'test_name');

$postData = array(
  'file' => $cfile
);

curl_setopt_array($curl, array(
  CURLOPT_URL => $url,
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_POST => true,
  CURLOPT_POSTFIELDS => $postData,
  CURLOPT_httpsHEADER => array(
    'Content-Type: multipart/form-data'
  )
));

$response = curl_exec($curl);

if (curl_error($curl)) {
    $error_msg = curl_error($curl);
    echo "cURL Error: $error_msg";
} else {
    $jsonResponse = json_decode($response, true);
    print_r($jsonResponse);
}

curl_close($curl);
?>
```


### React Native Example using Fetch API

Let's assume that you have a file URI that points to the image you want to upload. Here's how you can perform the API call:

```javascript
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, ActivityIndicator } from 'react-native';

const DamageDetectionComponent = () => {
  const [loading, setLoading] = useState(false);
  const [predictions, setPredictions] = useState(null);

  const makePrediction = async () => {
    setLoading(true);

    // Initialize FormData and append the image file
    let formData = new FormData();
    formData.append('file', {
      uri: 'file:///path/to/image.jpg', // replace with actual file path
      name: 'image.jpg',
      type: 'image/jpeg',
    });

    // Fetch API for POST request
    try {
      const response = await fetch('http://<your-server-ip>:8080/predict', {
        method: 'POST',
        headers: {
          'Content-Type': 'multipart/form-data',
        },
        body: formData,
      });

      // Handle response
      if (response.ok) {
        const jsonResponse = await response.json();
        setPredictions(jsonResponse.predictions);
      } else {
        console.error(`Server responded with status: ${response.status}`);
      }
    } catch (error) {
      console.error('Error:', error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <View>
      <TouchableOpacity onPress={makePrediction}>
        <Text>Make Prediction</Text>
      </TouchableOpacity>

      {loading && <ActivityIndicator size="large" color="#0000ff" />}

      {predictions && (
        <View>
          <Text>Predictions:</Text>
          {predictions.map((pred, index) => (
            <Text key={index}>
              Label: {pred.label}, Score: {pred.score}
            </Text>
          ))}
        </View>
      )}
    </View>
  );
};

export default DamageDetectionComponent;

```

Note that you need to replace `<your-server-ip>` and `<path-to-image>` with the appropriate values.
