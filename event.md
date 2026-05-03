# CloudCom Event Integration

Syncs mobile app user activity logs to the CloudCom platform via the Event Trigger API.

---

## Endpoint

```
POST https://stagingapi.cloudcom.me/api/Event/EventTrigger?eventname={action}
```

The `eventname` query parameter is the action value from the `User Activity Log` record (e.g. `login`, `view`, `filter`).

---

## Base Payload Structure

Every event shares the same envelope. The action-specific fields live inside `AdditionalAttributes`.

```json
{
  "Enterprise_User_Id": "<broker_id>",
  "ApplicationId": 123,
  "CreationDate": "2024-01-01T10:00:00Z",
  "AdditionalAttributes": {
    "key": "value"
  }
}
```

| Field | Source | Description |
|---|---|---|
| `Enterprise_User_Id` | `User Activity Log.broker` | Broker CRM record name |
| `ApplicationId` | `cloudcom_application_id` (site config) | CloudCom application identifier |
| `CreationDate` | `User Activity Log.creation` | ISO 8601 UTC timestamp |
| `AdditionalAttributes` | `User Activity Log.data` + `country` | Action-specific fields |

---

## ID vs Name Fields

For fields like `project_id`, `developer_id`, `property_id`, and `partner_id`, the mobile app can send either the **numeric ID** (as a string) or the **display name** of the record. Both are accepted and stored as-is.

```json
// Using numeric ID
{ "property_id": "1023" }

// Using display name
{ "property_id": "Mivida – Garden Apartment" }
```

---

## Events Reference

### `login`

Triggered when a broker logs into the mobile app.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:30:00Z",
  "AdditionalAttributes": {
    "phone": "01012345678"
  }
}
```

---

### `search`

Triggered when a broker performs a search.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:31:00Z",
  "AdditionalAttributes": {
    "type": "compound"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `type` | string | Search type: `compound` or `separate` |

---

### `filter`

Triggered when a broker applies filters on the property listing.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:32:00Z",
  "AdditionalAttributes": {
    "project_id": "2050",
    "developer_id": "305",
    "type": "apartment",
    "price": "1000000-3000000",
    "location": ["Cairo", "Giza"],
    "category": ["residential"],
    "delivery": ["2025", "2026"],
    "finishing": ["fully finished"],
    "bedrooms": ["2", "3"]
  }
}
```

| Field | Type | Description |
|---|---|---|
| `project_id` | string | Filtered project ID or name |
| `developer_id` | string | Filtered developer ID or name |
| `type` | string | Property type |
| `price` | string | Price range as `"min-max"` |
| `location` | array of strings | Selected locations |
| `category` | array of strings | Property categories |
| `delivery` | array of strings | Expected delivery years |
| `finishing` | array of strings | Finishing types |
| `bedrooms` | array of strings | Bedroom counts |

All fields are optional — only the ones the broker actually used are included.

---

### `view`

Triggered when a broker opens a property detail page.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:33:00Z",
  "AdditionalAttributes": {
    "property_id": "4102"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `property_id` | string | Property ID or name |

---

### `favorite`

Triggered when a broker adds or removes a property from favorites.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:34:00Z",
  "AdditionalAttributes": {
    "property_id": "4102"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `property_id` | string | Property ID or name |

---

### `favorites_history`

Triggered when a broker opens the favorites list screen. No additional attributes.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:35:00Z",
  "AdditionalAttributes": {}
}
```

---

### `select_partner`

Triggered when a broker selects a partner to contact from a property page.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:36:00Z",
  "AdditionalAttributes": {
    "property_id": "4102",
    "partner_id": "2077",
    "contact": "whatsapp"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `property_id` | string | Property ID or name |
| `partner_id` | string | Partner broker ID or name |
| `contact` | string | Contact method: `phone` or `whatsapp` |

---

### `calculator`

Triggered when a broker uses the mortgage/price calculator on a property.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:37:00Z",
  "AdditionalAttributes": {
    "property_id": "4102",
    "partner_id": "2077",
    "value": 5000000
  }
}
```

| Field | Type | Description |
|---|---|---|
| `property_id` | string | Property ID or name |
| `partner_id` | string | Partner broker ID or name |
| `value` | number | Calculated value |

---

### `notification_history`

Triggered when a broker opens the notifications screen.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:38:00Z",
  "AdditionalAttributes": {
    "unread": 12
  }
}
```

---

### `brochure`

Triggered when a broker views or downloads a property brochure.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:39:00Z",
  "AdditionalAttributes": {
    "property_id": "4102"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `property_id` | string | Property ID or name |

---

### `voice_orientation`

Triggered when a broker plays a voice orientation for a property.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:40:00Z",
  "AdditionalAttributes": {
    "property_id": "4102"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `property_id` | string | Property ID or name |

---

### `contests`

Triggered when a broker opens the contests screen.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:41:00Z",
  "AdditionalAttributes": {
    "contest_earn": 500,
    "total_earnings": 1200
  }
}
```

---

### `guidelines`

Triggered when a broker opens the guidelines screen.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:42:00Z",
  "AdditionalAttributes": {
    "lang": "ar"
  }
}
```

| Field | Type | Values |
|---|---|---|
| `lang` | string | `ar`, `en` |

---

### `earnings_history`

Triggered when a broker opens the earnings history screen.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:43:00Z",
  "AdditionalAttributes": {
    "page": "closed_deals"
  }
}
```

| Field | Type | Values |
|---|---|---|
| `page` | string | `closed_deals`, `shared_deals` |

---

### `call_partner`

Triggered when a broker calls a partner directly from the app.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:44:00Z",
  "AdditionalAttributes": {
    "partner_id": "2077",
    "property_id": "4102"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `partner_id` | string | Partner broker ID or name |
| `property_id` | string | Property ID or name |

---

### `whatsapp_partner`

Triggered when a broker contacts a partner via WhatsApp from the app.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:45:00Z",
  "AdditionalAttributes": {
    "partner_id": "2077",
    "property_id": "4102"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `partner_id` | string | Partner broker ID or name |
| `property_id` | string | Property ID or name |

---

### `swap_banners`

Triggered when a broker swipes through banners on a property page.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:46:00Z",
  "AdditionalAttributes": {
    "property_id": "4102"
  }
}
```

| Field | Type | Description |
|---|---|---|
| `property_id` | string | Property ID or name |

---

### `close_form`

Triggered when a broker closes a form, with or without submitting.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:47:00Z",
  "AdditionalAttributes": {
    "submitted": true
  }
}
```

---

### `change_language`

Triggered when a broker switches the app language.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:48:00Z",
  "AdditionalAttributes": {
    "lang": "en"
  }
}
```

| Field | Type | Values |
|---|---|---|
| `lang` | string | `ar`, `en` |

---

### `overseas`

Triggered when a broker opens the overseas section. No additional attributes.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:49:00Z",
  "AdditionalAttributes": {}
}
```

---

### `whatsapp_contact_us`

Triggered when a broker contacts support via WhatsApp.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:50:00Z",
  "AdditionalAttributes": {
    "type": "support"
  }
}
```

| Field | Type | Values |
|---|---|---|
| `type` | string | `support`, `CIL`, `contract`, `closing`, `coverage` |

---

### `settings_contact_us`

Triggered when a broker taps a contact option from the settings screen.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:51:00Z",
  "AdditionalAttributes": {
    "type": "WhatsApp"
  }
}
```

| Field | Type | Values |
|---|---|---|
| `type` | string | `contract number`, `Instagram`, `Facebook`, `WhatsApp`, `website` |

---

### `meeting_button`

Triggered when a broker taps the meeting button.

```json
{
  "Enterprise_User_Id": "1001",
  "ApplicationId": 123,
  "CreationDate": "2024-06-01T08:52:00Z",
  "AdditionalAttributes": {
    "available": true
  }
}
```

| Field | Type | Description |
|---|---|---|
| `available` | boolean | Whether a meeting slot was available |

---

## Site Config Required

The following keys must be present in `site_config.json`:

```json
{
  "cloudcom_base_url": "https://stagingapi.cloudcom.me",
  "cloudcom_cred": "<base64 application key>",
  "cloudcom_application_id": 123,
  "cloudcom_auth_key": "<auth key>"
}
```
