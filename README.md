[![Licence](https://img.shields.io/cocoapods/l/LivenessSDK?color=red&logo=red)](https://img.shields.io/cocoapods/l/LivenessSDK?color=red&logo=red)

# Face Match SDK

## Introduction
Brought to you by FaceX.io, this docker image can now be used to host FaceX Face Match API on your server.

## Index
* [Setup](#setup)
* [Interacting with the server](#interacting-with-the-server)
* [Face Detection](#face-detection)
  * [Endpoint](#endpoint)
  * [Response](#response-)
* [Face Match](#face-match)
  * [Endpoint](#endpoint-)
  * [Query Parameters](#query-parameters-)
  * [Body Parameters](#body-parameters-) 
  * [Response](#response--1)
* [Errors and Warnings](#error-and-warnings)
  * [Server Side](#server-side-)
  * [API error Responses](#api-error-responses)

  
## Setup
1. Download the docker image file provided as a tar archive `face_api.tar.gz`

2. Verify that docker is installed and ready to go.
    ```
    docker --version
    ```
    You can also run a hello-world image to test the installation.
    ```
    docker run hello-world
    ```
3. Load the docker image from the tar archive.
    ```
    docker image load < face_api.tar.gz
    ```
4. Verify that the image is loaded by listing docker images.
    ```
    docker images
    ```
5. Run the docker image
    ```
    docker run -it --rm -p 5000:5000 -e USER_KEY=<license_key> facex/api:latest
    ```
    >`-p` &nbsp; : &nbsp; tag chooses the port to serve on  
    > To choose a custom port `xxxx`. Provide the `-p` tag as:  
    > `-p 5000:xxxx`


6. FaceX API server will start serving on `0.0.0.0:5000`. On a local machine this would be `localhost:5000` or `127.0.0.1:5000`


## Interacting with the server:

You can interact with the server using the exposed rest API.
Documentation for which is provided below. 

Sample codes for Python, can be found in `/sample_codes`


## Face Detection

#### Endpoint:
**POST** `/face_detect`

#### Parameters :
parameter| importance | field | description |
--- | --- |---|----|
img | required |file |image file containing a face to perform detection on|

#### Response :
If face is found, the detection score `confidence` and the Face bounding box `bboxes` is returned.
```
{
    "success": true,
    "data": [
        {
            "confidence": 0.99971056,
            "bboxes": [
                138,
                170,
                327,
                307
            ]
        }
    ]
}
```
Incase, there are multiple faces:
```
{
    "success": true,
    "data": [
        {
            "confidence": 0.9986617,
            "bboxes": [
                74,
                55,
                223,
                157
            ]
        },
        {
            "confidence": 0.9983609,
            "bboxes": [
                56,
                494,
                216,
                597
            ]
        },
        {
            "confidence": 0.8113603,
            "bboxes": [
                75,
                280,
                209,
                380
            ]
        }
    ]
}
```
If no face found, `data` field is empty.
```
{
    "success": true,
    "data": []
}
```

## Face Match

#### Endpoint :
**POST** `/face_match`

### Query Parameters :
parameter|importance |Options|default| description |
---|---|---|---|---|
det |optional|0 or 1|1|whether to perform face detection or not. If `0` server expects you to provide a croped face|

> **Note:**  
> Query params are appended in the url  
> Example : `http://localhost:5000/face_match?det=1`

#### Body Parameters :
parameter| importance | field | description |
--- | --- |---|----|
img_1 | required |file |image file containing a face|
img_2 | required |file |image file containing a face|

#### Response :

On face match :
```
{
    "success": true,
    "match": "match",
    "distance": 0.78127575
}
```
On no match : 
```
{
    "success": true,
    "match": "no match",
    "distance": 1.015771
}
```

## Error and Warnings 

### Server Side :

Error Message | Description |
---|---|
Error verifying license|Connection issue - Please check your internet connection|
License expired|Allowed API call credits exhausted

### API Error Responses:

API Error messages are returned with `success` field `False` and the `message` field carries the error message.

Error Message | Code |Description |
---|---|---|
Error Fetching Images |400| Input format is incorrect|
No face detected in image_1 |400|Either the image contains no face or a face could not be detected
Multiple faces in image_1|400|Images should only contain a single face
No face detected in image_2|400|Either the image contains no face or a face could not be detected
Multiple faces in image_2|400|Images should only contain a single face
