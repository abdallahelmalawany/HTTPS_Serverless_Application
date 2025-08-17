# HTTPS_Serverless_Application
# 🎟️ Raffle Application (Serverless on AWS)


## 📂 Project Structure

```
raffle-app/
│
├── frontend/
│   ├── apply.html      # User registration form
│   ├── draw.html       # Admin page to count participants & draw winners
│
├── backend/
│   ├── apply.js        # Lambda: register participant
│   ├── count.js        # Lambda: count participants
│   ├── draw.js         # Lambda: pick random winners
│
├── docs/
│   └── architecture.png # AWS Architecture diagram
│
└── README.md           # Documentation
```

---

## 🖥️ Frontend

### `apply.html`

* Collects `name`, `phone`, and `email`
* Sends `POST /raffle/apply`

### `draw.html`

* Displays total participants
* Runs a draw via `GET /raffle/draw`
* Shows list of winners

---

## 🧩 Backend (Lambdas)

### 1. Apply Lambda (`apply.js`)

Registers a participant in DynamoDB.

```js
const command = new PutCommand({
  TableName: "devops90_raffle",
  Item: { email, phone, name, won: "no" },
});
```

### 2. Count Lambda (`count.js`)

Counts total participants.

```js
const command = new ScanCommand({
  TableName: "devops90_raffle",
  Select: "COUNT"
});
```

### 3. Draw Lambda (`draw.js`)

Picks 3 random winners and updates their `won` status.

```js
const update_command = new UpdateCommand({
  TableName: "devops90_raffle",
  Key: { email: winner.email },
  UpdateExpression: 'set won = :r',
  ExpressionAttributeValues: { ':r': 'yes' }
});
```

---

## 🔗 API Endpoints

| Method | Endpoint        | Description                  |
| ------ | --------------- | ---------------------------- |
| POST   | `/raffle/apply` | Register participant         |
| GET    | `/raffle/count` | Get total participants count |
| GET    | `/raffle/draw`  | Draw 3 random winners        |

### Example Request: `POST /raffle/apply`

```json
{
  "name": "John Doe",
  "phone": "0112345678",
  "email": "john@example.com"
}
```

### Example Response

```
Thanks, Your data may have been received;)
```

### Example Response: `GET /raffle/draw`

```json
{
  "winners": [
    { "name": "John Doe", "email": "john@example.com", "phone": "0112345678" },
    { "name": "Jane Smith", "email": "jane@example.com", "phone": "0109876543" },
    { "name": "Ali Hassan", "email": "ali@example.com", "phone": "0123456789" }
  ]
}
```

---

## 📊 DynamoDB Table

* **Table Name:** `devops90_raffle`
* **Primary Key:** `email`
* **Attributes:**

  * `name` (String)
  * `phone` (String)
  * `email` (String, PK)
  * `won` (String: `yes` | `no`)

---

## ⚡ Deployment

### 1. DynamoDB

```sh
aws dynamodb create-table \
  --table-name devops90_raffle \
  --attribute-definitions AttributeName=email,AttributeType=S \
  --key-schema AttributeName=email,KeyType=HASH \
  --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5
```

### 2. Deploy Lambdas

* Package each Lambda (`apply.js`, `count.js`, `draw.js`)
* Deploy via AWS Console, SAM, or Serverless Framework
* Attach each to API Gateway endpoints

### 3. Frontend

* Upload `apply.html` and `draw.html` to S3 bucket
* Enable **static website hosting**
* Distribute via **CloudFront**

### 4. Domain & SSL

* Configure **Route 53** for custom domain
* Request certificate with **ACM** for HTTPS

### 5. Monitoring

* Use **CloudWatch Logs** to track Lambda executions
* Add metrics/alarms for DynamoDB & API Gateway

---

## ✅ Summary

This raffle application demonstrates a **complete serverless solution** on AWS with:

* Static frontend (S3 + CloudFront)
* API Gateway + Lambda backend
* DynamoDB persistence
* Secure domain (Route 53 + ACM)
* Logging & monitoring (CloudWatch)

🎉 Users can join raffles, and admins can easily run draws to select winners!

<img width="1920" height="795" alt="Screenshot 2025-08-17 at 23-06-17 Request certificate Certificate Manager us-east-1" src="https://github.com/user-attachments/assets/5b8c4859-7996-4ebd-91da-f1207acf6e30" />
<img width="1910" height="788" alt="Screenshot 2025-08-17 at 23-07-34 API Gateway - Custom domain names" src="https://github.com/user-attachments/assets/eaf78949-541c-4809-b732-effcec41356c" />
<img width="1892" height="755" alt="Screenshot 2025-08-17 at 23-12-28 API Gateway - Create API" src="https://github.com/user-attachments/assets/de34a8bf-85ce-40e7-81fc-71a07983c83e" />
<img width="1920" height="795" alt="Screenshot 2025-08-17 at 23-13-14 API Gateway - Create HTTP API Step 1" src="https://github.com/user-attachments/assets/75134442-0ad7-4541-9b40-264e8856fede" />
<img width="1904" height="801" alt="Screenshot 2025-08-17 at 23-13-55 API Gateway - Routes" src="https://github.com/user-attachments/assets/7b3fdbf4-9d73-4c7b-b7f7-eacfbe7de260" />
<img width="1919" height="786" alt="Screenshot 2025-08-17 at 23-17-42 API Gateway - Integrations" src="https://github.com/user-attachments/assets/a2139829-f71a-4424-9d7a-bbf64cae5ff0" />
<img width="1919" height="2488" alt="Screenshot 2025-08-17 at 23-22-46 Create S3 bucket S3 us-east-1" src="https://github.com/user-attachments/assets/c8030423-7539-4c4a-b7a1-8018de223fe3" />
<img width="1914" height="794" alt="Screenshot 2025-08-17 at 23-33-44 raffile com - S3 bucket S3 us-east-1" src="https://github.com/user-attachments/assets/f9fa6f9a-689f-4ffd-8acf-76c554e569c4" />
<img width="1916" height="777" alt="Screenshot 2025-08-17 at 23-46-38 Distributions CloudFront Global" src="https://github.com/user-attachments/assets/2d1fd062-372a-4a1f-a92a-58b53c3c877d" />
<img width="1907" height="793" alt="Screenshot 2025-08-17 at 23-55-35 CloudWatch us-east-1" src="https://github.com/user-attachments/assets/1c1441ca-2a04-4b33-b266-83e2227140e7" />
<img width="1918" height="713" alt="Screenshot 2025-08-17 at 22-19-31 Create table Amazon DynamoDB Management Console DynamoDB us-east-1" src="https://github.com/user-attachments/assets/d6c74afb-10ae-4175-a456-a63fc4882980" />
<img width="1892" height="1132" alt="Screenshot 2025-08-17 at 22-19-42 Create table Amazon DynamoDB Management Console DynamoDB us-east-1" src="https://github.com/user-attachments/assets/15d2efaa-9def-468f-887f-ce70ca9f4c7d" />
<img width="1008" height="1760" alt="Screenshot 2025-08-17 at 22-20-25 Create table Amazon DynamoDB Management Console DynamoDB us-east-1" src="https://github.com/user-attachments/assets/86c9e4fc-6927-428c-a07d-c265b283efef" />
<img width="1920" height="795" alt="Screenshot 2025-08-17 at 22-29-31 List tables Amazon DynamoDB Management Console DynamoDB us-east-1" src="https://github.com/user-attachments/assets/4451b7b9-8a9c-49f9-a2a3-b036d94cb3cd" />
<img width="1920" height="795" alt="Screenshot 2025-08-17 at 22-31-17 Roles IAM Global" src="https://github.com/user-attachments/assets/9d4f7e6b-00f1-4ac0-a1e3-7e509e31f08f" />
<img width="1920" height="795" alt="Screenshot 2025-08-17 at 22-31-29 Create role IAM Global" src="https://github.com/user-attachments/assets/a0d6da32-9772-44ea-b6bd-da10ef342995" />
<img width="1920" height="795" alt="Screenshot 2025-08-17 at 22-31-38 Create role IAM Global" src="https://github.com/user-attachments/assets/cec3cb81-6247-4ba0-ae42-3e5bb3d4a1af" />
<img width="1900" height="1480" alt="Screenshot 2025-08-17 at 22-33-46 Create role IAM Global" src="https://github.com/user-attachments/assets/27e75c9e-715d-440f-a87c-20e14a30f425" />
<img width="1904" height="879" alt="Screenshot 2025-08-17 at 22-35-25 Create function Functions Lambda" src="https://github.com/user-attachments/assets/0b9088fc-da83-4110-b300-fbf28545aecf" />
<img width="1919" height="1842" alt="Screenshot 2025-08-17 at 22-37-00 apply_raffile Functions Lambda" src="https://github.com/user-attachments/assets/23d8a6e0-6da4-4a10-a115-f71780e23e8a" />
<img width="1919" height="1172" alt="Screenshot 2025-08-17 at 22-38-48 Create function Functions Lambda" src="https://github.com/user-attachments/assets/5fea7247-0759-4d9c-9455-4b1852d533d0" />
<img width="1916" height="1854" alt="Screenshot 2025-08-17 at 22-40-00 count_raffile Functions Lambda" src="https://github.com/user-attachments/assets/2168cbbb-d7d9-453c-8502-13bc5c487f30" />
<img width="1913" height="1072" alt="Screenshot 2025-08-17 at 22-40-40 Create function Functions Lambda" src="https://github.com/user-attachments/assets/df4ed4d2-c953-4e85-af5e-4cf68e51f915" />
<img width="1915" height="1853" alt="Screenshot 2025-08-17 at 22-41-37 draw_raffile Functions Lambda" src="https://github.com/user-attachments/assets/4a790240-56d8-4049-9911-06349f718aef" />
<img width="1920" height="795" alt="Screenshot 2025-08-17 at 22-42-40 Route 53 - get started" src="https://github.com/user-attachments/assets/7dd184d2-a3e5-43c7-aa2f-e3a6c7134787" />
<img width="1910" height="554" alt="Screenshot 2025-08-17 at 22-44-19 Route 53 Global" src="https://github.com/user-attachments/assets/adc71883-c15a-44ad-bc71-5b52f47703a8" />
<img width="1920" height="1080" alt="Screenshot (18)" src="https://github.com/user-attachments/assets/95d62e00-ad75-416b-91bf-d1fcdbc7151f" />
<img width="1920" height="1080" alt="Screenshot (17)" src="https://github.com/user-attachments/assets/5635b6f9-50d0-4ef5-8bc3-dce4218b771e" />

