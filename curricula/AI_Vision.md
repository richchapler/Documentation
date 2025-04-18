# Lab: AI Vision

<img src="https://github.com/user-attachments/assets/2158f09e-207e-4b86-b45d-5d381090f8d2" width="1000" />

## Introduction

Azure AI Vision is a cloud-based service from Microsoft that leverages advanced algorithms to analyze images and extract valuable information. It encompasses a range of capabilities—from Optical Character Recognition (OCR) and face detection to comprehensive image analysis and video indexing—all designed to solve real-world problems quickly and effectively.

## Sections

### Prepare Resources  
* Set up the necessary Azure services  
* Configure your environment to securely manage API credentials using environment variables

### Optical Character Recognition (OCR)  
* Learn how to extract text from images  
* Analyze and interpret the JSON output to understand how text is detected and structured

### Face Detection and Analysis  
* Detect and analyze human faces in images, including advanced topics like liveness detection and portrait processing  
* Understand the nuances of facial recognition to support real-world identity verification scenarios

### Image Analysis  
* Explore the extraction of meaningful metadata from images through techniques like tagging, caption generation, and smart cropping  
* Discover how to enhance user experiences by leveraging detailed insights into image content

### Video Indexer  
* Utilize Azure AI Video Indexer to analyze video content, extract metadata, and generate insights from multimedia data  
* Understand how to manage video uploads, process indexing, and interpret results for comprehensive video analytics

------------------------- -------------------------

## Exercise 1: Prepare Resources  
_Complete this exercise only if you intend to complete Pro-Code exercises_

### Azure
In "East US" region (or other region that supports AI Vision resources), instantiate:

* AI Services
* Application Registration (with Client Secret)
* Computer Vision
* Open AI
* Storage Account (general purpose v1)
* Video Indexer

-------------------------

### Development Environment
Prepare a machine with the following artifacts:

* [PowerShell](https://richchapler.github.io/AzureSolutionsDocumentation/artifacts/PowerShell.html)
* [Visual Studio Code](https://richchapler.github.io/AzureSolutionsDocumentation/artifacts/VisualStudioCode.html) with [Jupyter](https://richchapler.github.io/AzureSolutionsDocumentation/artifacts/VisualStudioCode_Jupyter.html)
* [Python (including Virtual Environment)](https://richchapler.github.io/AzureSolutionsDocumentation/artifacts/Python.html) with the [dotenv module](https://richchapler.github.io/AzureSolutionsDocumentation/artifacts/Python_DotEnvModule.html)

-------------------------

#### Azure Identity Module

Open Visual Studio Code, navigate to View >> Terminal, then execute the following command:

```powershell
pip install azure-identity
```

Once installed, you'll be able to import `DefaultAzureCredential` in your code to obtain access tokens via your application registration credentials (rather than using keys).

-------------------------

#### Python Notebook

Open Visual Studio Code and create a new Jupyter Notebook by selecting File > New File, then searching for and selecting Jupyter Notebook. Save the new file as `ai_vision.ipynb` in `c:\myProject`.

-------------------------

#### Environment Variables

Create and initialize the `.env` file from within your notebook:

Add a Markdown cell with the following annotation:
```markdown
## Create `.env` File  
Create (if necessary) and prompt for update with API credentials and image path.
```

Then add a Code cell and paste the following code:
```python
import os
env_file = ".env"
if not os.path.isfile(env_file):
    open(env_file, "w").close()
    print(f".env file created at {os.path.abspath(env_file)}")
else:
    print(f".env file found at {os.path.abspath(env_file)}")
```

Execute the cell. The output should indicate whether the `.env` file was created or already exists.

Open the `.env` file and append the following lines:
```text
COMPUTER_VISION_API_KEY={Computer Vision, KEY 1}
COMPUTER_VISION_ENDPOINT=https://{prefix}cv.cognitiveservices.azure.com/

VIDEO_INDEXER_ACCOUNT_ID={Video Indexer, Account ID}
VIDEO_INDEXER_LOCATION={Video Indexer, Location}
VIDEO_INDEXER_TOKEN={Video Indexer, Access Token}
```

Add a Markdown cell with the following annotation:
```markdown
## Load Environment Variables  
This cell loads the configuration settings from your `.env` file, ensuring that your API credentials and file paths are available for subsequent code cells.
```

Add a Code cell to load these environment variables using the dotenv module:
```python
import os
from dotenv import load_dotenv

env_file = ".env"
load_dotenv(env_file)

COMPUTER_VISION_API_KEY = os.getenv("COMPUTER_VISION_API_KEY")
COMPUTER_VISION_ENDPOINT = os.getenv("COMPUTER_VISION_ENDPOINT")
VIDEO_INDEXER_ACCOUNT_ID = os.getenv("VIDEO_INDEXER_ACCOUNT_ID")
VIDEO_INDEXER_LOCATION = os.getenv("VIDEO_INDEXER_LOCATION")
VIDEO_INDEXER_TOKEN = os.getenv("VIDEO_INDEXER_TOKEN")
```

Execute the cell and restart the kernel to ensure that the new variable is correctly loaded.

------------------------- -------------------------
------------------------- -------------------------

## Exercise 2: Optical Character Recognition (OCR)  

This exercise demonstrates how to use Azure AI Vision, Optical Character Recognition (OCR) to extract text from images.

Navigate to [Azure AI | Vision Studio](https://portal.vision.cognitive.azure.com/), log in with your Azure credentials, and then click on the "Optical character recognition" tab.

<img src="https://github.com/user-attachments/assets/f008f817-51b8-4a7c-b230-19803782bde7" width="800" title="Snipped February 14, 2025" />

### Extract Text from Images

#### Low Code

<img src="https://github.com/user-attachments/assets/9d70618d-2d50-4ebc-81a8-55a9bb8be4bc" width="800" title="Snipped February 14, 2025" />

Check "I acknowledge...", click "Browse for a file" and then click "Please select a resource".

<img src="https://github.com/user-attachments/assets/3da8778e-7b78-4ab7-9aa7-5b9e5fe15edc" width="800" title="Snipped February 14, 2025" />

Complete the "Select an AzureResource" form and then click "Confirm".

Iteratively click the samples to the right of the "Drag and drop a file..." box.

<img src="https://github.com/user-attachments/assets/d50659f8-4762-4c5f-a6da-95505f2645be" width="800" title="Snipped February 14, 2025" />

Review results on the "Detected attributes" / "JSON" tabs.

------------------------- -------------------------

### Pro Code

#### Update Environment Variables

Append the following line to your `.env` file:
```text
IMAGEPATH_OCR=C:\myProject\ocr.jpg
```

Append the following code to the "Load Environment Variables" code in the `ai_vision.ipynb` notebook:
```python
IMAGEPATH_OCR = os.getenv("IMAGEPATH_OCR")
```

Re-execute "Load Environment Variables" code and restart the kernel.

-------------------------

#### Add Demonstration Code

Click "+ Markdown" and paste the following annotation into the resulting cell:
```markdown
## Extract Text from Images
Optical Character Recognition (OCR)
```

Render the Markdown cell by clicking the checkmark in the upper-right controls.

Click ""+ Code"" and paste the following code into the new cell:
```python
import os
import requests

def perform_ocr(image_path):
    with open(image_path, "rb") as f:
        image_data = f.read()
    url = f"{COMPUTER_VISION_ENDPOINT.rstrip('/')}/computervision/imageanalysis:analyze?api-version=2023-02-01-preview&features=read"
    headers = {
        "Ocp-Apim-Subscription-Key": COMPUTER_VISION_API_KEY,
        "Content-Type": "application/octet-stream"
    }
    try:
        response = requests.post(url, headers=headers, data=image_data)
        response.raise_for_status()
        return response
    except requests.exceptions.RequestException as e:
        print("OCR error:", e)
        return None

if IMAGEPATH_OCR and os.path.isfile(IMAGEPATH_OCR):
    res = perform_ocr(IMAGEPATH_OCR)
    if res:
        print(res.json())
else:
    print("IMAGEPATH_OCR is not defined or the file does not exist.")
```

Execute cell and review the returned JSON result.

------------------------- -------------------------

##### Expected Result  
_Note: JSON formatted and abbreviated_

```json
{
  "readResult": {
    "stringIndexType": "TextElements",
    "content": "NATIONAL IDENTITY CARD\nBIOMETRICS\nNAME: John Doe\nSEX: Male\nHAIR: Brown\nHEIGHT: 5'10\"\nWEIGHT: 165\nBORN: 13 Dec 1981\nDRIVERS LICENSE: XXT55340H59\n1003A2- 107624-( *- 102) 440u 28878976-(tf. 7 -- i9#3.c)",
    "pages": [
      {
        "height": 3528.0,
        "width": 5300.0,
        "angle": 0.1194,
        "pageNumber": 1,
        "words": [
          {
            "content": "NATIONAL",
            "boundingBox": [1456.0, 909.0, 1932.0, 906.0, 1930.0, 1010.0, 1453.0, 1012.0],
            "confidence": 0.995,
            "span": {"offset": 0, "length": 8}
          },
          ...
        ]
      }
    ],
    "styles": [],
    "modelVersion": "2022-04-30"
  },
  "modelVersion": "2023-02-01-preview",
  "metadata": {"width": 5300, "height": 3527}
}
```

------------------------- -------------------------
------------------------- -------------------------

## Exercise 3: Face

This exercise demonstrates how to use Azure AI Vision, Face to detect and analyze human faces in images.

Navigate to [Azure AI | Vision Studio](https://portal.vision.cognitive.azure.com/), log in with your Azure credentials, and then click on the "Face" tab.

<img src="https://github.com/user-attachments/assets/ced5f919-80b9-40ad-83dd-61da99015b68" width="800" title="Snipped February 18, 2025" />

### Detect Faces in an Image

#### Low Code

<img src="https://github.com/user-attachments/assets/d78b53a5-aca9-40aa-8589-c1c7d0baa362" width="800" title="Snipped February 18, 2025" />

Iteratively click the samples to the right of the "Drag and drop a file..." box.

<img src="https://github.com/user-attachments/assets/8074b06b-9b85-4235-8992-abbe7942ce04" width="800" title="Snipped February 18, 2025" />

Review results on the "Detected attributes" / "JSON" tabs.

-------------------------

#### Liveness Detection

<img src="https://github.com/user-attachments/assets/d8f00fd3-c59c-4182-9ae9-f082bba05d84" width="800" title="Snipped February 18, 2025" />

Click "Face Liveness only" and then "Try Out".

<img src="https://github.com/user-attachments/assets/029518de-efc6-49ed-86c6-63c813bd6894" width="800" title="Snipped February 18, 2025" />

Click "Apply for access".

<img src="https://github.com/user-attachments/assets/3b7d6f98-a599-411e-8db3-72b62d1bb017" width="800" title="Snipped February 18, 2025" />

Complete and submit the form.

<img src="https://github.com/user-attachments/assets/b0d52333-bcb5-4227-849c-eace366d3fa6" width="800" title="Snipped February 18, 2025" />

The "Azure AI Gating Team" will email and promise to respond in ~10 business days.

-------------------------

#### Portrait Processing

<img src="https://github.com/user-attachments/assets/9f4ed2f0-dd8f-4c93-af7c-37c190b14df5" width="800" title="Snipped February 18, 2025" />

Iteratively click the samples to the right of the "Drag and drop a file..." box.

<img src="https://github.com/user-attachments/assets/ea5b5082-3af4-4b79-8635-14447d980f1b" width="800" title="Snipped February 18, 2025" />

Review results on the "Detected attributes" / "JSON" tabs and review the generated portrait.

-------------------------

#### Photo ID Matching

<img src="https://github.com/user-attachments/assets/d8273d24-dd8f-4f8b-9585-dd4fc1ac4180" width="800" title="Snipped February 18, 2025" />

Iteratively click the samples to the right of the "Drag and drop a file..." box and compare to the Camera Preview.

------------------------- -------------------------
------------------------- -------------------------

## Exercise 4: Image Analysis

This exercise demonstrates how to use Azure AI Vision, Image Analysis to extract meaningful insights from images, including object detection, caption generation, and scene understanding.

Navigate to [Azure AI | Vision Studio](https://portal.vision.cognitive.azure.com/), log in with your Azure credentials, and then click on the "Image analysis" tab.

<img src="https://github.com/user-attachments/assets/5c5b68c7-6f82-43a1-b4fe-93cd3b648d14" width="800" title="Snipped March 14, 2025" />

### Search Photos with Image Retrieval

#### Low Code

<img src="https://github.com/user-attachments/assets/340088a0-c508-46a1-bb03-278d74c121f0" width="800" title="Snipped February 18, 2025" />

Iteratively click the sample image sets, select a retrieval query and click Search.

<img src="https://github.com/user-attachments/assets/687cc074-f25f-4c59-80fd-650c80d16312" width="800" title="Snipped February 18, 2025" />

Consider trying your own image.

------------------------- -------------------------

#### Pro Code

#### Update Environment Variables

Append the following lines to your `.env` file:
```text
IMAGEPATH_SEARCH=C:\myProject\search.jpg
```

Append the following code to the "Load Environment Variables" code in the `ai_vision.ipynb` notebook:
```python
IMAGEPATH_SEARCH = os.getenv("IMAGEPATH_SEARCH")
```

Re-execute "Load Environment Variables" code and restart the kernel.

##### Add Demonstration Code

Click "+ Markdown" and paste the following annotation into the resulting cell:
```markdown
## Search Photos with Image Retrieval
Image Analysis
```

Render the Markdown cell then click "+ Code" and paste the following code into the new cell:
```python
import os
import requests

def search_photos_with_image_retrieval(image_path):
    with open(image_path, "rb") as image_file:
        image_data = image_file.read()

    headers = {
        "Ocp-Apim-Subscription-Key": COMPUTER_VISION_API_KEY,
        "Content-Type": "application/octet-stream"
    }

    # Use the "tags" feature to extract descriptive keywords for image retrieval.
    url = f"{COMPUTER_VISION_ENDPOINT}/computervision/imageanalysis:analyze?api-version=2023-02-01-preview&features=tags"
    
    response = requests.post(url, headers=headers, data=image_data)
    return response

if IMAGEPATH_SEARCH and os.path.isfile(IMAGEPATH_SEARCH):
    response = search_photos_with_image_retrieval(IMAGEPATH_SEARCH)
    print(response.json())
else:
    print("IMAGEPATH_SEARCH is not defined or the file does not exist. Please check your .env file.")
```

Execute cell and review the returned JSON result.

-------------------------

###### Expected Result  
_Note: JSON formatted and abbreviated_

```json
{
  "modelVersion": "2023-02-01-preview",
  "metadata": {
    "width": 398,
    "height": 340
  },
  "tagsResult": {
    "values": [
      {
        "name": "outdoor",
        "confidence": 0.9897938966751099
      },
      {
        "name": "tractor",
        "confidence": 0.986690878868103
      },
      {
        "name": "agriculture",
        "confidence": 0.9778040051460266
      },
      {
        "name": "farm",
        "confidence": 0.9688516855239868
      },
      {
        "name": "cash crop",
        "confidence": 0.9292412996292114
      },
      {
        "name": "agricultural machinery",
        "confidence": 0.9211045503616333
      },
      {
        "name": "plantation",
        "confidence": 0.9173258543014526
      },
      {
        "name": "crop",
        "confidence": 0.9134097099304199
      },
      {
        "name": "farmworker",
        "confidence": 0.8990883231163025
      },
      {
        "name": "soil",
        "confidence": 0.8958316445350647
      },
      {
        "name": "plant",
        "confidence": 0.8954752683639526
      },
      {
        "name": "plough",
        "confidence": 0.8804078102111816
      },
      {
        "name": "farmer",
        "confidence": 0.8800090551376343
      },
      {
        "name": "grass",
        "confidence": 0.8694881200790405
      },
      {
        "name": "mountain",
        "confidence": 0.8089953660964966
      },
      {
        "name": "ground",
        "confidence": 0.7287822365760803
      },
      {
        "name": "field",
        "confidence": 0.6489622592926025
      },
      {
        "name": "farm machine",
        "confidence": 0.5761915445327759
      }
    ]
  }
}
```

------------------------- ------------------------- -------------------------

### Add Dense Captions to Images

#### Low Code

<img src="https://github.com/user-attachments/assets/1c4581fe-36dc-4501-821d-c05390591c18" width="800" title="Snipped February 18, 2025" />

Iteratively click the samples to the right of the "Drag and drop a file..." box.

<img src="https://github.com/user-attachments/assets/fc0dd38d-1da9-4ae5-b457-88cb45ba8d02" width="800" title="Snipped February 18, 2025" />

Review results on the "Detected attributes" / "JSON" tabs.

------------------------- -------------------------

#### Pro Code

#### Update Environment Variables

Append the following line to your `.env` file:
```text
IMAGEPATH_DENSECAPS=C:\myProject\densecaps.jpg
```

Append the following code to the "Load Environment Variables" code in the `ai_vision.ipynb` notebook:
```python
IMAGEPATH_DENSECAPS = os.getenv("IMAGEPATH_DENSECAPS")
```

Re-execute "Load Environment Variables" code and restart the kernel.

##### Add Demonstration Code

Click "+ Markdown" and paste the following annotation into the resulting cell:
```markdown
## Add Dense Captions to Images
Image Analysis
```

Render the Markdown cell then click "+ Code" and paste the following code into the new cell:
```python
import os
import requests

def get_dense_captions(image_path):
   # Read the image file in binary mode
   with open(image_path, "rb") as image_file:
       image_data = image_file.read()
   
   # Define the request headers including your API key and content type
   headers = {
       "Ocp-Apim-Subscription-Key": COMPUTER_VISION_API_KEY,
       "Content-Type": "application/octet-stream"
   }
   
   # Use the "denseCaptions" feature to extract detailed captions from the image.
   url = f"{COMPUTER_VISION_ENDPOINT.rstrip('/')}/computervision/imageanalysis:analyze?api-version=2023-02-01-preview&features=denseCaptions"
   
   response = requests.post(url, headers=headers, data=image_data)
   return response

# Check if the IMAGEPATH_DENSECAPS is defined and the file exists.
if IMAGEPATH_DENSECAPS and os.path.isfile(IMAGEPATH_DENSECAPS):
   response = get_dense_captions(IMAGEPATH_DENSECAPS)
   print(response.json())
else:
   print("IMAGEPATH_DENSECAPS is not defined or the file does not exist. Please check your .env file.")
```

Execute cell and review the returned JSON result.

-------------------------

###### Expected Result  
_Note: JSON formatted and abbreviated_

```json
{
  "denseCaptionsResult": {
    "values": [
      {
        "text": "a man driving a tractor in a farm",
        "confidence": 0.7997510433197021,
        "boundingBox": {
          "x": 0,
          "y": 0,
          "w": 398,
          "h": 340
        }
      },
      ...
    ]
  },
  "modelVersion": "2023-02-01-preview",
  "metadata": {
    "width": 398,
    "height": 340
  }
}
```

------------------------- ------------------------- -------------------------

### Add Captions to Images

#### Low Code

<img src="https://github.com/user-attachments/assets/df89523d-e20b-4509-b3cb-091fff6f6405" width="800" title="Snipped February 18, 2025" />

Iteratively click the samples to the right of the "Drag and drop a file..." box.

<img src="https://github.com/user-attachments/assets/2a1bdd9f-aff9-412d-bb28-6a15e40a7204" width="800" title="Snipped February 18, 2025" />

Review results on the "Detected attributes" / "JSON" tabs.

------------------------- -------------------------

#### Pro Code

##### Update Environment Variables

Append the following line to your `.env` file:   
```text
IMAGEPATH_CAPTIONS=C:\myProject\caption.jpg
```

Append the following code to the "Load Environment Variables" code in the `ai_vision.ipynb` notebook:   
```python
IMAGEPATH_CAPTIONS = os.getenv("IMAGEPATH_CAPTIONS")
```

Re-execute "Load Environment Variables" code and restart the kernel.

##### Add Demonstration Code

Click "+ Markdown" and paste the following annotation into the resulting cell:
```markdown
## Add Captions to Images
Extract captions from an image using Azure AI Vision.
```

Add a Code cell with the following code:
```python
import os
import requests

def get_image_captions(image_path):
   # Open the image file in binary mode
   with open(image_path, "rb") as f:
       image_data = f.read()
   # Construct the API URL using the COMPUTER_VISION_ENDPOINT and the captions feature
   url = f"{COMPUTER_VISION_ENDPOINT.rstrip('/')}/computervision/imageanalysis:analyze?api-version=2023-02-01-preview&features=captions"
   headers = {
       "Ocp-Apim-Subscription-Key": COMPUTER_VISION_API_KEY,
       "Content-Type": "application/octet-stream"
   }
   # Make the POST request to the API
   response = requests.post(url, headers=headers, data=image_data)
   return response

# Check if the IMAGEPATH_CAPTIONS is defined and the file exists.
if IMAGEPATH_CAPTIONS and os.path.isfile(IMAGEPATH_CAPTIONS):
   response = get_image_captions(IMAGEPATH_CAPTIONS)
   print(response.json())
else:
   print("IMAGEPATH_CAPTIONS is not defined or the file does not exist. Please check your .env file.")
```
   
Execute cell and review the returned JSON result.

------------------------- ------------------------- -------------------------

### Detect Common Objects in Images

Not documented...

------------------------- ------------------------- -------------------------

### Extract Common Tags from Images

#### Low Code

<img src="https://github.com/user-attachments/assets/bd4656ac-989c-4f38-840f-5074db0b76bf" width="800" title="Snipped February 18, 2025" />

Choose a model, choose a language, and then iteratively click the samples to the right of the "Drag and drop a file..." box.

<img src="https://github.com/user-attachments/assets/b865ebbf-6596-40ba-990d-271057af81f8" width="800" title="Snipped February 18, 2025" />

Review results on the "Detected attributes" / "JSON" tabs.

------------------------- -------------------------

#### Pro Code

##### Update Environment Variables

Append the following line to your `.env` file:
```text
IMAGEPATH_TAGS=C:\myProject\tags.jpg
```

Append the following code to the "Load Environment Variables" code in the `ai_vision.ipynb` notebook:
```python
IMAGEPATH_TAGS = os.getenv("IMAGEPATH_TAGS")
```

Re-execute "Load Environment Variables" code and restart the kernel.

##### Add Demonstration Code
Click "+ Markdown" and paste the following annotation into the resulting cell:
```markdown
## Extract Common Tags from Images
Image Analysis
```

Add a Code cell with the following code:
```python
import os
import requests

def extract_image_tags(image_path):
   # Open the image file in binary mode
   with open(image_path, "rb") as f:
       image_data = f.read()
   
   # Construct the API URL using the COMPUTER_VISION_ENDPOINT and the tags feature
   url = f"{COMPUTER_VISION_ENDPOINT.rstrip('/')}/computervision/imageanalysis:analyze?api-version=2023-02-01-preview&features=tags"
   headers = {
       "Ocp-Apim-Subscription-Key": COMPUTER_VISION_API_KEY,
       "Content-Type": "application/octet-stream"
   }
   # Make the POST request to the API
   response = requests.post(url, headers=headers, data=image_data)
   return response

# Check if the IMAGEPATH_TAGS is defined and the file exists.
if IMAGEPATH_TAGS and os.path.isfile(IMAGEPATH_TAGS):
   response = extract_image_tags(IMAGEPATH_TAGS)
   print(response.json())
else:
   print("IMAGEPATH_TAGS is not defined or the file does not exist. Please check your .env file.")
```
   
Execute cell and review the returned JSON result.

------------------------- ------------------------- -------------------------

### Create Smart-Cropped Images

#### Low Code

<img src="https://github.com/user-attachments/assets/c76c1094-341d-4fa7-8d2b-dd74a94dcef6" width="800" title="Snipped February 18, 2025" />

Iteratively click the samples to the right of the "Drag and drop a file..." box.

<img src="https://github.com/user-attachments/assets/89d14157-7a61-4af0-a36f-55980aa8485c" width="800" title="Snipped February 18, 2025" />

Review results on the "Cropped image" tab and adjust aspect ratio to taste.

------------------------- -------------------------

#### Pro Code

##### Update Environment Variables

Append the following line to your `.env` file:
```text
IMAGEPATH_CROP=C:\myProject\crop.jpg
```

Append the following code to the "Load Environment Variables" code in the `ai_vision.ipynb` notebook:
```python
IMAGEPATH_CROP = os.getenv("IMAGEPATH_CROP")
```

Re-execute "Load Environment Variables" code and restart the kernel.

##### Add Demonstration Code

Click "+ Markdown" and paste the following annotation into the resulting cell:
```markdown
## Create Smart-Cropped Images
Image Analysis
```

Add a Code cell with the following code:
```python
import os
import requests
from PIL import Image
from io import BytesIO

def generate_smart_crop(image_path, width=400, height=400):
   # Open the image file in binary mode
   with open(image_path, "rb") as image_file:
       image_data = image_file.read()
   
   # Construct the API URL with the smart cropping parameters
   url = f"{COMPUTER_VISION_ENDPOINT.rstrip('/')}/computervision/v3.2/generateThumbnail?width={width}&height={height}&smartCropping=true"
   headers = {
       "Ocp-Apim-Subscription-Key": COMPUTER_VISION_API_KEY,
       "Content-Type": "application/octet-stream"
   }
   
   # Make the POST request to generate a smart-cropped image
   response = requests.post(url, headers=headers, data=image_data)
   response.raise_for_status()  # Ensure the request succeeded
   return response

# Check if the IMAGEPATH_CROP is defined and the file exists.
if IMAGEPATH_CROP and os.path.isfile(IMAGEPATH_CROP):
   response = generate_smart_crop(IMAGEPATH_CROP, width=400, height=400)
   # The response contains the binary image data of the cropped image.
   cropped_image = Image.open(BytesIO(response.content))
   cropped_image.show()  # This opens the cropped image in your default image viewer.
else:
   print("IMAGEPATH_CROP is not defined or the file does not exist. Please check your .env file.")
```
   
Execute cell and review the returned JSON result.

------------------------- -------------------------
------------------------- -------------------------

## Exercise 5: Video Indexer

With Azure AI Vision Spatial Analysis retiring on 30 March 2025, this exercise introduces you to Azure AI Video Indexer—a robust solution that offers richer video analysis capabilities. You will learn how to analyze videos both via the web portal and programmatically using the Video Indexer API.

#### Low Code

Navigate to [Azure AI Video Indexer](https://www.videoindexer.ai/account/login) and log in.

<img src="https://github.com/user-attachments/assets/a1ad5532-6daa-43e4-8ab8-fcf7d4bd123b" width="800" title="Snipped April, 2025" />

Complete the "Use of facial identification and recognition" form and then click "Accept".

> This popup is expected when you first access Azure's facial identification and celebrity recognition features. Azure requires users to fill out the request form and agree to the legal and compliance terms before enabling these limited access services. Once your use case is verified and access is granted, you should not see this prompt again on subsequent logins.

Click on "Upload" and on the resulting "Upload and index" popup, click "Browse for files"

<img src="https://github.com/user-attachments/assets/7c6bf86f-7fab-4315-85e6-0f783c35ec7f" width="800" title="Snipped April, 2025" />

> Consider using a sample video from a "free-to-use" site like the [NASA Image and Video Library](https://images.nasa.gov/)

Select a video file and click "Open".

<img src="https://github.com/user-attachments/assets/22d5bd38-0ea3-4043-9b90-d24f2507aed3" width="800" title="Snipped April, 2025" />

Complete the "Upload and index" popup form:
* File name – A display name for the uploaded video. You can keep the default name or change it to something more descriptive.
* Add files – An option to include additional video files if you want to upload more than one at a time.
* Privacy – Determines who can access the video and its insights. Options typically include private (only you can see it) or public (anyone with the link can view it).
* Streaming quality – Lets you select how the video is encoded for playback. Options might include single bitrate or adaptive bitrate, which adjusts to varying network conditions.
* Video source language – Specifies the main spoken language in the video. This setting helps with accurate transcription and speech analysis.
* Manage language model or speech models – Allows you to choose custom language or speech models if you have trained any, or you can use the default settings.
* Advanced settings – Contains additional configuration options like indexing presets or brand detection. These options are usually hidden by default unless you need to fine-tune the analysis.

Click "Review + upload".

<img src="https://github.com/user-attachments/assets/fc4d71d4-8eb5-453d-a82f-2886dfe77f04" width="800" title="Snipped April, 2025" />

Complete the resulting "Upload and index" form, and then click "Upload + index".

<img src="https://github.com/user-attachments/assets/d1e567f6-3ddf-455c-8d93-a506a484c1b4" width="800" title="Snipped April, 2025" />

Monitor progress.

<img src="https://github.com/user-attachments/assets/64caa1b7-07f5-4595-8bf2-36f7539f62fc" width="800" title="Snipped April, 2025" />

Once video processing is complete, you will receive notification email.

<img src="https://github.com/user-attachments/assets/b0f23583-3882-402b-a78c-6cf7d960f00b" width="800" title="Snipped April, 2025" />

Click on "Watch now >" in the email or directly on the video in the "Azure AI Video Indexer" interface.

<img src="https://github.com/user-attachments/assets/0f9a2967-e44d-44b1-9ebb-1469d817fb30" width="800" title="Snipped April, 2025" />

Review information about the video:
* Video Playback Area – shows “Space 32015-orig” playing with an image of the International Space Station  
* Insights Panel – includes several data points:  
  * Person – shows “1 person”  
  * Topic – shows “1 topic”  
  * Keywords – shows “3 keywords”  
  * Labels – shows “7 labels”  
  * Named entity – shows “1 named entity”
  * Scenes - shows "9 scenes" and a thumbnail strip

------------------------- -------------------------

#### Pro Code

#### Update Environment Variables

Append the following line to your `.env` file:
```text
VIDEO_INDEXER_SAMPLEPATH=C:\myProject\{filename}
```

Append the following code to the "Load Environment Variables" code in the `ai_vision.ipynb` notebook:
```python
VIDEO_INDEXER_SAMPLEPATH = os.getenv("VIDEO_INDEXER_SAMPLEPATH")
```

Re-execute "Load Environment Variables" code and restart the kernel.

#### Add Demonstration Code

Click "+ Markdown" and paste the following annotation into the resulting cell:
```markdown
## Video Indexer  
```

-------------------------

Click "+ Code" and paste the following Python into the resulting cell:
```python
import os
from dotenv import load_dotenv
import requests
import json
import time

load_dotenv(".env")

VIDEO_INDEXER_TOKEN = os.getenv("VIDEO_INDEXER_TOKEN")
VIDEO_INDEXER_SAMPLEPATH = os.getenv("VIDEO_INDEXER_SAMPLEPATH")
VIDEO_INDEXER_LOCATION = os.getenv("VIDEO_INDEXER_LOCATION")
VIDEO_INDEXER_ACCOUNT_ID = os.getenv("VIDEO_INDEXER_ACCOUNT_ID")

video_name = os.path.splitext(os.path.basename(VIDEO_INDEXER_SAMPLEPATH))[0]

list_url = f"https://api.videoindexer.ai/{VIDEO_INDEXER_LOCATION}/Accounts/{VIDEO_INDEXER_ACCOUNT_ID}/Videos?accessToken={VIDEO_INDEXER_TOKEN}"
list_response = requests.get(list_url)
list_response.raise_for_status()
videos_list = list_response.json()

for video in videos_list.get("videos", []):
    if video.get("name") == video_name:
        vid_id = video.get("id")
        delete_url = f"https://api.videoindexer.ai/{VIDEO_INDEXER_LOCATION}/Accounts/{VIDEO_INDEXER_ACCOUNT_ID}/Videos/{vid_id}?accessToken={VIDEO_INDEXER_TOKEN}"
        delete_response = requests.delete(delete_url)
        if delete_response.status_code == 204:
            print(f"Deleted video with id {vid_id}")
        else:
            print(f"Failed to delete video with id {vid_id}: {delete_response.text}")

time.sleep(10)

upload_url = f"https://api.videoindexer.ai/{VIDEO_INDEXER_LOCATION}/Accounts/{VIDEO_INDEXER_ACCOUNT_ID}/Videos?accessToken={VIDEO_INDEXER_TOKEN}&name={video_name}&privacy=Private"
with open(VIDEO_INDEXER_SAMPLEPATH, "rb") as video_file:
    video_data = video_file.read()
files = {'file': (os.path.basename(VIDEO_INDEXER_SAMPLEPATH), video_data, 'video/mp4')}

upload_response = requests.post(upload_url, files=files)
upload_response.raise_for_status()
print(json.dumps(upload_response.json(), indent=2))
```

> Note: If you are using the same video file, you may need to change the name in the URL to avoid a "duplicate" error

##### Expected Result

```json
{
  "accountId": "d66d7d20-aaf9-46c5-9d98-a63819219511",
  "id": "srcpxbz9z8",
  "partition": null,
  "externalId": null,
  "metadata": null,
  "name": "Space-247365~orig",
  "description": null,
  "created": "2025-04-03T19:36:34.8566667+00:00",
  "lastModified": "2025-04-03T19:36:34.8566667+00:00",
  "lastIndexed": "2025-04-03T19:36:34.8566667+00:00",
  "privacyMode": "Private",
  "userName": "Rich Chapler",
  "isOwned": true,
  "isBase": true,
  "hasSourceVideoFile": true,
  "state": "Uploaded",
  "moderationState": "OK",
  "reviewState": "None",
  "isSearchable": true,
  "processingProgress": "1%",
  "durationInSeconds": 0,
  "thumbnailVideoId": "srcpxbz9z8",
  "thumbnailId": "00000000-0000-0000-0000-000000000000",
  "searchMatches": [],
  "indexingPreset": "Default",
  "streamingPreset": "Default",
  "sourceLanguage": "en-US",
  "sourceLanguages": [
    "en-US"
  ],
  "personModelId": "00000000-0000-0000-0000-000000000000"
}
```
