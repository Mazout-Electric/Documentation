# Mazout Electric IoT API Documentation

**Base URL:** `https://api.mazoutelectric.com`\n
**API Version:** v1.0

---

## 📘 Table of Contents

1. [Overview](#overview)
2. [Authentication](#authentication)
3. [Error Handling](#error-handling)
4. [Endpoints](#endpoints)

   * [Authentication](#authentication-endpoint)
   * [Device Operations](#device-endpoints)
   * [Administration](#administration-endpoints)
5. [Data Models](#data-models)
6. [Examples](#examples)
7. [Changelog](#changelog)

---

## 🧭 Overview

The MazouElectric API provides a secure and standardized interface for clients to interact with IoT hardware. It supports:

* Secure authentication using Bearer tokens
* Telemetry data access
* Remote device control
* Firmware management (OTA)
* Administrative actions like user and vehicle listings

---

## 🔐 Authentication

All endpoints require a Bearer token. You must first obtain a token via the authentication endpoint.

### 🔑 Authentication Endpoint

```bash
curl https://api.mazoutelectric.com/api/auth/token \
  --header 'Content-Type: application/json' \
  --data '{
    "username": "<username>",
    "password": "<password>"
  }'
```

#### ✅ Successful Response

```json
{
  "status": "SUCCESS|FAILURE",
  "data": {
    "token": "<token>",
    "username": "<username>"
  },
  "err": "<err>",
  "msg": "<msg>"
}
```

Use the token in subsequent requests:

```
Authorization: Bearer <token>
```

---

## 🚨 Error Handling

All error responses follow [RFC 7807 Problem Details](https://tools.ietf.org/html/rfc7807):

```json
{
  "type": "https://api.mazoutelectric.com/errors/invalid-request",
  "title": "Invalid Request",
  "status": 400,
  "detail": "'vehicleId' must be a valid UUID",
  "instance": "/v1/devices/<deviceID>/commands"
}
```

---

## 🔧 Endpoints

### 🔑 Authentication Endpoint

| Method | Endpoint          | Description        |
| ------ | ----------------- | ------------------ |
| POST   | `/api/auth/token` | Get a Bearer token |

---

### 📟 Device Endpoints

Endpoints related to telemetry, control, and firmware upgrades.

| Method | Endpoint                                                       | Description                    |
| ------ | -------------------------------------------------------------- | ------------------------------ |
| GET    | `/v1/devices/{deviceID}/telemetry`                             | Get latest telemetry           |
| GET    | `/v1/devices/{deviceID}/telemetry?from={ISO8601}&to={ISO8601}` | Get telemetry for a time range |
| POST   | `/v1/devices/{deviceID}/commands`                              | Send command(s) to device      |
| POST   | `/v1/devices/{deviceID}/firmware`                              | Initiate OTA firmware upgrade  |
| GET    | `/v1/devices/{deviceID}/firmware/status?upgradeID={upgradeID}` | Get firmware upgrade status    |

#### 📤 Command Payload

```json
{
  "commands": [
    { "type": "immobilize", "value": <boolean> },
    { "type": "rpmPreset", "value": <integer> }
  ]
}
```

#### 🔄 Firmware Upgrade Request

```json
{
  "version": "<semver>",
  "url": "<firmwareBinaryURL>",
  "checksum": "<sha256>"
}
```

---

### 🛠️ Administration Endpoints

Endpoints to manage users, vehicles, and metrics.

| Method | Endpoint                               | Description                              |
| ------ | -------------------------------------- | ---------------------------------------- |
| GET    | `/api/vehicles`                        | List all vehicles                        |
| GET    | `/api/users`                           | List all users                           |
| POST   | `/api/vehicles/distance/bulk`          | Calculate distance for multiple vehicles |
| GET    | `/api/vehicles/{vehicleID}/gps/latest` | Get latest GPS data for a vehicle        |

#### 🧮 Bulk Distance Request

```json
{
  "vehicleIds": ["<vehicleID1>", "<vehicleID2>", ...],
  "from": "<ISO8601>",
  "to": "<ISO8601>"
}
```

#### ✅ Bulk Distance Response

```json
{
  "results": [
    { "vehicleID": "<vehicleID>", "distanceMeters": <integer> },
    ...
  ]
}
```

---

## 🧱 Data Models

```json
// Telemetry Record
{
  "rpm": "integer",
  "voltage": "float",
  "immobilized": "boolean",
  "timestamp": "string (ISO 8601)"
}

// Command Batch
{
  "commands": [
    { "type": "string", "value": "boolean|integer|float|string" }
  ]
}

// Bulk Distance Request
{
  "vehicleIds": ["string"],
  "from": "string (ISO 8601)",
  "to": "string (ISO 8601)"
}
```

---

## 🧪 Examples

### 🔐 Authenticate

```bash
curl https://api.mazoutelectric.com/api/auth/token \
  -H 'Content-Type: application/json' \
  -d '{"username":"<username>","password":"<password>"}'
```

### 🚗 List Vehicles

```bash
curl https://api.mazoutelectric.com/api/vehicles \
  -H 'Authorization: Bearer <token>'
```

### 📍 Get Latest GPS

```bash
curl https://api.mazoutelectric.com/api/vehicles/<vehicleID>/gps/latest \
  -H 'Authorization: Bearer <token>'
```

### 📏 Compute Bulk Distances

```bash
curl -X POST https://api.mazoutelectric.com/api/vehicles/distance/bulk \
  -H 'Authorization: Bearer <token>' \
  -H 'Content-Type: application/json' \
  -d '{"vehicleIds": ["<id1>", "<id2>"], "from": "<ISO8601>", "to": "<ISO8601>"}'
```

---

## 📝 Changelog

* **v1.0** – Initial release: authentication, telemetry, command control, firmware upgrades, and administrative endpoints.
