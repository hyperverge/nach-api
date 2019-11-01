
## HyperVerge NACH API Documentation

The NACH API contains three endpoints:

1. NACH Generation
2. NACH Extraction
3. NACH Alignment

## NACH Generation 

### Overview
The NACH generation endpoint generates pre-filled NACH form from the fields passed as input.  This NACH form is returned as a base64 encoded PDF.

### Schema
We recommend using HTTPS for all API access. All data is received as JSON.

### Root Endpoint
A `GET` request can be issued to the root endpoint to check for successful connection: 

	 curl https://ind-docs-2.hyperverge.co/v2.0 

The `plain/text` reponse of `"AoK!"` should be received.

### Authentication

Currently, a simple appId, appKey combination is passed in the request header. The appId and appKey are provided on request by the HyperVerge team. If you would like to try the API, please reach out to contact@hyperverge.co

	curl -X POST https://ind-docs-2.hyperverge.co/v2.0/populateNACH \
	  -H 'appid: xxx' \
	  -H 'appkey: yyy' \
	  -H 'transactionid: zzz' \
	  -H 'content-type: application/json'

On failed attempt with invalid credentials or unauthorized access the following error message will be received :

	{
	  "status": "failure",
	  "statusCode": "401",
	  "error" : {
        "message" : "Missing/Invalid credentials",	
		  }	
	}

Please do not expose the appId and appKey on browser applications. In case of a browser application, set up the API calls from the server side.


### API Call Structure

*  **URL**
	* /populateNACH

*  **Method:**  `POST`

*  **Header**
	- content-type : 'application/json'
	- appid
	- appkey
	- transactionid

*  **Request Body**

	List of fields to passed in request body json

	
	| Field |  Type| Maximum length | Compulsory|
	|---|---|--|--|
	| UMRN|String| 20| yes|
	| nachDate|"MM/DD/YYYY"||yes|
	| sponsorCode|String|11| yes|
	| utilityCode|String|18| yes|
	| bankName|String|30| yes|
	| accountType|"SB"/"CA"/"CC"/"SB-NRO"/"SB-NRE"/"Others"||yes|
	| IFSCCode|String|11|yes|
	| MICR|String|9||
	| companyName|String|30|yes|
	| frequency|"monthly"/"quarterly"/"half-yearly"/"yearly"/"As and when present"||yes|	
	| amountInNumber|String|10|yes|
	| amountInWords|String|30|yes|
	| debitType|"fixedAmount"/"maximumAmount"||yes|
	| startDate|"MM/DD/YYYY"||yes|
	| endDate|"MM/DD/YYYY"|
	| untilCanceled|"yes"/"no"|
	| NACHType|"create" / "modify" / "cancel"||yes|
	| phoneNumber|String|10|
	| emailId|String|30|
	| reference1|String|25|
	| reference2|String|25|
	| primaryAccountHolder|String|22|yes|
	| secondaryAccountHolder|String|22
	| tertiaryAccountHolder|String|22
	| logo|String(will be shared by Hyperverge for different clients)|5 |yes|
	
	
### Response Structure

*  **Success Response:**

	*  **Code:** 200

		* Incase of a properly made request, the response would follow schema.
		```
		{
			"status" : "success",
			"statusCode" : "200",
			"result" : <resultObject>
		}
		```
		The `resultObject` has the following 		Schema :
		```
		{
			"outputImage" : "Base 64 of the generated image with the fields filled"
		}
		```
		
*  **Error Response:**
	* Request error
		```
		{
			"status" : "failure",
			"statusCode" : "xxx",
			"error" : {
				"message" : 'Message'	
          }	
		}
		```
	
	| Case| Status Code | Message|
	|---|---|---|
	|Input error - missing field|400|"\<Field> is required"
	| Invalid or Missing appId and appKey | 401 | "Invalid or missing credentials"|
	| Internal server errors | 500 | "Internal server error" |


## NACH Extraction

### Overview
The NACH extraction endpoint takes a NACH form as input and extracts various fields from the form along with  cropping and aligning of the form.

### Schema
We recommend using HTTPS for all API access. All data is received as JSON, and all image uploads are to be performed as form-data (POST request). Incase of a pdf file input, the key name has to be `pdf` and in all other cases, the key name for the image could be anything apart from `pdf`.

### Root Endpoint
A `GET` request can be issued to the root endpoint to check for successful connection: 

	 curl https://ind-docs-2.hyperverge.co/v2.0 

The `plain/text` reponse of `"AoK!"` should be received.

### Authentication

Currently, a simple appId, appKey combination is passed in the request header. The appId and appKey are provided on request by the HyperVerge team. If you would like to try the API, please reach out to contact@hyperverge.co

	curl -X POST https://ind-docs-2.hyperverge.co/v2.0/populateNACH \
	  -H 'appid: xxx' \
	  -H 'appkey: yyy' \
	  -H 'transactionid: zzz' \
	  -H 'content-type: application/json'

On failed attempt with invalid credentials or unauthorized access the following error message will be received :

	{
	  "status": "failure",
	  "statusCode": "401",
	  "error" : {
		  "message" : "Invalid or missing credentials",
		  "code" : 1
		}
	}

Please do not expose the appId and appKey on browser applications. In case of a browser application, set up the API calls from the server side.


### API Call Structure

*  **URL**

	* /readNACH

*  **Method:**  `POST`

*  **Header**

	- content-type : 'multipart/formdata'
	- appid
	- appkey
	- transactionid

*  **Request Body**
	* Compulsory field
		- image : image file (jpg/tiff/png)
	* Optional fields - if these flags are present, then the corresponding base64 encoded file will be sent in the response
		- enableOutputJPEG : "yes"/"no"
		- enableOutputTIF: "yes"/"no"
		- enableOutputPDF: "yes"/"no"

	
### Response Structure

*  **Success Response:**

	*  **Code:** 200 <br  />

		* Incase of a properly made request, the response would follow schema.

		```
		{
			"status" : "success",
			"statusCode" : "200",
			"result" : <resultObject>
		}
		```

		* The `resultObject` has the following Schema :

		```
		{
			"details" : {

				"field-1" : {
					"value" : "extracted-value-1",
					"conf" : <int-value>,
					"to-be-reviewed" : "yes/no"
				},

				"field-2" : {
					"value" : "extracted-value-2",
					"conf" : <int-value>,
					"to-be-reviewed" : "yes/no"
				},

				"field-3" : {
					"value" : "extracted-value-3",
					"conf" : <int-value>,
					"to-be-reviewed" : "yes/no"
				},

			..

			},

			"base64AlignedJPEG" : "Base 64 of the Aligned JPEG image in required format",
			"base64AlignedTIF" :" Base 64 of the Aligned TIF image in the required format"
			"base64AlignedPDF": "Base 64 of the Aligned PDF file in the required format"
		}
		```

*  **Error Response**

	*  Request error
		
			{
				"status" : "failure",
				"statusCode" : "xxx",
				"error" : {
					"message" : 'Message',
					"code" : int
				}
			}

	#### Error Codes

	| Case | Status Code | Message | Error Code |
	|---|---|---|---|
	| Invalid or Missing appId and appKey | 401 | 		"Invalid or missing credentials" | 1 |
	| Missing Image| 400 | "Image input is missing" | 2 |
	| Invalid Image format | 400 | "Image not one of the supported types (jpg/tiff/png)"| 3 |
	| Unable to detect markers | 422 | "Reclick image - Unable to detect the markers" | 4 |
	| Internal server errors | 5xx | "Internal server error" | 5 |

	
	#### Fields Extracted

	|Field Key | Value Type |
	|---|---|
	| UMRN|String|
	| nachDate|"MM/DD/YYYY"|
	| sponsorCode|String|
	| utilityCode|String|
	| bankName|String|
	| accountType|"SB"/"CA"/"CC"/"SB-NRO"/"SB-NRE"/"Others"|
	| IFSCCode|String|
	| MICR|String|
	| customerName|String|
	| frequency|"monthly"/"quarterly"/"half-yearly"/"yearly"/"As and when present"|
	| amountInNumber|String|
	| amountInWords|String|
	| debitType|String|
	| startDate|"MM/DD/YYYY"|
	| endDate|"MM/DD/YYYY"|
	| untilCanceled|"yes"/"no"|
	| NACHType|"create" / "modify" / "cancel"|
	| phoneNumber|String|
	| emailId|String|
	| reference1|String|
	| reference2|String|
	| signaturePresentPrimary|"yes","no"|
	| signaturePresentSecondary|"yes","no"|
	| signaturePresentTertiary|"yes","no"|
	| primaryAccountHolder|String|
	| secondaryAccountHolder|String|
	| tertiaryAccountHolder|String|
	
## NACH Alignment

### Overview
The NACH extraction endpoint takes a NACH form as input, checks for the presence of NACH form and aligns the NACH form to requirements of NPCI

### Schema
We recommend using HTTPS for all API access. All data is received as JSON, and all image uploads are to be performed as form-data (POST request). Incase of a pdf file input, the key name has to be `pdf` and in all other cases, the key name for the image could be anything apart from `pdf`.

### Root Endpoint
A `GET` request can be issued to the root endpoint to check for successful connection: 

	 curl https://ind-docs-2.hyperverge.co/v2.0 

The `plain/text` reponse of `"AoK!"` should be received.

### Authentication

Currently, a simple appId, appKey combination is passed in the request header. The appId and appKey are provided on request by the HyperVerge team. If you would like to try the API, please reach out to contact@hyperverge.co

	curl -X POST https://ind-docs-2.hyperverge.co/v2.0/alignNACH \
	  -H 'appid: xxx' \
	  -H 'appkey: yyy' \
	  -H 'transactionid: zzz' \
	  -H 'content-type: application/json'

On failed attempt with invalid credentials or unauthorized access the following error message will be received :

	{
	  "status": "failure",
	  "statusCode": "401",
	  "error" : {
		  "message" : "Invalid or missing credentials",
		  "code" : 1
		}
	}

Please do not expose the appId and appKey on browser applications. In case of a browser application, set up the API calls from the server side.


### API Call Structure

*  **URL**

	* /alignNACH

*  **Method:**  `POST`

*  **Header**

	- content-type : 'multipart/formdata'
	- appid
	- appkey
	- transactionid

*  **Request Body**
	* Compulsory field
		- image : image file (jpg/tiff/png)
	* Optional fields - if these flags are present, then the corresponding base64 encoded file will be sent in the response
		- enableOutputJPEG : "yes"/"no"
		- enableOutputTIF: "yes"/"no"
		- enableOutputPDF: "yes"/"no"
	* Optional field - if this flag is present, signature verification will be performed
		- detectSignature: "yes"


		 

	
### Response Structure

*  **Success Response:**

	*  **Code:** 200 <br  />

		* Incase of a properly made request, the response would follow schema.

		```
		{
			"status" : "success",
			"statusCode" : "200",
			"result" : <resultObject>
		}
		```

		* The `resultObject` has the following Schema :

		```
		{
			"details" : {

				"field-1" : {
					"value" : "extracted-value-1",
					"conf" : <int-value>,
					"to-be-reviewed" : "yes/no"
				},

				"field-2" : {
					"value" : "extracted-value-2",
					"conf" : <int-value>,
					"to-be-reviewed" : "yes/no"
				},

				"field-3" : {
					"value" : "extracted-value-3",
					"conf" : <int-value>,
					"to-be-reviewed" : "yes/no"
				},

			..

			},

			"base64AlignedJPEG" : "Base 64 of the Aligned JPEG image in required format",
			"base64AlignedTIF" :" Base 64 of the Aligned TIF image in the required format"
			"base64AlignedPDF": "Base 64 of the Aligned PDF file in the required format"
		}
		```

*  **Error Response**

	*  Request error
		
			{
				"status" : "failure",
				"statusCode" : "xxx",
				"error" : {
					"message" : 'Message',
					"code" : int
				}
			}

	#### Error Codes

	| Case | Status Code | Message | Error Code |
	|---|---|---|---|
	| Invalid or Missing appId and appKey | 401 | 		"Invalid or missing credentials" | 1 |
	| Missing Image| 400 | "Image input is missing" | 2 |
	| Invalid Image format | 400 | "Image not one of the supported types (jpg/tiff/png)"| 3 |
	| Unable to detect markers | 422 | "Reclick image - Unable to detect the markers" | 4 |
	| Internal server errors | 5xx | "Internal server error" | 5 |

	
	#### Fields Extracted

	|Field Key | Value Type |
	|---|---|
	| signaturePresentPrimary|"yes","no"|
	| signaturePresentSecondary|"yes","no"|
	| signaturePresentTertiary|"yes","no"|
