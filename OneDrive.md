# OneDrive Graph API Samples#

Thses samples build on the access_token you received in the [Azure AD v2 Auth Flow](AzureADv2AuthFlow.md) sample. 

## Required Scopes for these Samples ##
These samples required the following permissions scopes

* https://graph.microsoft.com/Files.ReadWrite 
* https://graph.microsoft.com/User.Read

### List files in OneDrive ###

```
GET https://graph.microsoft.com/v1.0/me/drive/root/children
Authorization: bearer eyJ0eXAiOiJKV1Q...[redacted]
```

### Create OneDrive Folder ###

**Request**
```
POST https://graph.microsoft.com/v1.0/me/drive/root/children
Authorization: bearer eyJ0eXAiOiJKV1Q...[redacted]
Content-Type: application/json
Body:
```
```javascript
{
  "name": "test-folder",
  "folder": { }
}
```

**Response**

```javascript
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
```

### Get the created folder ###

** Request **

```
GET https://graph.microsoft.com/v1.0/me/drive/items/01B3CBOMV6Y2GOVW7725BZO354PWSELRRZ
Authorization: bearer eyJ0eXAiOiJKV1Q...[redacted]
```

** Response**

```javascript
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
```

### Upload txt file to created folder ###

**Request**

```
PUT https://graph.microsoft.com/v1.0/me/drive/items/01B3CBOMV6Y2GOVW7725BZO354PWSELRRZ/children/samplefile.txt/content
Authorization: bearer eyJ0eXAiOiJKV1Q...[redacted]
Content-Type: text/plain
Body:
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam hendrerit augue ut leo viverra, sed pellentesque neque maximus. In cursus quam nec metus pellentesque porttitor. Sed in ornare felis. Aenean vitae venenatis mi. Nunc ut scelerisque quam, vitae dignissim orci. Vivamus ut magna a leo egestas fermentum ac sed quam. Phasellus viverra mollis ligula, sit amet porta tellus vehicula et. Ut sit amet neque nisl. Aliquam erat volutpat. Quisque dignissim vulputate pulvinar.
```

**Response**

```javascript
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
```
