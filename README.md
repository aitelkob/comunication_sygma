
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
