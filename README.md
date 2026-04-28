# CloudCom Customer Payload Schema

Sent via `POST /CloudComWebhook/Customer?auth=<auth_key>`

## Fields

| Field | Type | Source / Logic |
|---|---|---|
| `Enterprise_User_Id` | `string` | `broker.name` |
| `createdAt` | `string` (ISO 8601 + "Z") | `broker.creation` — e.g. `"2024-01-15T10:30:00Z"` |
| `name` | `string` | `broker.full_name` |
| `phone` | `string` | Concatenation of `dialing_code` (stripped of `+`) + `phone_number` |
| `email` | `string` | `broker.email` |
| `city` | `string \| null` | English name from `City`; `null` if unset |
| `age` | `number \| null` | `broker.age`; `null` if blank |
| `gender` | `string \| null` | `broker.gender`; `null` if blank |
| `campaignStatus` | `boolean` |  |
| `preferredAreas` | `string \| null` | English name of `Area` ; `null` if not found |
| `userType` | `"Sales Developer" \| "Broker" \| null` | |
| `userCondition` | `"Active" \| "Inactive" \| "Super Active" \| null` | |
| `userStatus` | `"Closed" \| "Open"` | `"Closed"` if broker has a `Deal` with status `Pending` or `Accepted`; otherwise `"Open"` |
| `earningStatus` | `"Paid" \| "Pending"` | `"Paid"` if `broker.total_earned > 0`; otherwise `"Pending"` |
| `preferredContactMethod` | `"SMS" \| "Notification"` | `"SMS"` if `broker.enable_sms` is truthy; otherwise `"Notification"` |
| `experience` | `"Fresh" \| "Senior" \| null` | |
| `applicationId` | `string` | CloudCom client `application_id` (from config) |

## Example Payload

```json
{
  "Enterprise_User_Id": "34512",
  "createdAt": "2024-01-15T10:30:00Z",
  "name": "Ahmed Al-Rashid",
  "phone": "971501234567",
  "email": "ahmed@example.com",
  "city": "Dubai",
  "age": 34,
  "gender": "Male",
  "campaignStatus": true,
  "preferredAreas": "Downtown Dubai",
  "userType": "Broker",
  "userCondition": "Active",
  "userStatus": "Open",
  "earningStatus": "Paid",
  "preferredContactMethod": "Notification",
  "experience": "Senior",
  "applicationId": "app_abc123"
}
```
