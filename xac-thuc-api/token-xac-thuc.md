---
description: Đặc tả API lấy thông tin token xác thực hệ thống JETPAY BankHub
---

# Token xác thực

[**Thông tin domain**](../thong-tin-tich-hop/thong-tin-domain.md) **và** [**ngân hàng hỗ trợ**](../thong-tin-tich-hop/ngan-hang-ho-tro.md) **tích hợp.**

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
