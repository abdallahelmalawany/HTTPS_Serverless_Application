# HTTPS_Serverless_Application
# 🎟️ Raffle Application (Serverless on AWS)
```mermaid
flowchart TD

  User[👤 User] -->|HTTP Request| APIGW[🌐 API Gateway]

  APIGW -->|POST /register| LambdaRegister[🟢 Lambda: Register]
  APIGW -->|GET /count| LambdaCount[🟠 Lambda: Count]
  APIGW -->|POST /winners| LambdaWinners[🔵 Lambda: Select Winners]

  LambdaRegister -->|PutItem| DynamoDB[(🗄️ DynamoDB: devops90_raffle)]
  LambdaCount -->|Scan COUNT| DynamoDB
  LambdaWinners -->|Query + Update| DynamoDB

This is a **serverless raffle application** built with **AWS services**. Users can apply to join a raffle, and an admin can run a draw to randomly select winners. The app is fully serverless, scalable, and cost-efficient.

---

## 🚀 Architecture

![Architecture Diagram](docs/architecture.png)

**AWS Services Used:**

* **Amazon S3** → Host static frontend (`apply.html`, `draw.html`)
* **Amazon CloudFront** → CDN for global content delivery
* **Amazon Route 53 + ACM** → Custom domain & HTTPS
* **Amazon API Gateway** → REST API endpoints (`/apply`, `/count`, `/draw`)
* **AWS Lambda** → Backend logic (3 functions: `apply`, `count`, `draw`)
* **Amazon DynamoDB** → Store raffle participants & winners
* **AWS CloudWatch** → Logs & monitoring
* **AWS IAM** → Permissions & security

---

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
