# เอกสาร APIs

# โดเมน Domain

| Environment | Domain                             |
| ----------- | ---------------------------------- |
| Production  | https://post.postway.co.th         |
| Staging     | https://demo-post.postway.co.th    |
| Sandbox     | https://sandbox-post.postway.co.th |

# HTTP Status Code

| Code | รายละเอียด                                       |
| ---- | ----------------------------------------------- |
| 2xx  | ทำรายการสำเร็จ                                     |
| 4xx  | ข้อมูลผิดพลาด ไม่มีสิทธิ์ ไม่ได้รับ Access Token           |
| 5xx  | ผิดพลาดในระบบ Server หรือ อยู่ในระหว่างระบบ Maintain |

## HTTP Status 4xx

ไม่ได้รับสิทธิ์ในการเข้าถึง หรือ Unauthorization

```json
{
  "message": "Forbidden resource",
  "error": "Forbidden",
  "statusCode": 403
}
```

## HTTP Status 5xx

เกิดข้อผิดพลาดที่ Server

```json
{
  "message": "Internal server error",
  "statusCode": 500
}
```

กำลังปรับปรุง Server

```json
{
  "message": "Server is under maintenance",
  "statusCode": 513
}
```

# การยืนยันตัวตน และข้อมูลผู้ใช้ (Authentication & Me)

การยืนยันตัวตนเราจะทำการแนบผ่าน Header ของ HTTP Request

## Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

โดยเราจะทดสอบว่า Access Token นี้ทำงานได้ไหม เราจะยิงผ่าน [POST] /merchant/auth/account

## [POST] /merchant/auth/account

Method: `POST`

Authentication: `required`

รายละเอียด: `สำหรับยิงมาเพื่อยืนยันตัวตน และดึงข้อมูลโปรไฟล์ลูกค้า ร้านค้า และ SESSION`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Curl command

```bash
curl -X 'POST' \
  'https://<domain>/merchant/auth/account/info' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer ***' \
  -d ''
```

Response (Success)

| Property                 | Data Type | M/O | Description                               | Example                  |
| ------------------------ | --------- | --- | ----------------------------------------- | ------------------------ |
| user                     | Object    | M   | ข้อมูลผู้ใช้งาน                                |                          |
| user.username            | String    | M   | ชื่อบัญชี                                     | demo_user                |
| user.email               | String    | M   | อีเมล์                                      | demo_user@gmail.com      |
| user.first_name          | String    | M   | ชื่อจริง                                     | USER                     |
| user.last_name           | String    | M   | นามสกุล                                    | TEST                     |
| user.mobile_phone        | String    | M   | เบอร์โทรศัพท์                                | 0992222222               |
| user.role                | String    | M   | Role ของบัญชีนี้ ข้อมูลมี Admin, User และ Public | User                     |
| store                    | Object    | M   | ข้อมูลร้านค้า                                 |                          |
| store.code               | String    | M   | รหัสร้าน                                    | PW227ED1E                |
| store.name               | String    | M   | ชื่อร้าน                                     | store name               |
| store.mobile_phone       | String    | M   | เบอร์โทรศัพท์ร้าน                             | 0992222222               |
| store.store_billing_type | String    | M   | ประเภทการชำระเงินร้าน                        | Top Up                   |
| store.store_cod_type     | String    | M   | ประเภท COD ร้าน                            | receipt                  |
| store.address            | String    | M   | ที่อยู่ร้าน                                    | 11/111                   |
| store.sub_district       | String    | M   | ตำบล                                       | xxx                      |
| store.district           | String    | M   | อำเภอ                                      | xxx                      |
| store.province           | String    | M   | จังหวัด                                     | xxx                      |
| store.zip_code           | String    | M   | รหัสไปรษณีย์                                 | 10300                    |
| session                  | Object    | M   | ข้อมูล session                              |                          |
| session.expired          | String    | M   | วันหมดอายุ session                          | 2026-02-26T15:31:25.144Z |

ตัวอย่าง JSON Response

```json
{
  "user": {
    "username": "demo_user",
    "email": "demo_user@gmail.com",
    "first_name": "USER",
    "last_name": "TEST",
    "mobile_phone": "0992222222",
    "role": "User"
  },
  "store": {
    "code": "PW227ED1E",
    "name": "store name",
    "mobile_phone": "0992222222",
    "store_billing_type": "Top Up",
    "store_cod_type": "receipt",
    "address": "11/111",
    "sub_district": "xxx",
    "district": "xxx",
    "province": "xxx",
    "zip_code": "10300"
  },
  "session": {
    "expired": "2026-02-26T15:31:25.144Z"
  }
}
```

# ใบปะหน้า (Label)

## [POST] /api/label/order/shipments

Method: `POST`

Authentication: `required`

รายละเอียด: `สำหรับสร้างใบปะหน้า`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Request

| Property          | Data Type | M/O | Description                                      | Example                                          |
| ----------------- | --------- | --- | ------------------------------------------------ | ------------------------------------------------ |
| tracking_nos      | String[]  | M   | เลขติดตาม                                         | [PW2507FE08Y8DX, PW2507FE08Y8DY, PW2507FE08Y8DZ] |
| label_size        | String    | O   | ขนาดใบปะหน้า (Enum: 4x3, 4x4, 4x6, 6x4, A4, 80mm) | 6x4                                              |
| label_orientation | String    | O   | แนวใบปะหน้า (Enum: Portrait, Landscape)           | Landscape                                        |

ตัวอย่าง JSON Request

```json
{
  "tracking_nos": ["PW2507FE08Y8DX"],
  "label_size": "6x4",
  "label_orientation": "Landscape"
}
```

Curl command

```bash
curl -X 'POST' \
  'https://<domain>/api/label/order/shipments' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer F8D44602A2ACCD0E03E17D99A786C5E0E801D6F088BA8199F35DEBEC463538AF2601C816B4D8C85860DC25663FA095BB14EA' \
  -H 'Content-Type: application/json' \
  -d '{
  "tracking_nos": ["PW2507FE08Y8DX"],
  "label_size": "6x4",
  "label_orientation": "Landscape"
}'
```

Response (Success)

| Property       | Data Type | M/O | Description         | Example                                               |
| -------------- | --------- | --- | ------------------- | ----------------------------------------------------- |
| content        | String    | M   | ไฟล์ใบปะหน้า (Base64) | JVBERi0xLjMKJbrfrOAKMyAwIG9...                        |
| content_length | Number    | M   | ขนาดไฟล์ (ไบต์)       | 91176                                                 |
| content_type   | String    | M   | ประเภทไฟล์           | application/pdf                                       |
| file_name      | String    | M   | ชื่อไฟล์               | 37d783da-0874-41db-b265-f0fba8cdd227_TH2101F3FP5K.pdf |

ตัวอย่าง JSON Response

```json
{
  "content": "JVBERi0xLjMKJbrfrOAKMyAwIG9...4cmVmCjkwMzM4CiUlRU9G", // -> base64 binary file
  "content_length": 91176,
  "content_type": "application/pdf",
  "file_name": "37d783da-0874-41db-b265-f0fba8cdd227_TH2101F3FP5K.pdf"
}
```

# รายการขนส่ง (Order shipment)

## [GET] /merchant/order-shipment/get-by-tracking-no/{tracking_no}

Method: `GET`

Authentication: `required`

รายละเอียด: `ดึงข้อมูลรายการขนส่งด้วย tracking_no`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Parameter request

| Property    | Data Type | M/O | Description | Example        |
| ----------- | --------- | --- | ----------- | -------------- |
| tracking_no | String    | M   | เลขติดตาม    | PW2507FE08Y8DX |

Curl command

```bash
curl -X 'GET' \
  'https://<domain>/merchant/order-shipment/get-by-tracking-no/PW2507FE08Y8DX' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer F8D44602A2ACCD0E03E17D99A786C5E0E801D6F088BA8199F35DEBEC463538AF2601C816B4D8C85860DC25663FA095BB14EA'
```

Response (Success)

| Property               | Data Type | M/O | Description      | Example                  |
| ---------------------- | --------- | --- | ---------------- | ------------------------ |
| channel                | String    | M   | ช่องทาง           | V1                       |
| sender                 | Object    | M   | ข้อมูลผู้ส่ง          | {...}                    |
| sender.fullname        | String    | M   | ชื่อผู้ส่ง            | SENDER NAME              |
| sender.mobile_phone    | String    | M   | เบอร์โทรศัพท์ผู้ส่ง    | 099xxxxxxx               |
| sender.address         | String    | M   | ที่อยู่ผู้ส่ง           | 11/111                   |
| sender.sub_district    | String    | M   | ตำบลผู้ส่ง           | xxx                      |
| sender.district        | String    | M   | อำเภอผู้ส่ง          | xxx                      |
| sender.province        | String    | M   | จังหวัดผู้ส่ง         | xxx                      |
| sender.zip_code        | String    | M   | รหัสไปรษณีย์ผู้ส่ง     | 10130                    |
| recipient              | Object    | M   | ข้อมูลผู้รับ          | {...}                    |
| recipient.fullname     | String    | M   | ชื่อผู้รับ            | RECIPIENT NAME           |
| recipient.mobile_phone | String    | M   | เบอร์โทรศัพท์ผู้รับ    | 099xxxxxxx               |
| recipient.address      | String    | M   | ที่อยู่ผู้รับ           | 11/111                   |
| recipient.sub_district | String    | M   | ตำบลผู้รับ           | xxx                      |
| recipient.district     | String    | M   | อำเภอผู้รับ          | xxx                      |
| recipient.province     | String    | M   | จังหวัดผู้รับ         | xxx                      |
| recipient.zip_code     | String    | M   | รหัสไปรษณีย์ผู้รับ     | 10130                    |
| package                | Object    | M   | ข้อมูลพัสดุ          | {...}                    |
| package.my_tracking_no | String    | O   | รหัสติดตามของฉัน    |                          |
| package.tracking_no    | String    | M   | รหัสติดตามพัสดุ      | PW2507GSO2U9LHA          |
| package.ref1           | String    | O   | รหัสอ้างอิง 1       | TH2101F3VF4KA            |
| package.ref2           | String    | O   | รหัสอ้างอิง 2       |                          |
| package.ref3           | String    | O   | รหัสอ้างอิง 3       |                          |
| package.weight         | Number    | M   | น้ำหนักพัสดุ (กรัม)    | 455                      |
| package.width          | Number    | M   | ความกว้าง (ซม.)   | 1                        |
| package.length         | Number    | M   | ความยาว (ซม.)    | 1                        |
| package.height         | Number    | M   | ความสูง (ซม.)     | 1                        |
| order_shipment_status  | String    | M   | สถานะการขนส่ง     | WaitForDropOff           |
| status                 | String    | M   | สถานะ            | on processing            |
| completed_at           | String    | O   | เวลาสำเร็จ         | null                     |
| in_transit_at          | String    | O   | เวลาอยู่ระหว่างขนส่ง | null                     |
| created_at             | String    | M   | เวลาสร้าง         | 2025-07-10T03:27:13.191Z |
| updated_at             | String    | M   | เวลาอัปเดต        | 2025-07-10T03:27:14.248Z |

ตัวอย่าง JSON Response

```json
{
    "channel": "V1",
    "sender": {
    "fullname": "SENDER NAME",
    "mobile_phone": "099xxxxxxx",
    "address": "11/111",
    "sub_district": "xxx",
    "district": "xxx",
    "province": "xxx",
    "zip_code": "10130"
    },
    "recipient": {
    "fullname": "RECIPIENT NAME",
    "mobile_phone": "099xxxxxxx",
    "address": "11/111",
    "sub_district": "xxx",
    "district": "xxx",
    "province": "xxx",
    "zip_code": "10130"
    },
    "package": {
    "my_tracking_no": "",
    "tracking_no": "PW2507GSO2U9LH",
    "ref1": "TH2101F3VF4K",
    "ref2": "",
    "ref3": "",
    "weight": 455,
    "width": 1,
    "length": 1,
    "height": 1
    },
    "order_shipment_status": "WaitForDropOff",
    "status": "on processing",
    "completed_at": null,
    "in_transit_at": null,
    "created_at": "2025-07-10T03:27:13.191Z",
    "updated_at": "2025-07-10T03:27:14.248Z"
}
```

## [GET] /merchant/order-shipment/get-by-ref/{ref}

Method: `GET`

Authentication: `required`

รายละเอียด: `ดึงข้อมูลรายการขนส่งด้วย reference ทั้ง ref1, ref2 และ ref3`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Parameter request

| Property | Data Type | M/O | Description  | Example        |
| -------- | --------- | --- | ------------ | -------------- |
| ref      | String    | M   | เลขติดตามขนส่ง | PW2507FE08Y8DX |

Curl command

```bash
curl -X 'GET' \
  'https://<domain>/merchant/order-shipment/get-by-ref/TH2101F3FP5K' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer F8D44602A2ACCD0E03E17D99A786C5E0E801D6F088BA8199F35DEBEC463538AF2601C816B4D8C85860DC25663FA095BB14EA'
```

Response (Success)

| Property               | Data Type | M/O | Description      | Example                  |
| ---------------------- | --------- | --- | ---------------- | ------------------------ |
| channel                | String    | M   | ช่องทาง           | V1                       |
| sender                 | Object    | M   | ข้อมูลผู้ส่ง          | {...}                    |
| sender.fullname        | String    | M   | ชื่อผู้ส่ง            | SENDER NAME              |
| sender.mobile_phone    | String    | M   | เบอร์โทรศัพท์ผู้ส่ง    | 099xxxxxxx               |
| sender.address         | String    | M   | ที่อยู่ผู้ส่ง           | 11/111                   |
| sender.sub_district    | String    | M   | ตำบลผู้ส่ง           | xxx                      |
| sender.district        | String    | M   | อำเภอผู้ส่ง          | xxx                      |
| sender.province        | String    | M   | จังหวัดผู้ส่ง         | xxx                      |
| sender.zip_code        | String    | M   | รหัสไปรษณีย์ผู้ส่ง     | 10130                    |
| recipient              | Object    | M   | ข้อมูลผู้รับ          | {...}                    |
| recipient.fullname     | String    | M   | ชื่อผู้รับ            | RECIPIENT NAME           |
| recipient.mobile_phone | String    | M   | เบอร์โทรศัพท์ผู้รับ    | 099xxxxxxx               |
| recipient.address      | String    | M   | ที่อยู่ผู้รับ           | 11/111                   |
| recipient.sub_district | String    | M   | ตำบลผู้รับ           | xxx                      |
| recipient.district     | String    | M   | อำเภอผู้รับ          | xxx                      |
| recipient.province     | String    | M   | จังหวัดผู้รับ         | xxx                      |
| recipient.zip_code     | String    | M   | รหัสไปรษณีย์ผู้รับ     | 10130                    |
| package                | Object    | M   | ข้อมูลพัสดุ          | {...}                    |
| package.my_tracking_no | String    | O   | รหัสติดตามของฉัน    |                          |
| package.tracking_no    | String    | M   | รหัสติดตามพัสดุ      | PW2507GSO2U9LHA          |
| package.ref1           | String    | O   | รหัสอ้างอิง 1       | TH2101F3VF4KA            |
| package.ref2           | String    | O   | รหัสอ้างอิง 2       |                          |
| package.ref3           | String    | O   | รหัสอ้างอิง 3       |                          |
| package.weight         | Number    | M   | น้ำหนักพัสดุ (กรัม)    | 455                      |
| package.width          | Number    | M   | ความกว้าง (ซม.)   | 1                        |
| package.length         | Number    | M   | ความยาว (ซม.)    | 1                        |
| package.height         | Number    | M   | ความสูง (ซม.)     | 1                        |
| order_shipment_status  | String    | M   | สถานะการขนส่ง     | WaitForDropOff           |
| status                 | String    | M   | สถานะ            | on processing            |
| completed_at           | String    | O   | เวลาสำเร็จ         | null                     |
| in_transit_at          | String    | O   | เวลาอยู่ระหว่างขนส่ง | null                     |
| created_at             | String    | M   | เวลาสร้าง         | 2025-07-10T03:27:13.191Z |
| updated_at             | String    | M   | เวลาอัปเดต        | 2025-07-10T03:27:14.248Z |

ตัวอย่าง JSON Response

```json
{
    "channel": "V1",
    "sender": {
    "fullname": "SENDER NAME",
    "mobile_phone": "099xxxxxxx",
    "address": "11/111",
    "sub_district": "xxx",
    "district": "xxx",
    "province": "xxx",
    "zip_code": "10130"
    },
    "recipient": {
    "fullname": "RECIPIENT NAME",
    "mobile_phone": "099xxxxxxx",
    "address": "11/111",
    "sub_district": "xxx",
    "district": "xxx",
    "province": "xxx",
    "zip_code": "10130"
    },
    "package": {
    "my_tracking_no": "",
    "tracking_no": "PW2507GSO2U9LH",
    "ref1": "TH2101F3VF4K",
    "ref2": "",
    "ref3": "",
    "weight": 455,
    "width": 1,
    "length": 1,
    "height": 1
    },
    "order_shipment_status": "WaitForDropOff",
    "status": "on processing",
    "completed_at": null,
    "in_transit_at": null,
    "created_at": "2025-07-10T03:27:13.191Z",
    "updated_at": "2025-07-10T03:27:14.248Z"
}
```

## [POST] /merchant/order-shipment/filter

Method: `POST`

Authentication: `required`

รายละเอียด: `สำหรับยิงมาเพื่อดึงข้อมูลรายการขนส่งของร้านค้าดังกล่าว`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Request

| Property | Data Type | M/O | Description | Example |
| -------- | --------- | --- | ----------- | ------- |
| filter   | Object    | O   | คำค้นหา       |         |
| limit    | Number    | M   | จำนวนต่อหน้า   | 10      |
| page     | Number    | M   | หน้าที่        | 1       |

ตัวอย่าง JSON Request

```json
{
  "filter": "",
  "limit": 10,
  "page": 1
}
```

Curl command

```bash
curl -X 'POST' \
  'https://<domain>/api/order-shipment/filter' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer F8D44602A2ACCD0E03E17D99A786C5E0E801D6F088BA8199F35DEBEC463538AF2601C816B4D8C85860DC25663FA095BB14EA' \
  -H 'Content-Type: application/json' \
  -d '{
  "filter": "",
  "limit": 10,
  "page": 1
}'
```

Response (Success)

| Property                    | Data Type | M/O | Description      | Example                  |
| --------------------------- | --------- | --- | ---------------- | ------------------------ |
| count                       | Number    | M   | จำนวนที่มีอยู่ในระบบ   | 1692                     |
| data                        | Array     | M   | รายการขนส่ง       | [ ... ]                  |
| data.channel                | String    | M   | ช่องทาง           | V1                       |
| data.sender                 | Object    | M   | ข้อมูลผู้ส่ง          | {...}                    |
| data.sender.fullname        | String    | M   | ชื่อผู้ส่ง            | SENDER NAME              |
| data.sender.mobile_phone    | String    | M   | เบอร์โทรศัพท์ผู้ส่ง    | 099xxxxxxx               |
| data.sender.address         | String    | M   | ที่อยู่ผู้ส่ง           | 11/111                   |
| data.sender.sub_district    | String    | M   | ตำบลผู้ส่ง           | xxx                      |
| data.sender.district        | String    | M   | อำเภอผู้ส่ง          | xxx                      |
| data.sender.province        | String    | M   | จังหวัดผู้ส่ง         | xxx                      |
| data.sender.zip_code        | String    | M   | รหัสไปรษณีย์ผู้ส่ง     | 10130                    |
| data.recipient              | Object    | M   | ข้อมูลผู้รับ          | {...}                    |
| data.recipient.fullname     | String    | M   | ชื่อผู้รับ            | RECIPIENT NAME           |
| data.recipient.mobile_phone | String    | M   | เบอร์โทรศัพท์ผู้รับ    | 099xxxxxxx               |
| data.recipient.address      | String    | M   | ที่อยู่ผู้รับ           | 11/111                   |
| data.recipient.sub_district | String    | M   | ตำบลผู้รับ           | xxx                      |
| data.recipient.district     | String    | M   | อำเภอผู้รับ          | xxx                      |
| data.recipient.province     | String    | M   | จังหวัดผู้รับ         | xxx                      |
| data.recipient.zip_code     | String    | M   | รหัสไปรษณีย์ผู้รับ     | 10130                    |
| data.package                | Object    | M   | ข้อมูลพัสดุ          | {...}                    |
| data.package.my_tracking_no | String    | O   | รหัสติดตามของฉัน    |                          |
| data.package.tracking_no    | String    | M   | รหัสติดตามพัสดุ      | PW2507GSO2U9LHA          |
| data.package.ref1           | String    | O   | รหัสอ้างอิง 1       | TH2101F3VF4KA            |
| data.package.ref2           | String    | O   | รหัสอ้างอิง 2       |                          |
| data.package.ref3           | String    | O   | รหัสอ้างอิง 3       |                          |
| data.package.weight         | Number    | M   | น้ำหนักพัสดุ (กรัม)    | 455                      |
| data.package.width          | Number    | M   | ความกว้าง (ซม.)   | 1                        |
| data.package.length         | Number    | M   | ความยาว (ซม.)    | 1                        |
| data.package.height         | Number    | M   | ความสูง (ซม.)     | 1                        |
| data.order_shipment_status  | String    | M   | สถานะการขนส่ง     | WaitForDropOff           |
| data.status                 | String    | M   | สถานะ            | on processing            |
| data.completed_at           | String    | O   | เวลาสำเร็จ         | null                     |
| data.in_transit_at          | String    | O   | เวลาอยู่ระหว่างขนส่ง | null                     |
| data.created_at             | String    | M   | เวลาสร้าง         | 2025-07-10T03:27:13.191Z |
| data.updated_at             | String    | M   | เวลาอัปเดต        | 2025-07-10T03:27:14.248Z |
| limit                       | Number    | M   | จำนวนต่อหน้า        | 10                       |
| page                        | Number    | M   | หน้าที่             | 1                        |
| page_count                  | Number    | M   | จำนวนหน้าทั้งหมด     | 170                      |

ตัวอย่าง JSON Response

```json
{
  "count": 1692,
  "data": [
    {
      "channel": "V1",
      "sender": {
        "fullname": "SENDER NAME",
        "mobile_phone": "099xxxxxxx",
        "address": "11/111",
        "sub_district": "xxx",
        "district": "xxx",
        "province": "xxx",
        "zip_code": "10130"
      },
      "recipient": {
        "fullname": "RECIPIENT NAME",
        "mobile_phone": "099xxxxxxx",
        "address": "11/111",
        "sub_district": "xxx",
        "district": "xxx",
        "province": "xxx",
        "zip_code": "10130"
      },
      "package": {
        "my_tracking_no": "",
        "tracking_no": "PW2507GSO2U9LH",
        "ref1": "TH2101F3VF4K",
        "ref2": "",
        "ref3": "",
        "weight": 455,
        "width": 1,
        "length": 1,
        "height": 1
      },
      "order_shipment_status": "WaitForDropOff",
      "status": "on processing",
      "completed_at": null,
      "in_transit_at": null,
      "created_at": "2025-07-10T03:27:13.191Z",
      "updated_at": "2025-07-10T03:27:14.248Z"
    },
    ...
  ],
  "limit": 10,
  "page": 1,
  "page_count": 170
}
```

## [POST] /api/order-shipment/create

Method: `POST`

Authentication: `required`

รายละเอียด: `สร้างรายการขนส่ง`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Request

| Property                        | Data Type            | M/O | Description    | Example             |
| ------------------------------- | -------------------- | --- | -------------- | ------------------- |
| shipping                        | Object               | M   | ข้อมูลขนส่ง       |                     |
| shipping.shipment_provider_name | String               | M   | ชื่อขนส่ง         | Flash               |
| shipping.my_tracking_no         | String               | O   | รหัสติดตามของฉัน  | string              |
| sender                          | Object               | M   | ข้อมูลผู้ส่ง        |                     |
| sender.fullname                 | String               | M   | ชื่อผู้ส่ง          | TEST API            |
| sender.email                    | String               | O   | อีเมลผู้ส่ง        |                     |
| sender.mobile_phone             | String               | M   | เบอร์โทรศัพท์ผู้ส่ง  | 0999999999          |
| sender.card_no                  | String               | O   | เลขบัตรประชาชน  |                     |
| sender.address                  | String               | M   | ที่อยู่ผู้ส่ง         | 11/111              |
| sender.sub_district             | String               | M   | ตำบลผู้ส่ง         | รามอินทรา            |
| sender.district                 | String               | M   | อำเภอผู้ส่ง        | คันนายาว             |
| sender.province                 | String               | M   | จังหวัดผู้ส่ง       | กรุงเทพ              |
| sender.zip_code                 | String               | M   | รหัสไปรษณีย์ผู้ส่ง   | 10230               |
| recipient                       | Object               | M   | ข้อมูลผู้รับ        |                     |
| recipient.fullname              | String               | M   | ชื่อผู้รับ          | Recipient Name      |
| recipient.email                 | String               | O   | อีเมลผู้รับ        |                     |
| recipient.mobile_phone          | String               | M   | เบอร์โทรศัพท์ผู้รับ  | 0299999999          |
| recipient.address               | String               | M   | ที่อยู่ผู้รับ         | 22/222              |
| recipient.sub_district          | String               | M   | ตำบลผู้รับ         | ทับมา                |
| recipient.district              | String               | M   | อำเภอผู้รับ        | เมืองระยอง           |
| recipient.province              | String               | M   | จังหวัดผู้รับ       | ระยอง               |
| recipient.zip_code              | String               | M   | รหัสไปรษณีย์ผู้รับ   | 21000               |
| package                         | Object               | M   | ข้อมูลพัสดุ        |                     |
| package.insurance_value         | Number               | O   | มูลค่าประกัน      | 0                   |
| package.note                    | String               | O   | หมายเหตุ        | Test API by POSTWAY |
| package.type                    | FlashArticleCategory | O   | ประเภทพัสดุ      | 99                  |
| package.weight                  | Number               | M   | น้ำหนัก (กรัม)     | 567                 |
| package.width                   | Number               | M   | กว้าง (ซม.)     | 1                   |
| package.height                  | Number               | M   | สูง (ซม.)       | 2                   |
| package.length                  | Number               | M   | ยาว (ซม.)      | 3                   |
| product_cods                    | Array                | M   | รายการสินค้า COD |                     |
| product_cods.name               | String               | M   | ชื่อสินค้า         | เครื่องสำอาง           |
| product_cods.amount             | Number               | M   | จำนวน           | 1                   |
| product_cods.price_per_item     | Number               | M   | ราคาต่อชิ้น       | 100                 |

ตัวอย่าง JSON Request

```json
[
    {
        "shipping": {
            "shipment_provider_name": "Flash",
            "my_tracking_no": "string"
        },
        "sender": {
            "fullname": "TEST API",
            "email": "",
            "mobile_phone": "0999999999",
            "card_no": "",
            "address": "11/111",
            "sub_district": "รามอินทรา",
            "district": "คันนายาว",
            "province": "กรุงเทพ",
            "zip_code": "10230"
        },
        "recipient": {
            "fullname": "Recipient Name",
            "email": "",
            "mobile_phone": "0299999999",
            "address": "22/222",
            "sub_district": "ทับมา",
            "district": "เมืองระยอง",
            "province": "ระยอง",
            "zip_code": "21000"
        },
        "package": {
            "insurance_value": 0,
            "note": "Test API by POSTWAY",
            "type": 99,
            "weight": 567,
            "width": 1,
            "height": 2,
            "length": 3
        },
        "product_cods": [
            {
            "name": "เครื่องสำอาง",
            "amount": 1,
            "price_per_item": 100
            }
        ]
    }
  ]
```

Curl command

```bash
curl -X 'POST' \
  'https://<domain>/merchant/order-shipment/create' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer F8D44602A2ACCD0E03E17D99A786C5E0E801D6F088BA8199F35DEBEC463538AF2601C816B4D8C85860DC25663FA095BB14EA' \
  -H 'Content-Type: application/json' \
  -d '[
    {
        "shipping": {
            "shipment_provider_name": "Flash",
            "my_tracking_no": "string"
        },
        "sender": {
            "fullname": "TEST API",
            "email": "",
            "mobile_phone": "0999999999",
            "card_no": "",
            "address": "11/111",
            "sub_district": "รามอินทรา",
            "district": "คันนายาว",
            "province": "กรุงเทพ",
            "zip_code": "10230"
        },
        "recipient": {
            "fullname": "Recipient Name",
            "email": "",
            "mobile_phone": "0299999999",
            "address": "22/222",
            "sub_district": "ทับมา",
            "district": "เมืองระยอง",
            "province": "ระยอง",
            "zip_code": "21000"
        },
        "package": {
            "insurance_value": 0,
            "note": "Test API by POSTWAY",
            "type": 99,
            "weight": 567,
            "width": 1,
            "height": 2,
            "length": 3
        },
        "product_cods": [
            {
            "name": "เครื่องสำอาง",
            "amount": 1,
            "price_per_item": 100
            }
        ]
    }
  ]'
```

Response (Success)

| Property                    | Data Type | M/O | Description      | Example                  |
| --------------------------- | --------- | --- | ---------------- | ------------------------ |
| code                        | Number    | M   | รหัสสถานะ         | 200                      |
| data                        | Array     | M   |                  |                          |
| data.channel                | String    | M   | ช่องทาง           | API                      |
| data.sender                 | Object    | M   | ข้อมูลผู้ส่ง          | {...}                    |
| data.sender.fullname        | String    | M   | ชื่อผู้ส่ง            | TEST API                 |
| data.sender.mobile_phone    | String    | M   | เบอร์โทรศัพท์ผู้ส่ง    | 0999999999               |
| data.sender.address         | String    | M   | ที่อยู่ผู้ส่ง           | 11/111                   |
| data.sender.sub_district    | String    | M   | ตำบลผู้ส่ง           | รามอินทรา                 |
| data.sender.district        | String    | M   | อำเภอผู้ส่ง          | คันนายาว                  |
| data.sender.province        | String    | M   | จังหวัดผู้ส่ง         | กรุงเทพ                   |
| data.sender.zip_code        | String    | M   | รหัสไปรษณีย์ผู้ส่ง     | 10230                    |
| data.recipient              | Object    | M   | ข้อมูลผู้รับ          | {...}                    |
| data.recipient.fullname     | String    | M   | ชื่อผู้รับ            | Recipient Name           |
| data.recipient.mobile_phone | String    | M   | เบอร์โทรศัพท์ผู้รับ    | 0299999999               |
| data.recipient.address      | String    | M   | ที่อยู่ผู้รับ           | 22/222                   |
| data.recipient.sub_district | String    | M   | ตำบลผู้รับ           | ทับมา                     |
| data.recipient.district     | String    | M   | อำเภอผู้รับ          | เมืองระยอง                |
| data.recipient.province     | String    | M   | จังหวัดผู้รับ         | ระยอง                    |
| data.recipient.zip_code     | String    | M   | รหัสไปรษณีย์ผู้รับ     | 21000                    |
| data.package                | Object    | M   | ข้อมูลพัสดุ          | {...}                    |
| data.package.my_tracking_no | String    | O   | รหัสติดตามของฉัน    | string                   |
| data.package.tracking_no    | String    | M   | รหัสติดตามพัสดุ      | PW2507EZ12EFQ3           |
| data.package.ref1           | String    | O   | รหัสอ้างอิง 1       |                          |
| data.package.ref2           | String    | O   | รหัสอ้างอิง 2       |                          |
| data.package.ref3           | String    | O   | รหัสอ้างอิง 3       |                          |
| data.package.weight         | Number    | M   | น้ำหนักพัสดุ (กรัม)    | 567                      |
| data.package.width          | Number    | M   | ความกว้าง (ซม.)   | 1                        |
| data.package.length         | Number    | M   | ความยาว (ซม.)    | 3                        |
| data.package.height         | Number    | M   | ความสูง (ซม.)     | 2                        |
| data.order_shipment_status  | String    | M   | สถานะการขนส่ง     | WaitForDropOff           |
| data.status                 | String    | M   | สถานะ            | on processing            |
| data.completed_at           | String    | O   | เวลาสำเร็จ         | null                     |
| data.in_transit_at          | String    | O   | เวลาอยู่ระหว่างขนส่ง | null                     |
| data.created_at             | String    | M   | เวลาสร้าง         | 2025-07-17T14:59:56.747Z |
| data.updated_at             | String    | M   | เวลาอัปเดต        | 2025-07-17T14:59:57.440Z |
| isSuccess                   | Boolean   | M   | สำเร็จหรือไม่        | true                     |
| message                     | String    | M   | ข้อความ           | ok                       |

ตัวอย่าง JSON Response

```json
{
  "code": 200,
  "data": [
    {
      "channel": "API",
      "sender": {
        "fullname": "TEST API",
        "mobile_phone": "0999999999",
        "address": "11/111",
        "sub_district": "รามอินทรา",
        "district": "คันนายาว",
        "province": "กรุงเทพ",
        "zip_code": "10230"
      },
      "recipient": {
        "fullname": "Recipient Name",
        "mobile_phone": "0299999999",
        "address": "22/222",
        "sub_district": "ทับมา",
        "district": "เมืองระยอง",
        "province": "ระยอง",
        "zip_code": "21000"
      },
      "package": {
        "my_tracking_no": "string",
        "tracking_no": "PW25075HDPTOL9",
        "ref1": "TH2101F4M93K",
        "ref2": "",
        "ref3": "",
        "weight": 567,
        "width": 1,
        "length": 3,
        "height": 2
      },
      "order_shipment_status": "WaitForDropOff",
      "status": "on processing",
      "completed_at": null,
      "in_transit_at": null,
      "created_at": "2025-07-17T16:08:31.996Z",
      "updated_at": "2025-07-17T16:08:32.945Z"
    }
  ],
  "isSuccess": true,
  "message": "ok"
}
```

## [POST] /merchant/order-shipment/calculate-price

Method: `POST`

Authentication: `required`

รายละเอียด: `คำนวณราคารายการขนส่ง`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Request

| Property       | Data Type | M/O | Description   | Example   |
| -------------- | --------- | --- | ------------- | --------- |
| shipment_name  | String    | M   | ชื่อขนส่ง        | Flash     |
| r_sub_district | String    | M   | ตำบล           | ทับมา      |
| r_district     | String    | M   | อำเภอ          | เมืองระยอง |
| r_province     | String    | M   | จังหวัด         | ระยอง     |
| r_zip_code     | String    | M   | รหัสไปรษณีย์     | 21000     |
| p_weight       | Number    | M   | น้ำหนักพัสดุ (กรัม) | 567       |
| p_width        | Number    | M   | กว้าง (CM)     | 1         |
| p_height       | Number    | M   | กว้าง (CM)     | 1         |
| p_length       | Number    | M   | กว้าง (CM)     | 1         |
| p_cod          | Number    | M   | มูลค่า COD      | 100       |
| p_insurance    | Number    | M   | มูลค่าประกัน     | 2500      |

ตัวอย่าง JSON Request

```json
{
  "shipment_name": "Flash",
  "r_sub_district": "ทับมา",
  "r_district": "เมืองระยอง",
  "r_province": "ระยอง",
  "r_zip_code": "21000",
  "p_weight": 567,
  "p_width": 1,
  "p_height": 1,
  "p_length": 1,
  "p_cod": 100,
  "p_insurance": 2500
}
```

Curl command

```bash
curl -X 'POST' \
  'https://<domain>/merchant/order-shipment/calculate-price' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer F8D44602A2ACCD0E03E17D99A786C5E0E801D6F088BA8199F35DEBEC463538AF2601C816B4D8C85860DC25663FA095BB14EA' \
  -H 'Content-Type: application/json' \
  -d '{
  "shipment_name": "Flash",
  "r_sub_district": "ทับมา",
  "r_district": "เมืองระยอง",
  "r_province": "ระยอง",
  "r_zip_code": "21000",
  "p_weight": 567,
  "p_width": 1,
  "p_height": 1,
  "p_length": 1,
  "p_cod": 100,
  "p_insurance": 2500
}'
```

Response

| Property                       | Data Type        | M/O | Description         | Example |
| ------------------------------ | ---------------- | --- | ------------------- | ------- |
| plan_detail                    | Object           | M   | แผนราคา             |         |
| plan_detail.region             | PlanDetailRegion | M   | พื้นที่                 | UPC     |
| plan_detail.type               | PlanDetailType   | M   | คำนวณด้วย             | weight  |
| plan_detail.max_boundary       | Number           | M   | ค่าคำนวณสูงสุด          | 1000    |
| plan_detail.min_boundary       | Number           | M   | ค่าคำนวณตำ่สุด           | 0       |
| price_infos                    | Array            | M   |                     |         |
| price_infos.description        | String           | M   | คำอธิบายรายการ        | ค่าขนส่ง  |
| price_infos.cost               | Number           | M   | ต้นทุน                |         |
| price_infos.cashback_cost      | Number           | M   | Cashback มูลค่า       |         |
| price_infos.is_reward_cashback | Boolean          | M   | จ่าย Cashback หรือยัง? |         |
| price_infos.total_affliliate   | Number           | M   | ส่งเสริมการขาย        |         |

ตัวอย่าง JSON Response

```json
{
  "plan_detail": {
    "region": "UPC",
    "type": "weight",
    "max_boundary": 1000,
    "min_boundary": 0
  },
  "price_infos": [
    {
      "description": "ค่าขนส่ง",
      "cost": 30,
      "price": 25,
      "cashback_cost": 0,
      "is_reward_cashback": false,
      "total_affliliate": 0
    },
    {
      "description": "ค่า COD",
      "cost": 2,
      "price": 2,
      "cashback_cost": 0,
      "is_reward_cashback": false,
      "total_affliliate": 0
    },
    {
      "description": "ค่าประกัน",
      "cost": 7,
      "price": 7,
      "cashback_cost": 0,
      "is_reward_cashback": false,
      "total_affliliate": 0
    }
  ]
}
```

## [POST] /merchant/order-shipment/cancel

Method: `POST`

Authentication: `required`

รายละเอียด: `ยกเลิกรายการขนส่ง`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Request

| Property    | Data Type | M/O | Description | Example        |
| ----------- | --------- | --- | ----------- | -------------- |
| tracking_no | String    | M   | รหัสติดตาม    | PW25075HDPTOL9 |

ตัวอย่าง JSON Request

```json
{
  "tracking_no": "PW25075HDPTOL9"
}
```

Curl command

```bash
curl -X 'POST' \
  'https://<domain>/merchant/order-shipment/cancel' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer F8D44602A2ACCD0E03E17D99A786C5E0E801D6F088BA8199F35DEBEC463538AF2601C816B4D8C85860DC25663FA095BB14EA' \
  -H 'Content-Type: application/json' \
  -d '{
  "tracking_no": "PW25075HDPTOL9"
}'
```

Response (Success)

| Property  | Data Type | M/O | Description | Example |
| --------- | --------- | --- | ----------- | ------- |
| code      | Number    | M   | รหัสสถานะ    | 200     |
| data      | null      | O   |             |         |
| isSuccess | Boolean   | M   | สำเร็จหรือไม่   | true    |
| message   | String    | M   | ข้อความ      | ok      |

ตัวอย่าง JSON Response

```json
{
  "code": 200,
  "isSuccess": true,
  "message": "ok",
  "data": null
}
```

# ข้อมูลขนส่ง (Shipment provider)

## [GET] /merchant/shipment-provider/all

Method: `GET`

Authentication: `required`

รายละเอียด: `ดึงข้อมูลขนส่งที่ร้านค้านั้นได้รับบริการ`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Curl command

```bash
curl -X 'GET' \
  'https://<domain>/merchant/shipment-provider/all' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer F8D44602A2ACCD0E03E17D99A786C5E0E801D6F088BA8199F35DEBEC463538AF2601C816B4D8C85860DC25663FA095BB14EA'
```

Response (Success)

| Property                      | Data Type | M/O | Description                     | Example |
| ----------------------------- | --------- | --- | ------------------------------- | ------- |
|                               | Array     | M   | Root array element              |         |
| []name                        | String    | M   | Name in used system             | EMS     |
| []display_name                | String    | M   | Display name                    | EMS     |
| []width                       | Object    | M   |                                 |         |
| []width.max                   | Number    | M   | Max width use cm                | 70      |
| []width.min                   | Number    | M   | Min width use cm                | 1       |
| []length                      | Object    | M   |                                 |         |
| []length.max                  | Number    | M   | Max lenght use cm               | 70      |
| []length.min                  | Number    | M   | Min lenght use cm               | 1       |
| []height                      | Object    | M   |                                 |         |
| []height.max                  | Number    | M   | Max height use cm               | 70      |
| []height.min                  | Number    | M   | Min height use cm               | 1       |
| []weight                      | Object    | M   |                                 |         |
| []weight.is_calculate         | Boolean   | M   | Enable calculate with weight    | true    |
| []weight.max                  | Number    | M   | Max weight use gram             | 20000   |
| []weight.min                  | Number    | M   | Min weight use gram             | 1       |
| []cubic                       | Object    | M   |                                 |         |
| []cubic.is_calculate          | Boolean   | M   | Enable calculate with cubic     | false   |
| []cubic.max                   | Number    | M   | Max cubic                       | 0       |
| []cubic.min                   | Number    | M   | Min cubic                       | 0       |
| []dimension                   | Object    | M   |                                 |         |
| []dimension.is_calculate      | Boolean   | M   | Enable calculate with dimension | true    |
| []dimension.max               | Number    | M   | Max sum dimension               | 190     |
| []dimension.min               | Number    | M   | Min sum dimension               | 1       |
| []cod                         | Object    | M   |                                 |         |
| []cod.enable                  | Boolean   | M   | Enable use cod                  | true    |
| []cod.max                     | Number    | M   | Max cod value                   | 50000   |
| []cod.min                     | Number    | M   | Min cod value                   | 0       |
| []insurance                   | Object    | M   |                                 |         |
| []insurance.enable            | Boolean   | M   | Enable use insurance            | true    |
| []insurance.max               | Number    | M   | Max insurance value             | 50000   |
| []insurance.min               | Number    | M   | Min insurance value             | 2001    |
| []fee                         | Object    | M   |                                 |         |
| []fee.cod_postway_to_customer | Number    | M   | COD fee by postway to merchant  | 3       |
| []fee.cod_customer_to_mass    | Number    | M   | COD fee by merchant to customer | 3       |

```json
[
  {
    "name": "EMS",
    "display_name": "EMS",
    "width": {
      "max": 70,
      "min": 1
    },
    "length": {
      "max": 70,
      "min": 1
    },
    "height": {
      "max": 70,
      "min": 1
    },
    "weight": {
      "is_calculate": true,
      "max": 20000,
      "min": 1
    },
    "cubic": {
      "is_calculate": false,
      "max": 0,
      "min": 0
    },
    "dimension": {
      "is_calculate": true,
      "max": 190,
      "min": 1
    },
    "cod": {
      "enable": true,
      "max": 50000,
      "min": 0
    },
    "insurance": {
      "enable": true,
      "max": 50000,
      "min": 2001
    },
    "fee": {
      "cod_postway_to_customer": 3,
      "cod_customer_to_mass": 3
    }
  },
  ...
]
```

# ข้อมูลประเทศไทย (Thailand)

## [POST] /merchant/thailand/filter

Method: `POST`

Authentication: `required`

รายละเอียด: `ดึงข้อมูลพื้นที่ประเทศไทย ตามขนส่งต่างๆ ทั้งข้อมูลพื้นที่ห่างไกล พื้นที่ท่องเที่ยว และพื้นที่เกาะ`

Headers

| Name          | Value                 |
| ------------- | --------------------- |
| Content-Type  | application/json      |
| Authorization | Bearer `access_token` |

Request

| Property                | Data Type | M/O | Description | Example |
| ----------------------- | --------- | --- | ----------- | ------- |
| filter                  | String    | O   | ค้นหาข้อมูล    |         |
| limit                   | Number    | M   | จำนวนต่อหน้า   | 10      |
| page                    | Number    | M   | หน้าที่        | 1       |
| shipment_provider_names | String[]  | O   | รายชื่อขนส่ง   | ["EMS"] |
| sub_district            | String    | O   | ตำบล         | สามเสน  |
| district                | String    | O   | อำเภอ        |         |
| province                | String    | O   | จังหวัด       |         |
| zip_code                | String    | O   | รหัสไปรษณีย์   |         |

ตัวอย่าง JSON Request

```json
{
  "filter": "",
  "limit": 10,
  "page": 1,
  "shipment_provider_names": ["EMS"],
  "sub_district": "",
  "district": "พญาไท",
  "province": "",
  "zip_code": ""
}
```

Curl command

```bash
curl -X 'POST' \
  'https://<domain>/merchant/thailand/filter' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer F8D44602A2ACCD0E03E17D99A786C5E0E801D6F088BA8199F35DEBEC463538AF2601C816B4D8C85860DC25663FA095BB14EA' \
  -H 'Content-Type: application/json' \
  -d '{
  "filter": "",
  "limit": 10,
  "page": 1,
  "shipment_provider_names": ["EMS"],
  "sub_district": "",
  "district": "พญาไท",
  "province": "",
  "zip_code": ""
}'
```

Response (Success)

| Property                     | Data Type | M/O | Description  | Example               |
| ---------------------------- | --------- | --- | ------------ | --------------------- |
| count                        | Number    | M   | จำนวนที่พบ      | 1                     |
| data                         | Arrat     | M   |              |                       |
| data.sub_district            | String    | M   | ตำบล          | สามเสนใน              |
| data.district                | String    | M   | อำเภอ         | พญาไท                 |
| data.province                | String    | M   | จังหวัด        | กรุงเทพ                |
| data.zipcode                 | String    | M   | รหัสไปรษณีย์    | 10400                 |
| data.is_island               | Boolean   | M   | พื้นที่เกาะ      | false                 |
| data.is_remote_area          | Boolean   | M   | พื้นที่ห่างไกล    | false                 |
| data.is_tourist              | Boolean   | M   | พื้นที่ท่องเที่ยว   | false                 |
| data.shipment_provider_names | String[]  | M   | รายชื่อขนส่ง    | ["EMS", "Logic Post"] |
| limit                        | Number    | M   | จำนวนต่อหน้า    | 10                    |
| page                         | Number    | M   | หน้าที่         | 1                     |
| page_count                   | Number    | M   | จำนวนหน้าทั้งหมด | 170                   |

ตัวอย่าง JSON Response

```json
{
  "count": 1,
  "data": [
    {
      "sub_district": "สามเสนใน",
      "district": "พญาไท",
      "province": "กรุงเทพ",
      "zipcode": "10400",
      "is_island": false,
      "is_remote_area": false,
      "is_tourist": false,
      "shipment_provider_names": [
        "EMS",
        "Logic Post"
      ]
    }
  ],
  "limit": 10,
  "page": 1,
  "page_count": 1
}
```

# Webhook

การใช้งาน webhook มีความหมายว่า ด้วยระบบของร้านค้าเองนั้น ต้องเตรียม HTTP Server ในการรองรับ WEBHOOK ผ่านระบบ INTERNET และต้องมี HTTPS ที่มีความน่าเชื่อถือให้เราสามารถยิง HTTP Request กลับไปทาง Server ของร้านค่าได้

## Status ปรับสถานะ

Method: `POST`

รายละเอียด: `ยิง HTTP Request ไปยัง Merchant Server เพื่ออัพเดทสถานะดังกล่าว`

Headers

สามารถแก้ไข Header เองได้ผ่าน Setting ของร้านค้า

| Name         | Value            |
| ------------ | ---------------- |
| Content-Type | application/json |

Request

| Property    | Data Type           | M/O | Description  | Example        |
| ----------- | ------------------- | --- | ------------ | -------------- |
| tracking_no | String              | M   | เลขติดตาม     | PW25075HDPTOL9 |
| ref1        | String              | M   | เลขติดตามขนส่ง | TH2101F4M93K   |
| ref2        | String              | O   |              |                |
| ref3        | String              | O   |              |                |
| status      | OrderShipmentStatus | O   | สถานะ        | In-Transit     |

๋ตัวอย่าง JSON Request

```json
{
  "tracking_no": "PW25075HDPTOL9",
  "ref1": "TH2101F4M93K",
  "ref2": "",
  "ref3": "",
  "status": "In-Transit"
}
```

Response by http status code 2xx is success, 4xx is fails and 500 is error

## Dimension

Method: `POST`

รายละเอียด: `ยิง HTTP Request ไปยัง Merchant Server เพื่ออัพเดทค่า Dimension ต่างๆ`

Headers

สามารถแก้ไข Header เองได้ผ่าน Setting ของร้านค้า

| Name         | Value            |
| ------------ | ---------------- |
| Content-Type | application/json |

Request

| Property    | Data Type | M/O | Description                | Example        |
| ----------- | --------- | --- | -------------------------- | -------------- |
| tracking_no | String    | M   | เลขติดตาม                   | PW25075HDPTOL9 |
| ref1        | String    | M   | เลขติดตามขนส่ง               | TH2101F4M93K   |
| ref2        | String    | O   |                            |                |
| ref3        | String    | O   |                            |                |
| weight      | Number    | M   | Width of the package (cm)  | 567            |
| width       | Number    | M   | Width of the package (cm)  | 23             |
| length      | Number    | M   | Length of the package (cm) | 42             |
| height      | Number    | M   | Height of the package (cm) | 24             |


๋ตัวอย่าง JSON Request

```json
{
  "tracking_no": "PW25075HDPTOL9",
  "ref1": "TH2101F4M93K",
  "ref2": "",
  "ref3": "",
  "weight": 567,
  "width": 23,
  "length": 42,
  "height": 24
}
```

Response by http status code 2xx is success, 4xx is fails and 500 is error

## Price

Method: `POST`

รายละเอียด: `ยิง HTTP Request ไปยัง Merchant Server เพื่ออัพเดทค่า COD หรือ ประกัน`

Headers

สามารถแก้ไข Header เองได้ผ่าน Setting ของร้านค้า

| Name         | Value            |
| ------------ | ---------------- |
| Content-Type | application/json |

Request

| Property    | Data Type | M/O | Description  | Example        |
| ----------- | --------- | --- | ------------ | -------------- |
| tracking_no | String    | M   | เลขติดตาม     | PW25075HDPTOL9 |
| ref1        | String    | M   | เลขติดตามขนส่ง | TH2101F4M93K   |
| ref2        | String    | O   |              |                |
| ref3        | String    | O   |              |                |
| cod         | Number    | M   | COD          | 3500           |
| insurance   | Number    | M   | ประกัน        | 2500           |


๋ตัวอย่าง JSON Request

```json
{
  "tracking_no": "PW25075HDPTOL9",
  "ref1": "TH2101F4M93K",
  "ref2": "",
  "ref3": "",
  "cod": 3500,
  "insurance": 2500
}
```

Response by http status code 2xx is success, 4xx is fails and 500 is error

