# Xác nhận đơn hàng

API đặc tả thông báo xác nhận đơn hàng khi có biến động số dư trên tài khoản đã được kết nối JETPAY BankHub.

API này bên đối tác xây dựng.

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
