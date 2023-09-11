# Khởi tạo giao dịch

API được gọi từ website của đơn vị sang JETPAY để khởi tạo giao dịch chuyển khoản theo cú pháp cho đơn hàng.

[**Thông tin domain**](../thong-tin-tich-hop/thong-tin-domain.md) **và** [**ngân hàng hỗ trợ**](../thong-tin-tich-hop/ngan-hang-ho-tro.md) **tích hợp.**

**URL**

`POST {{jbf_domain}}/orgs/transactions`

**Content-Type**

`application/json`

**Headers**

<table><thead><tr><th width="161.33333333333331">Tên header</th><th width="159">Giá trị</th><th>Ý nghĩa</th></tr></thead><tbody><tr><td>Authorization</td><td>Bearer {token}</td><td>Header chứa token xác thực ứng dụng tích hợp JETPAY BankHub.</td></tr></tbody></table>

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

**Thông tin hàng hóa**

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
