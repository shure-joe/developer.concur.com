
## Image v1

<div id="attn-depr">
<p><b>This API has been deprecated.</b></p>

<p>Partners and customers using a deprecated API should contact SAP Concur and discuss moving to the latest versions.</p>
</div>

Learn more in the [API Lifecycle & Deprecation Policy.](/tools-support/deprecation-policy.html)


* [Post an image](#post-image)
* [Get an image URL](#get-image-url)

The SAP Concur Imaging Web service allows clients to manage the receipt images attached to expense reports and the images attached to invoices. Clients can retrieve existing images by reportID, image ID, or invoiceID, and upload new images to a user, expense entry, report, or invoice.

The Imaging web service supports the following image formats:

* `application/pdf` which has not been encrypted or password protected.
* `image/jpg`
* `image/jpeg`
* `image/png`

The maximum file size allowed is 10 MB.

#### Scope Usage

Name|Description|Endpoint
---|---|---
`IMAGE`|Add or Retrieve Report and Line Item Images.|GET, POST

#### Product Restrictions

SAP Concur products are highly configurable, and not all clients will have access to all features.

Partner developers must determine which configurations are required for their solution prior to the review process.

Existing clients can work with SAP Concur Advantage Technical Services to create custom applications that work with their configuration.

#### <a name="post-image"></a>Post an image

### Request

#### URI

#### Templates

```shell
https://www.concursolutions.com/api/image/v1.0/receipt
https://www.concursolutions.com/api/image/v1.0/expenseentry/{entryId}
https://www.concursolutions.com/api/image/v1.0/invoice/{requestId}
https://www.concursolutions.com/api/image/v1.0/report/{reportId}
```

#### Parameters

> **Required**: One of these parameters is always required.

Name|Type|Format|Description
---|---|---|---
`receipt`|`string`|-|Uploads a receipt image and associates it with the OAuth consumer. The user can view the image in the receipt management section of SAP Concur.
`entryReceiptID/{entryReceiptId}`|`string`|-|Obtains a short-lived URL (15 minute ttl) that can be rendered to obtain the receipt image. 
`expenseentry/{entryID}`|`string`|-|Uploads a receipt image and associates it with the expense entry that matches the supplied entry ID. Once an image is attached to the entry, you cannot append additional images.
`invoice/{requestID}`|`string`|-|Uploads an invoice image and associates it with the invoice that matches the supplied request ID. Each invoice is uniquely identified by the request ID value. Once an image is attached to the invoice, you cannot append additional images. The Concur Invoice product is required to use this endpoint. Currently you must use the Invoice user interface to get the Request ID value.
`report/{reportID}`|`string`|-|Uploads a receipt image and associates it with the report that matches the supplied report ID. If a report image already exists for the report, the new image will be appended to the existing image.

#### Headers

* [RFC 7235 Authorization](https://tools.ietf.org/html/rfc7235#section-4.2) (Required) - Authorization header with OAuth token for valid SAP Concur user. The SAP Concur user that authenticates during the OAuth process for this request must either be the user associated with this image, or have one of the following user roles in SAP Concur: Company Administrator or Web Services Administrator for Professional, or Can Administer for Standard. These administrative roles allow the user to manage data for the entire company.
* [RFC 7231 Content-Type](https://tools.ietf.org/html/rfc7231#section-3.1.1.5)
  * `application/pdf`, `image/jpg`, `image/jpeg`, `image/png`
* [RFC 7230 Content-Length](https://tools.ietf.org/html/rfc7230#section-3.3.2)

#### Payload

A byte array containing the image data.

### Response

#### Status Codes

* [201 Created](https://tools.ietf.org/html/rfc7231#section-6.3.2)

#### Headers

* [RFC 7231 Content-Type](https://tools.ietf.org/html/rfc7231#section-3.1.1.5): `application/xml`

#### Payload

* [Image](#schema-image)

### Example

#### Request

> This example contains a truncated (see the ellipsis) 64-bit encoded string representation of the image for illustration purposes only.
> This example demonstrates one of the endpoints available.

```shell
POST https://www.concursolutions.com/api/image/v1.0/receipt
Authorization: OAuth {token}
Content-Length: 65536
Content-Type: image/jpeg
```

```text
/9j/4AAQSkZJRgABAQEAyADIAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0a
HBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/2wBDAQkJCQwLDBgNDRgyIRwhMjIyMjIy
MjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjL/wAARCAkiBqQDASIA
AhEBAxEB/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQA
AAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3
ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWm
p6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/8QAHwEA
AwEBAQEBAQEBAQAAAAAAAAECAwQFBgcICQoL/8QAtREAAgECBAQDBAcFBAQAAQJ3AAECAxEE ...
```

#### Response

```shell
HTTP/1.1 201 Created
Content-Type: application/xml
```

```xml
<Image xmlns="http://www.concursolutions.com/api/image/2011/02" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <Id>aBcDeMwbl34xnwdkUw5ZjDsggDl2$pyoy31$pnGaHAywmPrpbAmE</Id>
  <Url />
</Image>
```

#### <a name="get-image-url"></a>Get an image URL

Retrieves a URL to the following images:

* Receipt
* Expense entry
* Invoice
* Expense Report (PDF)
  * If receipts have been uploaded to specific expense entries, they will all be bundled into one PDF at the report level.

The URL is valid for 30 minutes after the request.

### Request

#### URI

#### Template

```shell
https://www.concursolutions.com/api/image/v1.0/receipt/{imageId}
https://www.concursolutions.com/api/image/v1.0/expenseentry/{entryId}
https://www.concursolutions.com/api/image/v1.0/invoice/{requestId}
https://www.concursolutions.com/api/image/v1.0/report/{imageId}
```

#### Parameters

Name|Type|Format|Description
---|---|---|---
`receipt/{imageId}`|`string`|-|The {keyword}/{identifier} for the desired receipt.
`expenseentry/{entryID}`|`string`|-|The {keyword}/{identifier} for the desired expense entry. The entryId is returned in the `RpeKey` within `ExpenseEntry` portion of the response from the [Expense Report Full Details v1.1](/api-reference/expense/expense-report/v1dot1.report-full-details.html) API.
`invoice/{requestID}`|`string`|-|The {keyword}/{identifier} for the desired payment request. The Concur Invoice product is required to use this endpoint. Currently you must use the Invoice user interface to get the Request ID value.
`report/{imageId}`|`string`|-|The {keyword}/{identifier} for the desired report.

#### Headers

* [RFC 7235 Authorization](https://tools.ietf.org/html/rfc7235#section-4.2) containing an OAuth token for a valid SAP Concur user. The OAuth consumer must be the owner of the report to which the image is attached.
* [RFC 7231 Accept](https://tools.ietf.org/html/rfc7231#section-5.3.2): `application/xml`

#### Payload

None

### Response

#### Status Codes

* [200 OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)

#### Headers

* [RFC 7231 Content-Type](https://tools.ietf.org/html/rfc7231#section-3.1.1.5): `application/xml`

#### Payload

* [Image](#schema-image)

### Example

#### Request

> This example demonstrates one of the endpoints available.

```shell
GET /api/image/v1.0/expenseentry/A2C40CEE415B43B2A0BE
Authorization: OAuth {token}
Accept: application/xml
```

#### Response

```shell
200 OK
Content-Type: application/xml
```

```xml
<Image xmlns="http://www.concursolutions.com/api/image/2011/02" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <Id>A2C40CEE415B43B2A0BE</Id>
  <Url>https://api.example.com/getImage?cid=able999999&amp;val=F9B35244G86</Url>
</Image>
```

#### <a name="schema"></a>Schema

#### <a name="schema-image"></a>Image

The Image schema provides metadata about an image within the system.

Name|Type|Format|Description
---|---|---|---
`Id`|`string`|-|The unique identifier of the image.
`Url`|`string`|-|The URL for the image. Note that special characters will be XML-encoded. You will need to unencode any special characters before using the link. This element is empty when uploading images.

#### C# Code Example

The following example in C# illustrates how to read the source image file as an array of bytes and pass that data into the request.

```csharp
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
request.Method = "POST";
request.ContentType = "application/pdf";
request.Headers.Add("Authorization", signature);

using (FileStream fs = new FileStream(pathToImageFile, FileMode.Open, FileAccess.Read))
{
	using (BinaryReader br = new BinaryReader(fs))
	{
		byte[] image = br.ReadBytes((int)fs.Length);
		request.ContentLength = image.Length;

		Stream ds = request.GetRequestStream();
		ds.Write(image, 0, image.Length);
		ds.Close();
	}
}

HttpWebResponse webresponse = request.GetResponse();
```
