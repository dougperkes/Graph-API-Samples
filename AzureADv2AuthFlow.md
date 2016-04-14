## Step 1: Register App with Azure AD V2 ##

1. Navigate to https://apps.dev.microsoft.com and login
2. Click **Add an app**, give it a name. Click **Create application**
3. Copy the Application ID.
4. Click Generate New Password, copy the password
5. Click **Add Platform**, select **Web**. Enter a non-resolving URL for demo purposes only: http://localhost/nopage
6. Click **Save**

Application ID: 7992df25-0718-4861-8e4f-26146069f7e0
App Secret: YGBvC...[redacted]

## Step 2: Determine permission scopes ##

1. Navigate to http://graph.microsoft.io/en-us/docs/authorization/permission_scopes
2. Read the documentation to determine the permission scopes needed for your application.

For my purposes, I want User.Read and Files.ReadWrite so the scope string will be:
https://graph.microsoft.com/Files.ReadWrite https://graph.microsoft.com/User.Read
This needs to be URL encoded, so navigate to https://www.bing.com/search?q=url+encode and enter the scope string.

Encoded scope: https%3A%2F%2Fgraph.microsoft.com%2FFiles.ReadWrite+https%3A%2F%2Fgraph.microsoft.com%2FUser.Read

Encoded Redirect URL: 
http%3A%2F%2Flocalhost%2Fnopage


## Step 2: Get an authorization code ##

1. Build up an authorization URL using the following template

https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={ApplicationID}&scope={EncodedScope}&redirect_uri={RedirectURL}&response_type=code

For my sample, this will be:
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={7992df25-0718-4861-8e4f-26146069f7e0}&scope=https%3A%2F%2Fgraph.microsoft.com%2FFiles.ReadWrite+https%3A%2F%2Fgraph.microsoft.com%2FUser.Read&redirect_uri=http%3A%2F%2Flocalhost%2Fnopage&response_type=code

2. Open your web browser and start the developer tools (F12). Open the Network tab to record network activity. Alternatively you could use Fiddler to monitor network traffic.
3. Navigate to the authorization url, enter your credentials, and consent to the permissions by clicking the **Accept** button.
4. You will be redirected to http://localhost/nopage. This will give a 404 error, but that's OK. All we need is the **code** parameter that is supplied as part of the URL query string. Copy the URL of the page or use the Network tool to find the URL. 
5. Copy the code query string parameter. There will be a session state parameter at the end of the query string, so make sure you strip that off.

Authorization Code: 
OAAABAAAAiL...[redacted]

## Step 3: Get an auth token for the user ##

1. Open Postman or your favorite HTTP request creation tool and create a request like the following:

url: https://login.microsoftonline.com/common/oauth2/v2.0/token
method: POST
Content-Type: application/x-www-form-urlencoded
body: 
scope={Unencoded Scope}
&grant_type=authorization_code
&client_id={ApplicationID}
&client_secret={App Secret}
&redirect_uri={Unencoded RedirectURL}
&code={authCode}

**Full HTTP Request**

POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2FFiles.ReadWrite+https%3A%2F%2Fgraph.microsoft.com%2FUser.Read&grant_type=authorization_code&client_id=7992df25-0718-4861-8e4f-26146069f7e0&client_secret=YGBvC...[redacted]&redirect_uri=http%3A%2F%2Flocalhost%2Fnopage&code=OAAABAAAAiL...[redacted]
	
**Response**
	
The response will be JSON with similar to the following:
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/files.readwrite https://graph.microsoft.com/user.read",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...[redacted]"
}

The JSON response contains the access token we will use with all of our requests to the graph API. 

## Step 4: Call the graph API ##

To call the graph API we will use the access_token in the Authorization HTTP header. 
URL: https://graph.microsoft.com/v1.0/method
Authorization: bearer {access_token}

**Sample Call**

GET https://graph.microsoft.com/v1.0/me 
Authorization: bearer eyJ0eXAiOiJKV1Q...[redacted]

**Response**

{
  "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#users/$entity",
  "businessPhones": [
    "6232290897"
  ],
  "displayName": "Doug Perkes",
  "givenName": "Doug",
  "jobTitle": null,
  "mail": "doug@contoso.com",
  "mobilePhone": "+16232290897",
  "officeLocation": null,
  "preferredLanguage": "en-US",
  "surname": "Perkes",
  "userPrincipalName": "doug@contoso.com",
  "id": "a864094e-b1c1-47e1-b3cd-f2e111ac3c59"
}

## Additional Samples ##

### List files in OneDrive ###

GET https://graph.microsoft.com/v1.0/me/drive/root/children
Authorization: bearer eyJ0eXAiOiJKV1Q...[redacted]

### Create OneDrive Folder ###

**Request**
POST https://graph.microsoft.com/v1.0/me/drive/root/children
Authorization: bearer eyJ0eXAiOiJKV1Q...[redacted]
Content-Type: application/json
Body:
{
  "name": "test-folder",
  "folder": { }
}

**Response**

{
  "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#users('a864094e-b1c1-47e1-b3cd-f2e111ac3c59')/drive/root/children/$entity",
  "createdBy": {
    "application": {
      "id": "00000003-0000-0000-c000-000000000000",
      "displayName": "Microsoft.Azure.AgregatorService"
    },
    "user": {
      "id": "a864094e-b1c1-47e1-b3cd-f2e111ac3c59",
      "displayName": "Doug Perkes"
    }
  },
  "createdDateTime": "2016-04-14T16:04:26Z",
  "cTag": "\"c:{77B62C73-C66D-4E05-B53B-2C30F8F3F6DD},0\"",
  "eTag": "\"{77B62C73-C66D-4E05-B53B-2C30F8F3F6DD},1\"",
  "folder": {
    "childCount": 0
  },
  "id": "01B3CBOMTTFS3HO3OGAVHLKOZMGD4PH5W5",
  "lastModifiedBy": {
    "application": {
      "id": "00000003-0000-0000-c000-000000000000",
      "displayName": "Microsoft.Azure.AgregatorService"
    },
    "user": {
      "id": "a864094e-b1c1-47e1-b3cd-f2e111ac3c59",
      "displayName": "Doug Perkes"
    }
  },
  "lastModifiedDateTime": "2016-04-14T16:04:26Z",
  "name": "test-folder",
  "parentReference": {
    "driveId": "b!Qbh7KAnrPUGx80VUsEyU3_TshIEG2RRDqiyDEpfg04-ndAHeHrKxQo76R9ptvB4v",
    "id": "01B3CBOMV6Y2GOVW7725BZO354PWSELRRZ",
    "path": "/drive/root:"
  },
  "size": 0,
  "webUrl": "https://perkes-my.sharepoint.com/personal/doug_perkes_onmicrosoft_com/Documents/test-folder"
}

### Get the created folder ###

** Request **
GET https://graph.microsoft.com/v1.0/me/drive/items/01B3CBOMV6Y2GOVW7725BZO354PWSELRRZ
Authorization: bearer eyJ0eXAiOiJKV1Q...[redacted]

** Response**
{
  "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#users('a864094e-b1c1-47e1-b3cd-f2e111ac3c59')/drive/items/$entity",
  "createdDateTime": "2014-06-14T22:53:57Z",
  "folder": {
    "childCount": 15
  },
  "id": "01B3CBOMV6Y2GOVW7725BZO354PWSELRRZ",
  "lastModifiedDateTime": "2016-04-14T16:04:26Z",
  "name": "root",
  "size": 0,
  "specialFolder": {
    "name": "documents"
  },
  "webUrl": "https://perkes-my.sharepoint.com/personal/doug_perkes_onmicrosoft_com/Documents"
}

### Upload txt file to created folder ###

**Request**

PUT https://graph.microsoft.com/v1.0/me/drive/items/01B3CBOMV6Y2GOVW7725BZO354PWSELRRZ/children/samplefile.txt/content
Authorization: bearer eyJ0eXAiOiJKV1Q...[redacted]
Content-Type: text/plain
Body:
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam hendrerit augue ut leo viverra, sed pellentesque neque maximus. In cursus quam nec metus pellentesque porttitor. Sed in ornare felis. Aenean vitae venenatis mi. Nunc ut scelerisque quam, vitae dignissim orci. Vivamus ut magna a leo egestas fermentum ac sed quam. Phasellus viverra mollis ligula, sit amet porta tellus vehicula et. Ut sit amet neque nisl. Aliquam erat volutpat. Quisque dignissim vulputate pulvinar.

**Response**

{
  "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#users('a864094e-b1c1-47e1-b3cd-f2e111ac3c59')/drive/items('01B3CBOMV6Y2GOVW7725BZO354PWSELRRZ')/children/$entity",
  "@microsoft.graph.downloadUrl": "https://perkes-my.sharepoint.com/personal/doug_perkes_onmicrosoft_com/_layouts/15/download.aspx?guestaccesstoken=ZxiS%2bJcr9dz8N3dpGGg00W8n%2fvYNtZoJOe4JmqMP7Pk%3d&docid=0ea029cc0e685466fa555f52ccffd4d5b&expiration=2016-04-14T17%3a10%3a58.000Z&userid=3&authurl=True&NeverAuth=True",
  "createdBy": {
    "application": {
      "id": "00000003-0000-0000-c000-000000000000",
      "displayName": "Microsoft.Azure.AgregatorService"
    },
    "user": {
      "id": "a864094e-b1c1-47e1-b3cd-f2e111ac3c59",
      "displayName": "Doug Perkes"
    }
  },
  "createdDateTime": "2016-04-14T16:10:58Z",
  "cTag": "\"c:{EA029CC0-E685-466F-A555-F52CCFFD4D5B},1\"",
  "eTag": "\"{EA029CC0-E685-466F-A555-F52CCFFD4D5B},1\"",
  "file": {
    "hashes": {}
  },
  "id": "01B3CBOMWATQBOVBPGN5DKKVPVFTH72TK3",
  "lastModifiedBy": {
    "application": {
      "id": "00000003-0000-0000-c000-000000000000",
      "displayName": "Microsoft.Azure.AgregatorService"
    },
    "user": {
      "id": "a864094e-b1c1-47e1-b3cd-f2e111ac3c59",
      "displayName": "Doug Perkes"
    }
  },
  "lastModifiedDateTime": "2016-04-14T16:10:58Z",
  "name": "samplefile.txt",
  "parentReference": {
    "driveId": "b!Qbh7KAnrPUGx80VUsEyU3_TshIEG2RRDqiyDEpfg04-ndAHeHrKxQo76R9ptvB4v",
    "id": "01B3CBOMV6Y2GOVW7725BZO354PWSELRRZ",
    "path": "/drive/root:"
  },
  "size": 476,
  "webUrl": "https://perkes-my.sharepoint.com/personal/doug_perkes_onmicrosoft_com/Documents/samplefile.txt"
}