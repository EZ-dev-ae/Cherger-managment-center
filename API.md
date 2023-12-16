# API Docs


#### Index

- [Create new Charge Point](#create-new-charge-point) (Zoho -> Charging Station)

- [Create new Customer](#create-new-customer) (Zoho -> Charging Station)

- [Block Customer](#block-customer) (Zoho -> Charging Station)

- [Unblock Customer](#unblock-customer) (Zoho -> Charging Station)

- [Charge Point Boot Notification](#charge-point-boot-notification) (Charging Station -> Zoho)


### Base URL

```
https://proxymb.tk/api/v1
```

## Create new Charge Point

POST: `/webhooks/generate_charge_point`

### Request Body

No request body is required.

### Response

#### Success

Status Code: `200 OK`
Body:

```json
{
  "charging_point_id": "fccp_17a18afd23e722dd021bd75b581e7651"
}
```

#### Failures

All failures will return a `500 Internal Server Error`.

---

## Create new Customer

POST: `/webhooks/register_customer`

### Request Body

```json
{
  "charging_point_id": "fccp_17a18afd23e722dd021bd75b581e7651",
  "application": "store",
  "zoho_customer_id": "445"
}
```

- `charging_point_id`: **REQUIRED** _String_ The is the charging point unique identification number.

- `application`: **REQUIRED** _String_

- `zoho_customer_id`: **REQUIRED** _String_ The is the customer unique identification number in Zoho.

### Response

#### Success

Status Code: `200 OK`
Body:

```json
{
  "id": 2,
  "created_at": "2023-12-15T22:21:54.570414+02:00",
  "updated_at": "2023-12-15T22:21:54.570414+02:00",
  "zoho_customer_id": "897",
  "application": "store",
  "id_tag": null,
  "max_active_transactions_count": 0
}
```

> By default customer will be blocked, to unblock the customer use the [Unblock Customer](#unblock-customer) endpoint.

#### Failures

| Status | Description                                                      | Response |
|--------|------------------------------------------------------------------|----------|
| 422    | Validation error due to invalid input request body               | ```{"errors": ["Key: 'P.Application' Error:Field validation for 'Application' failed on the 'required' tag"],"status": 422}```  |
| 404    | The requested resource was not found                             | ```{"details":null "error":"charge point with id: cp_26 not found","status":404}``` |
| 409    | A conflict may be from the charging point having already a user. | ```{"details":null,"error":"charge point with id: fccp_dfc40ad4bb8b77f543417ba257b66282 already has a user","status":409}``` |

---

## Block Customer

POST: `/webhooks/block_customer`

### Request Body

```json
{
  "zoho_customer_id": "445"
}
```

- `zoho_customer_id`: **REQUIRED** _String_ The is the customer unique identification number in Zoho.

### Response

#### Success

Status Code: `204 No Content`

#### Failures

| Status | Description                                                      | Response |
|--------|------------------------------------------------------------------|----------|
| 422    | Validation error due to invalid input request body.               |          |
| 404    | The requested resource was not found or invalid                 | ```{"details":null,"error":"user: 45 not found","status":404}``` |
| 401    | Blocking the user failed due to an internal error. |  |

---

## Unblock Customer

POST: `/webhooks/unblock_customer`

### Request Body

```json
{
  "zoho_customer_id": "445"
}
```

- `zoho_customer_id`: **REQUIRED** _String_ The is the customer unique identification number in Zoho.

- `max_active_transaction_count`: **OPTIONAL** _INTEGER_ Set to 0 to block this tag. Set to a negative value to disable concurrent transaction checks (i.e. every transaction will be allowed). Set to a positive value to control the number of active transactions that is allowed.

### Response

#### Success

Status Code: `204 No Content`

#### Failures

| Status | Description                                                      | Response |
|--------|------------------------------------------------------------------|----------|
| 422    | Validation error due to invalid input request body.               |          |
| 404    | The requested resource was not found or invalid                 | ```{"details":null,"error":"user with id: 1 not found or tag id: tg_875 is not correct","status":404}``` |
| 401    | Blocking the user failed due to an internal error. |  |

---

## Charge Point Boot Notification

We will send the all charge point information to Zoho as soon as the charge point is connected to the server.

### Expected Sent Body in a JSON format

```json
{
  "charging_point_id": "fccp_17a18afd23e722dd021bd75b581e7651",
  "ocpp_protocol": "ocpp1.6J",
  "vendor": "avt",
  "model": "avt_plus",
  "serial_number": "cp_sn_8789789",
  "fw_version": "",
  "fw_update_status": null,
  "fw_update_timestamp": null,
  "iccid": "iccid_78712124564",
  "imsi": "",
  "diagnostics_status": null,
  "last_heartbeat_timestamp": "2023-12-15T22:15:58Z",
  "description": "",
  "meter_serial_number": "sn_223138",
  "meter_type": "type_44212654"
}
```

All fields, except `charging_point_id`, are _*optional*_ and _*nullable*_.
