# Tài liệu tích hợp cho JBF

## Tài liệu tích hợp cho JETPAY BankHub

## 1. Giới thiệu

Tài liệu tích hợp JETPAY BankHub đặc tả kết nối kỹ thuật tích hợp giữa đơn vị (cá nhân, tổ chức) với JETPAY BankHub

Tài liệu được sử dụng cho cán bộ nhân viên kỹ thuật của JETPAY và cán bộ nhân viên kỹ thuật của đơn vị tích hợp

## 2. Thông tin tích hợp

**Thông tin domain**

| STT | Tham số      | Giá trị môi trường TEST                    | Giá trị môi trường Product |
| --- | ------------ | ------------------------------------------ | -------------------------- |
| 1   | auth\_domain | https://testdcauthapi.jetpay.vn            |                            |
| 2   | jbf\_domain  | https://testdcbankhubbusinessapi.jetpay.vn |                            |

**Danh sách mã ngân hàng hỗ trợ**

| Mã ngân hàng | Tên ngân hàng                       |
| ------------ | ----------------------------------- |
| VTB          | Ngân hàng TMCP Công Thương Việt Nam |

## 3. Đặc tả chi tiết

### 3.1 Luồng kết nối kỹ thuật

### 3.2 Đặc tả chi tiết các API

#### 3.2.1 API lấy token xác thực hệ thống

API thực hiện tạo một token xác thực đơn vị tích hợp JETPAY BankHub. Token được sử dụng cho các yêu cầu từ phía website của đơn vị tới hệ thống JETPAY BankHub.

**URL**

`POST {{auth_domain}}/connect/token`

**Content-Type:**

`application/xxx-form-urlencoded`

**Parameters**

* `client_id` _**string**_: Id của client cần lấy token
* `client_secret` _**string**_: Key bí mật của client cần lấy token
* `grant_type` _**string**_: client\_credentials

**Response**

Kết quả trả về dạng JSON object:

* `access_token` _**string**_: Access token xác thực hệ thống
* `expires_in` _**int**_: Thời gian sống của token
* `token_type` _**string**_: Loại token, VD: Bearer
* `scope` _**string**_: Scope của token

**Ví dụ**

Request

```json
{
	"client_id": "jbf-123456-client",
	"client_secret": "f9e8f32e-b3f4-42f7-bb8d-c561b97a3db9",
	"grant_type": "client_credentials"
}
```

Response

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6ImE5NTkzYzZmZGMzYjQwMTJiYjBhNzMxMWU1NzM5NzQwIiwidHlwIjoiYXQrand0In0.eyJuYmYiOjE2ODg3MTIyNjgsImV4cCI6MTY4ODc5ODY2OCwiaXNzIjoiaHR0cDovL2pldHBheS1hdXRoc2VydmljZSIsImF1ZCI6IkF1dGhTZXJ2aWNlIiwiY2xpZW50X2lkIjoianB0ZXN0LW5hcGFzLWNsaWVudCIsImlhdCI6MTY4ODcxMjI2OCwic2NvcGUiOlsiQXV0aFNlcnZpY2UiXX0.cd2JxLvscwPAaEQCzxVkVuKtB5-7x27ukWHLibjkS3buSGCYKVUHF5ueiqeoq_k4wo36l1CbjsHdvtBDGBnBjeEQRBzr8EkhRg0-AfuKn6Gyn43LbYGdFGKXMiv17bkmwl_hSPG173lEipXQfCtxdoIqb51tgxbXQNq4ZrwBTvlg2aWmB9M5TWvCBdJ0fNf65Lc5bq3JfviLoJJo9khS9lZa90zfQ6_XY_VKFkoLiSt-WxPYbVm9L_MUJ_ahrlQOyh3gW_1pzBMOvPB_RZSHbEm-Pl_pxxU_xAsnIAo6wzuopNJ5OXiOaJfj_DOAbQ2cOKBm5t9uwjKhwyreIhO3zw",
  "expires_in": 86400,
  "token_type": "Bearer",
  "scope": "AuthService"
}
```

#### 3.2.2 API khởi tạo giao dịch cho đơn hàng chuyển khoản

API được gọi từ website của đơn vị sang JETPAY để khởi tạo giao dịch chuyển khoản theo cú pháp cho đơn hàng.

**URL**

`POST {{jbf_domain}}/orgs/transactions`

**Content-Type**

`application/json`

**Headers**

| Tên header    | Giá trị        | Ý nghĩa                                                      |
| ------------- | -------------- | ------------------------------------------------------------ |
| Authorization | Bearer {token} | Header chứa token xác thực ứng dụng tích hợp JETPAY BankHub. |

**Request Body**

* `org`: thông tin đơn vị, tổ chức hoặc doanh nghiệp
  * `taxNo` _**string**_: mã số thuế của đơn vị, tổ chức
* `bank`: thông tin ngân hàng được kết nối trên JETPAY BankHub
  * `bankCode` _**string**_: mã ngân hàng được kết nối trên JETPAY BankHub
  * `bankAccountNumber` _**string**_: Số tài khoản ngân hàng được kết nối trên JETPAY BankHub
* `order`: thông tin đơn hàng
  * `orderId` _**string**_: mã đơn hàng
  * `amount` _**long**_: số tiền đơn hàng
  * `domainWebsite` _**string**_ (_**optional**_): domain website tạo đơn hàng, phục vụ khởi tạo đúng cú pháp giao dịch theo quy tắc đã thiết lập trên JETPAY BankHub. Nếu trống thì sẽ lấy theo quy tắc website đầu tiên trong danh sách thiết lập
  * `exportInvoice` _**bool:**_ Cho biết đơn hàng có xuất hóa đơn MeInvoice không? (Mặc định là **false**)
  * `additionalData` _**object**_: Thông tin bổ sung cho đơn hàng
* `customer` (_**optional**_): Thông tin khách hàng thực hiện thanh toán
  * `code` **string (**_**optional**_**)**: Mã khách hàng, Nếu không truyền thì sẽ sinh mới khi lập đề nghị sinh chứng từ
  * `name` _**string**_: Tên khách hàng
  * `taxNo` **string (**_**optional**_**):** Mã số thuế (nếu người mua hàng là doanh nghiệp, tổ chức)
  * `email` _**string**_: Email của khách hàng
  * `phoneNumber` _**string**_: số điện thoại
  * `address` **string (**_**optional**_**)**: Địa chỉ người mua hàng

**Các trường thông tin bổ sung trong additionalData (**_**tùy chọn**_**)**

* `products` _**List**_: Danh sách hàng hóa

**Thông tin hàng hóa** **(Bổ sung cho Vbee)**

* `productCode` _**string**_: Mã hàng
* `quantity` _**int**_: Số lượng
* `unit` _**string**_: Đơn vị tính
* `buildingCode` _**string (optional)**_: Mã công trình
* `buildingName` **string (**_**optional**_**):** Tên công trình
* `saleContract` _**string (optional)**_: Hợp đồng bán
* `saleContractName` **string (**_**optional**_**)**: Tên hợp đồng bán
* `extendedField` _**string (optional)**_: Trường mở rộng, mặc định “bank”

**Response Body**

Kết quả **thành công** `HttpStatusCode = 200` là một JSON Object với định dạng:

* `bank`: thông tin ngân hàng được kết nối trên JETPAY BankHub
  * `bankCode` _**string**_: mã ngân hàng được kết nối trên JETPAY BankHub
  * `bankName` _**string**_: tên ngân hàng được kết nối trên JETPAY BankHub
  * `bankAccountNumber` _**string**_: số tài khoản của ngân hàng được kết nối trên JETPAY BankHub
  * `bankAccountName` _**string**_: tên chủ tài khoản của ngân hàng được kết nối trên JETPAY BankHub
  * `branchName` _**string**_ (_**optional**_): tên chi nhánh tài khoản ngân hàng
  * `bankLogoUrl` _**string**_ (_**optional**_): url logo ngân hàng
* `order`: Thông tin đơn hàng
  * `orderId` _**string**_: mã đơn hàng
  * `amount` _**long**_: số tiền đơn hàng
  * `description` _**string**_: nội dung thanh toán chuyển khoản cho đơn hàng
  * `vietQrUrl` _**string**_: Url QR chuyển khoản

Kết quả khởi tạo **thất bại** `HttpStatusCode # 200`

Là object có định dạng:

* `error`
  * `code`: Mã lỗi
  * `message`: Mô tả chi tiết cho mã lỗi

| HttpStatusCode | errorCode | Mô tả chi tiết                                                          |
| -------------- | --------- | ----------------------------------------------------------------------- |
| 400            | E1000     | Mã ngân hàng không hợp lệ                                               |
| 400            | E1001     | Số tài khoản ngân hàng của đơn vị không hợp lệ                          |
| 400            | E1002     | Số tài khoản của đơn vị chưa kết nối ngân hàng                          |
| 400            | E1003     | Số tài khoản ngân hàng của đơn vị đang ngưng liên kết (ngừng hoạt động) |
| 400            | E1004     | Mã số thuế không hợp lệ                                                 |
| 400            | E1005     | Đơn vị đang ngưng hoạt động                                             |
| 400            | E1006     | Đơn vị chưa thiết lập website                                           |
| 401, 403       |           | Unauthorized                                                            |
| 500            |           | Lỗi hệ thống JETPAY                                                     |

**Ví dụ**

Request Body

```json
{
	"org": {
		"taxNo": "123456"
	},
	"bank": {
		"bankCode": "VTB",
		"bankAccountNumber": "123456"
	},
	"order": {
		"orderId": "123",
		"amount": 10000,
		"domainWebsite": "",
		"exportInvoice": true,
		"additionalData": {
			"products": [
				{
					"productCode": "1",
					"quantity": 1,
					"unit": "cái",
					"buildingCode": "123",
					"saleContract": "123",
					"extendedField": "bank"
				}
			]
		}
	},
	"customer": {
		"code": "KH0000001",
		"taxNo": "",
		"name": "Nguyen Van A",
		"email": "nva@example.com",
		"phoneNumber": "0912345678",
		"address": ""
	}
}
```

Response Body `HttpStatusCode = 200`

```json
{
	"bank": {
		"bankCode": "VTB",
		"bankName": "Ngân hàng TMCP Công Thương Việt Nam",
		"bankAccountNumber": "123456",
		"bankAccountName": "CTCP ABC",
		"branchName": "Hà Nội",
		"bankLogoUrl": ""
	},
	"order": {
		"orderId": "123",
		"amount": 10000,
		"purpose": "DH123",
    "vietQrUrl": ""
	}
}
```

Response Body `HttpStatusCode = 400`

#### 3.2.3 API nhận kết quả xác nhận đơn hàng thanh toán qua Webhook

API đặc tả thông báo xác nhận đơn hàng khi có biến động số dư trên tài khoản đã được kết nối JETPAY BankHub.

API này bên đối tác xây dựng.

**URL**

Đối tác cung cấp và thiết lập trong webhook trên trang quản lý giao dịch JETPAY BankHub cung cấp cho đơn vị

**Method**

`POST`

**Content-Type**

`application/json`

**Request Body**

* `header`
  * `msgId` _**string**_: id của message request, duy nhất trên mỗi lần gửi
  * `signature` _**string**_: chữ ký số cho request, trong đó `signData = key + msgId`, thuật toán ký số **MD5**
* `data`: dữ liệu lịch sử giao dịch map với thông tin đơn hàng
  * `records` _**Array**_: Danh sách lịch sử giao dịch, mỗi một lịch sử giao dịch bao gồm
    * `orderConfirmType` _**int**_: Cách thức xác nhận đơn hàng (**1**: tự động, **2**: thủ công)
    * `orderId` _**string**_: Mã đơn hàng
    * `amount` _**long**_: Số tiền giao dịch
    * `description` _**string**_: Nội dung chuyển khoản
    * `transId` _**string**_: Id giao dịch trên hệ thống của JETPAY BankHub
    * `bankTransId` _**string**_: Id giao dịch trên ngân hàng
    * `transTime` _**DateTime**_: Thời gian giao dịch

**Response Body**

Đầu API này sẽ chỉ nhận `HttpStatusCode = 200` , nếu `HttpStatusCode # 200` thì hệ thống của đơn vị chưa nhận được request trên

* `header`
  * `msgId` _**string**_: id của message request
  * `responseMsgId` _**string**_: id của message response, là mã duy nhất trên mỗi response, hệ thống của đơn vị tự định nghĩa
  * `signature` _**string**_: chữ ký số cho response, trong đó `signData = key + msgId + responseMsgId`, thuật toán ký số **MD5**
* `data`: dữ liệu lịch sử giao dịch map với thông tin đơn hàng
  * `records` _**Array**_: Danh sách lịch sử giao dịch, mỗi một lịch sử giao dịch bao gồm
    * `transId` _**string**_: Id giao dịch trên hệ thống của JETPAY BankHub
    * `error` _**int**_: Mã lỗi khi nhận lịch sử giao dịch (**0: thành công, 1: mã đơn hàng không hợp lệ, khác là thất bại**)
    * `message` _**string**_: Mô tả chi tiết cho mã lỗi khi nhận lịch sử giao dịch
    * `orderAmount` _**long**_: Số tiền thực của đơn hàng

**Ví dụ**

Request Body

```json
{
	"header": {
		"msgId": "20230710135000",
		"signature": ""
	},
	"data": {
		"records": [
			{
				"orderConfirmType": 1,
				"orderId": "123",
				"amount": 10000,
				"description": "DH123",
				"transId": "JP1688971897877",
				"bankTransId": "1688971922410"
			}
		]
	}
}
```

Response Body

```json
{
	"header": {
		"msgId": "20230710135000",
		"responseMsgId": "20230710135001",
		"signature": ""
	},
	"data": {
		"records": [
			{
				"transId": "JP1688971897877",
				"error": 0,
				"message": "Success",
				"orderAmount": 10000
			}
		]
	}
}
```
