# LASTBOUNCE API

## Quick Start

Our API has been designed for simple integration with your software. The API allows for both verification of individual emails and via the upload of a batch job, containing multiple records.

To integrate with our API, you will require an API key tied to your account. This serves as a method of authenticating your requests to our API, and managing your usage via the notion of account credits. An API key is therefore tied to usage; for each email that is verified, a credit is deducted from your account. If the credit balance is insufficient, the API call will fail.

### Verify a Single Email

To verify, refer to the sample curl request below:

```
curl \
--location \
--request POST \
'https://api.lastbounce.com/api/singleEmail/validate?email=virgilio.so@demandscience.com' \
--header 'x-api-key: DdTHUVFAI4Q6u810nlKhfIhHHo8qmoYqJD4Ns7jJ'

```

Response

```
{
    "email": "virgilio.so@demandscience.com",
    "status": true,
    "generalResult": "VALID",
    "specificResult": "OK",
    "domain": "demandscience.com",
    "localPart": "virgilio.so"
}
```

In the JSON response above, if the `status` is `true`, this indicates the email is deliverable, otherwise `false` means the email address does not exist. Along with `status` we include a field`generalResult` which includes a brief text description of the result. The `specificResult` identifies a more specific reason for the result. See [here] (## General and Specific Response Codes) for a list of result codes.

For additional features you may check the API documentation [here] (### Realtime Single Email Validation).

### Verify via Batch Upload

#### Send a CSV Batch File

A CSV file may include an optional header row. One column header must contain the word `Email` - we extract the email address for verification from this column. For a sample CSV file, you may check [here](## Sample CSV File).


```
curl \
--location \
--request POST \
'https://api.lastbounce.com/api/batch/validation' \
--header 'x-api-key: DdTHUVFAI4Q6u810nlKhfIhHHo8qmoYqJD4Ns7jJ' \
--form 'file=@"/file/path/email_list.csv"'
```

After submitting the CSV file, the batch verification process will start. The JSON response will contain a `batchJobId` which can be used to check on the batch job status.

```
{
    "fileName": "email_list_with_header2.csv",
    "fileSize": 83,
    "fileContentType": "text/csv",
    "fileId": 425,
    "meta": {
        "batchJobId": "c4f3c554-76e2-4a0e-8331-5c7551a8a250",
        "emailCount": 3,
        "duplicateCount": 0,
        "spamtrapCount": 0,
        "disposableCount": 0,
        "roleAccountCount": 0,
        "invalidFormatCount": 0,
        "typoCount": 1
    }
}

```

#### Checking The Batch Job Status

Perodically you will need to check on the status of your batch job by polling for the result. A poll of 5 to 10 seconds is enough for a small file (<1000 records), polling less frequently for a larger file. 

```
curl \
--location \
--request POST \
'https://api.lastbounce.com/api/batch/c4f3c554-76e2-4a0e-8331-5c7551a8a250/status' \
--header 'x-api-key: DdTHUVFAI4Q6u810nlKhfIhHHo8qmoYqJD4Ns7jJ'
```

The JSON response of the status

```
{
    "jobId": "c4f3c554-76e2-4a0e-8331-5c7551a8a250",
    "countDetail": {
        "validCount": 2,
        "invalidCount": 0,
        "acceptAllCount": 0,
        "riskyCount": 0,
        "duplicateCount": 0,
        "unknownCount": 1
    },
    "totalEmail": 3,
    "totalProcessedEmail": 3,
    "status": "COMPLETED",
    "createDate": "2022-03-01T03:57:15.97998Z",
    "completeDate": "2022-03-01T03:57:27.724657Z",
    "processedTime": 11
    "processed": 100
}
```

The response field `status` reflects the current batch job status. If it's still processing, `IN_PROGRESS` will be returned. If it's `COMPLETED` then the results download file is ready to be retrieved.

#### Download Results

Download the results file by specifying the `jobId` in a single API call:

```
curl --location --request GET \
'https://api.lastbounce.com/api/file/download/c4f3c554-76e2-4a0e-8331-5c7551a8a250' \
--header 'x-api-key: DdTHUVFAI4Q6u810nlKhfIhHHo8qmoYqJD4Ns7jJ'
```

The data returned will be a CSV file containing the results.

```
Email,Validated Email,Result,Additional Status Info
virgilio.so@demandscience.com,,VALID,OK
virgilio_so_jr@yahoo.cmo,,UNKNOWN,B2C
virgilio.so@gmail.com,,VALID,OK
```

## API Documentation


### Realtime Single Email Validation

| PARAMETER | TYPE   | VALUE           |  DESCRIPTION                                              |
|-----------|--------|-----------------|-----------------------------------------------------------|
| x-api-key | Header | API key         | Required unless your domain has been whitelisted         |
| email     | Query  | Email           | Required. This is the email to be validated               |
| fixTypos  | Query  | true or false   | Optional. Default false. Set to true if you want to auto fix typo errors |
| timeout   | Query  | 5 to 30         | Optional. Default to 5 seconds. Force response after a specified timeout. API response will be forced to return whatever the current status |

**Sample Single Verification Request**

```
curl --location --request POST \
'https://api.lastbounce.com/api/singleEmail/validate?email=virgilio.so@demandscience.cm&fixTypos=false&timeout=30' \
--header 'x-api-key: DdTHUVFAI4Q6u810nlKhfIhHHo8qmoYqJD4Ns7jJ'
```

**Sample Single Verification Response**

```
{
    "email": "virgilio.so@demandscience.cm",
    "status": false,
    "generalResult": "INVALID",
    "specificResult": "DNS_FAILURE",
    "domain": "demandscience.cm",
    "localPart": "virgilio.so"
}
```

Changing the API request to allow typo corrections by setting`fixTypos` to `true` with a sample `email` of `virgilio.so@demandscience.cm` will change the response as shown below:

```
{
    "email": "virgilio.so@demandscience.cm",
    "status": true,
    "generalResult": "VALID",
    "specificResult": "OK",
    "fixedTypo": "virgilio.so@demandscience.com",
    "domain": "demandscience.com",
    "localPart": "virgilio.so"
}
```

**Single Verification Response Fields**

| FIELD          | DESCRIPTION                            |
|----------------|----------------------------------------|
| email          | Email that was validated               |
| status         | True if email is valid otherwise false |
| generalResult  | Cause of the error result              |
| specificResult | More specific case of the error        |
| domain         | Domain part of the email               |
| localPart      | Local part of the email                |
| fixedTypo      | This field will show when `fixTypos` is set to true and the email was corrected |

### CSV Batch File Validation

| PARAMETER | TYPE            | VALUE           |  DESCRIPTION                                                     |
|-----------|-----------------|-----------------|------------------------------------------------------------------|
| x-api-key | Header          | API key         | Required unless your domain has been white listed                |
| file      | Body form data  | CSV file        | Required. This is the CSV file containing emails to be validated |
| fixTypos  | Query           | true or false   | Optional. Default false. Set to true if you want to auto fix the provided email. This will fix typographical errors |


**Sample CSV Batch File Request**

```
curl --location --request POST \
'https://api.lastbounce.com/api/batch/validation' \
--header 'x-api-key: DdTHUVFAI4Q6u810nlKhfIhHHo8qmoYqJD4Ns7jJ' \
--form 'file=@"/file/path/email_list.csv"'
```

**Sample CSV Batch File Response**

```
{
    "fileName": "email_list.csv",
    "fileSize": 83,
    "fileContentType": "text/csv",
    "fileId": 425,
    "meta": {
        "batchJobId": "c4f3c554-76e2-4a0e-8331-5c7551a8a250",
        "emailCount": 3,
        "duplicateCount": 0,
        "spamtrapCount": 0,
        "disposableCount": 0,
        "roleAccountCount": 0,
        "invalidFormatCount": 0,
        "typoCount": 1
    }
}

```

**CSV Batch File Response Fields**

| FIELD           | DESCRIPTION                            |
|-----------------|----------------------------------------|
| fileName        | File name uploaded                     |
| fileSize        | File size in bytes                     |
| fileContentType | File content type                      |
| fileId          | Internal file identifier               |
| meta.batchJobId         | Batch job identifier                   |
| meta.emailCount         | Number of emails in the file           |
| meta.duplicateCount     | Number of duplicate emails found in the file |
| meta.spamtrapCount      | Number of spam trap email in the file            |
| meta.disposableCount    | Number of disposable emails in the file           |
| meta.roleAccountCount   | Number of role account emails in the file         |
| meta.invalidFormatCount | Number of invalid formatted emails in the file |
| meta.typoCount          | Zero if `fixTypos` is set to false else count of email corrected |

### Batch Status

| PARAMETER | TYPE            | VALUE           |  DESCRIPTION                                                     |
|-----------|-----------------|-----------------|------------------------------------------------------------------|
| x-api-key | Header          | API key         | Required unless your domain has been white listed                |
| {jobId}   | Path            | Job Id          | Required. This is the batch job identifier. This is provided when we upload the file |

**CSV Batch File Status Sample Request**

```
curl --location --request POST \
'https://api.lastbounce.com/api/batch/c4f3c554-76e2-4a0e-8331-5c7551a8a250/status' \
--header 'x-api-key: DdTHUVFAI4Q6u810nlKhfIhHHo8qmoYqJD4Ns7jJ'
```

**CSV Batch File Status Sample Response**

```
{
    "jobId": "c4f3c554-76e2-4a0e-8331-5c7551a8a250",
    "countDetail": {
        "validCount": 2,
        "invalidCount": 0,
        "acceptAllCount": 0,
        "riskyCount": 0,
        "duplicateCount": 0,
        "unknownCount": 1
    },
    "totalEmail": 3,
    "totalProcessedEmail": 3,
    "status": "COMPLETED",
    "createDate": "2022-03-01T03:57:15.97998Z",
    "completeDate": "2022-03-01T03:57:27.724657Z",
    "processedTime": 11
    "processed": 100
}

```

**CSV Batch File Status Response Fields**

| FIELD                      | DESCRIPTION                            |
|----------------------------|----------------------------------------|
| jobId                      | Job identifier                         |
| countDetail.validCount     | Count of valid emails                  |
| countDetail.invalidCount   | Count of invalid emails                |
| countDetail.acceptAllCount | Count of accept-all emails             |
| countDetail.riskyCount     | Count of risky emails                  |
| countDetail.duplicateCount | Count of duplicate emails              |
| countDetail.unknownCount   | Count of unknown emails                |
| totalEmail                 | Total count of emails                  |
| totalProcessedEmail        | Total count of processed emails        |
| status                     | Tells us what the current status is. If it's still processing it will be set to `IN_PROGRESS`. If it's `COMPLETED` then the the results file is ready to download |
| createDate                 | Date when file was uploaded. UTC 0        |
| completeDate               | Date when validation was completed. UTC 0 |
| processedTime              | Time it took to validate the batch in seconds |

### Batch File Download (CSV)

| PARAMETER | TYPE            | VALUE           |  DESCRIPTION                                                     |
|-----------|-----------------|-----------------|------------------------------------------------------------------|
| x-api-key | Header          | API key         | Required unless your domain has been white listed                |
| {jobId}   | Path            | Job Id          | Required. This is batch job identifier. This is provided when we upload the file |


**Batch File Download Sample Request**

```
curl --location --request GET \
'https://api.lastbounce.com/api/file/download/c4f3c554-76e2-4a0e-8331-5c7551a8a250' \
--header 'x-api-key: DdTHUVFAI4Q6u810nlKhfIhHHo8qmoYqJD4Ns7jJ'

```

**Batch File Download Sample Response**

```
Email,Validated Email,Result,Additional Status Info
virgilio.so@demandscience.com,,VALID,OK
virgilio_so_jr@yahoo.cmo,,UNKNOWN,B2C
virgilio.so@gmail.com,,VALID,OK

```

**Batch File Download Response Fields**

The response is an actual file which contains the original data and some new columns which represents the result details.

| COLUMN HEADER              | DESCRIPTION                            |
|----------------------------|----------------------------------------|
| Validated Email            | Blank if no typo corrections were done, otherwise a corrected email is shown here |
| Result                     | Result of the email  |
| Additional Status Info     | A more specific result code | 

See [here] (## General and Specific Response Codes) for a list of result codes.


### API Usage

| PARAMETER | TYPE            | VALUE           |  DESCRIPTION                                                     |
|-----------|-----------------|-----------------|------------------------------------------------------------------|
| x-api-key | Header          | API key         | Required unless your domain has been whitelisted                |
| startDate | Query           | Start date      | Required. Used as a range to query validation usages             |
| endDate   | Query           | End date        | Required. Used as a range to query validation usages             |

**API Usage Sample Request**

```
curl --location --request POST \
'https://api.lastbounce.com/api/usage?startDate=2018-01-01&endDate=2023-02-24' \
--header 'x-api-key: DdTHUVFAI4Q6u810nlKhfIhHHo8qmoYqJD4Ns7jJ'

```

**API Usage Sample Response**

```
{
    "usageTotal": 799,
    "validCount": 37,
    "invalidCount": 97,
    "acceptAllCount": 94,
    "unknownCount": 197,
    "disposableCount": 62,
    "roleAccountCount": 254,
    "duplicateCount": 11,
    "spamTrapCount": 82
}

```

**API Usage Response Fields**

| FIELD            | DESCRIPTION                            |
|------------------|----------------------------------------|
| usageTotal       | Total validations with results given the date range |
| validCount       | Count of valid results given the date range         |
| invalidCount     | Count of invalid results given the date range       |
| acceptAllCount   | Count of accept all results given the date range    |
| unknownCount     | Count of unknown results given the date range       |
| disposableCount  | Count of disposable results given the date range    |
| roleAccountCount | Count of role account results given the date range  |
| duplicateCount   | Count of duplicate results given the date range     |
| spamTrapCount    | Count of spam results given the date range          |


### General and Specific Response Codes

| General Result   | Description                                |
|------------------|--------------------------------------------|
| DUPLICATE        | Duplicate email detected |
| RISKY            | Email is risky |
| ACCEPT_ALL       | Email is accept-all |
| UNKNOWN          | Unable to determine result |
| INVALID          | Email is invalid |
| VALID            | Email is valid |


| Specific Result                 | Description                                |
|---------------------------------|--------------------------------------------|
| ANY\_EMAIL\_ACCEPTED | Email server is accepting any emails for the domain |
| B2C | Email address is B2C. Email address is from a "free" provider such as hotmail, yahoo, gmail |
| BAD | Email address is bad |
| COMMON\_TYPO | Email address contains a typo |
| DISPOSABLE | Email address is disposable. typically quick-turnaround addresses that are disposed of quickly |
| DNS\_FAILURE | Email domain DNS results in a failure |
| DOMAIN\_SUSPENDED | Email domain is suspended |
| DUPLICATE | Email address is a duplicate |
| EXCLUDE | Email address is excluded |
| GREYLISTING | Email address is greylisted |
| INVALID\_CONVERSATION | Target mail server returned an invalid conver
