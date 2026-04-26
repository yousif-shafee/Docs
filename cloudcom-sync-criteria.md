# Cloudcom User Sync — Field Criteria

This document describes the business logic used when syncing a broker's profile from the Byit CRM to the Cloudcom platform. It is intended for non-technical readers such as operations, product, and CRM teams.

---

## Fields Synced Directly

These fields are fetched from their source and sent to Cloudcom without transformation. If a field has no value at the time of sync, it is left blank.

| Cloudcom Field | Source
|---|---|
| Name | The broker's full name
| Phone Number | The broker's registered phone number
| Email | The broker's registered email address
| City | The English name of the city linked to the broker's profile
| Campaign Status | Whether the broker appears as a member in any active campaign — **Subscribed** if found, **Not Subscribed** if not

The following fields are also synced directly from their source, but may be blank at the time of sync depending on data availability or whether the field has been filled in yet.

| Cloudcom Field | Source | When it may be blank |
|---|---|---|
| Age | A dedicated Age field on the broker's CRM profile | New field — blank for brokers registered before it was introduced until manually updated |
| Gender | A dedicated Gender field on the broker's CRM profile | New field — blank for brokers registered before it was introduced; the broker's name can be reviewed manually or via an AI model as a fallback to infer gender |
| Preferred Areas | The English name of the Primary Area on the broker's A-Team record | Blank if the broker has no team assignment or the team has no primary area set |

---

## Fields with Business Logic

The following fields cannot be copied directly and are derived based on rules.

---

### User Type

**Possible values:** Broker, Sales Developer

**How it is determined:**

We look at the broker's three call status fields (Call 1, Call 2, Call 3).

- If **any** of the three statuses is **"Developer"**, the user type is set to **Sales Developer**.
- If **any** of the three statuses is one of the following, the user type is set to **Broker**:
  - Active
  - Super Active
  - Inactive
  - Fresh
  - Closed
  - International
  - Pre-Fresh
  - Owner
- If all three statuses are exclusively ambiguous values — such as Ahya, No Response, Support Team, Client, Wrong Number, or Delete — the user type **cannot be determined** and the field is left blank.

> **Priority note:** If a broker has "Developer" in one status and a broker-category value in another, they are classified as **Sales Developer** (Developer takes priority).

---

### User Condition

**Possible values:** Active, Inactive, Super Active

**How it is determined:**

We scan the three call statuses in this order: **Call 3 → Call 2 → Call 1**.

The first status found that is either Active, Inactive, or Super Active becomes the user condition. If none of the three statuses match any of these values, the field is left blank.

> **Rationale:** Call 3 is the most recent call, so it reflects the broker's current state most accurately.

---

### User Status

**Possible values:** Closed, Open

**How it is determined:**

We check whether the broker has any deal in the system with a status of **Pending** or **Accepted**.

- If at least one such deal exists → **Closed**
- If no such deal exists → **Open**

> This reflects whether the broker is currently engaged with an active deal, regardless of the deal's outcome.

---

### Earning Status

**Possible values:** Pending, Paid

**How it is determined:**

We look at the broker's total earnings amount.

- If total earnings are **greater than zero** → **Paid**
- If total earnings are **zero** → **Pending**

> Important: A broker is considered "Paid" even if they have not yet collected the full amount (i.e., they have a remaining balance). The classification is based on whether *any* earnings have been recorded, not whether the full balance has been settled.

---

### Preferred Contact Method

**Possible values:** SMS, Notification

**How it is determined:**

We check the broker's SMS preference setting.

- If **SMS is enabled** → **SMS**
- If SMS is not enabled → **Notification**

> Note: WhatsApp is listed as an option on the Cloudcom side but is not supported in the Byit app, so it will never be assigned.

---

### Experience

**Possible values:** Fresh, Senior

**How it is determined — two steps:**

**Step 1:** Check the broker's latest status.
- If the latest status is contain **"Fresh"** → set experience to **Fresh** and stop.

**Step 2:** If the latest status is not contain "Fresh", parse the broker's years of experience field. This field can contain free-text values like `"12"`, `"1 Year"`, `"6 months"`, etc.

| Value contains / equals | Result |
|---|---|
| The word "month" (e.g. "6 months", "8 Months") | Fresh |
| A number of 0 or 1 (e.g. "0", "1", "1 Year") | Fresh |
| A number of 2 or more (e.g. "2", "5", "12 years") | Senior |
| Empty or unreadable value | Left blank |

---

### Age

**Possible values:** Any numeric age value

**How it is determined:**

A new Age field will be added to the broker's profile in the CRM. This field will be filled in manually for newly registered brokers going forward. When syncing, we check whether the age has been entered:

- If the age is filled → it is sent to Cloudcom as-is
- If the age is not filled → the field is left blank

> Brokers registered before this field was introduced will not have an age value until it is manually updated.

---

### Gender

**Possible values:** Male, Female

**How it is determined:**

A new Gender field will be added to the broker's profile in the CRM. This field will be filled in manually for newly registered brokers going forward. When syncing, we check in this order:

1. If the gender field is filled → use it directly
2. If the gender field is empty → the broker's name can be reviewed — either manually by the operations team or through an AI model — to infer gender from the name
3. If neither option yields a result → the field is left blank

> The name-based inference is an optional fallback and is not guaranteed to be accurate for all names.

---

### Preferred Areas

**Possible values:** Any area name (e.g. East, West, Coastal)

**How it is determined:**

We look up the broker's team assignment (A-Team record) and check the **Primary Area** field on that record. If a primary area is found, its English name is sent to Cloudcom.

- If the broker has a team with a primary area assigned → the area's English name is used
- If the broker has no team assignment, or the team has no primary area set → the field is left blank

---

### Campaign Status

**Possible values:** Subscribed, Not Subscribed

**How it is determined:**

We check the campaign membership records to see whether the broker is currently subscribed to any campaign.

- If the broker appears as a member in at least one active campaign → **Subscribed**
- If no campaign membership is found → **Not Subscribed**

---

## Fields Not Implemented

The following fields from the Cloudcom requirements could not be implemented because the data is not available in the Byit system.

| Cloudcom Field | Reason Not Implemented |
|---|---|
| Device Location | Not tracked (Brokerage / Developer / Compound distinction is not stored) |
