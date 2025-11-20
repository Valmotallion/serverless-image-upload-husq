# Serverless Image Upload & Retrieval Backend

## 📍 Overview
This project is a serverless backend service that enables:
- Uploading image files
- Retrieving them using a unique generated identifier/link
- Deleting uploaded images along with associated metadata

The implementation uses a single serverless function with internal routing.  
No frontend is required — testing can be done using cURL or Postman.

---

## 🚀 Core Requirements
Implement **three API operations** within a single Lambda function:
| Method | Endpoint | Description |
|--------|-----------|-------------|
| POST | `/upload` | Accept an image, store it, return unique ID & access link |
| GET | `/image/{id}` | Retrieve stored image by ID |
| DELETE | `/image/{id}` | Delete image and metadata |

---

## 🏗 Architecture
AWS-based implementation:
- **AWS Lambda** — single handler implementing routing logic
- **AWS S3** — stores uploaded images
- **AWS DynamoDB** — stores metadata mapping `imageId -> s3Key`
- **AWS API Gateway** — exposes HTTP endpoint with rate limiting
- **IaC**: AWS SAM

---

## 🧰 Tech Stack
| Component | Technology |
|-----------|-----------|
| Language | **TypeScript** |
| Infrastructure as Code | **AWS SAM** |
| Unit Testing | **Jest** |
| Storage | **S3 + DynamoDB** |
| Routing | Implemented inside Lambda function |

---

## 📁 Project Structure
```
src/
├── handlers/
│   └── lambda.ts          # Main Lambda entry with routing
├── services/
│   ├── s3Service.ts       # Upload / retrieve / delete S3 logic
│   ├── dbService.ts       # DynamoDB get/put/delete operations
│   └── router.ts          # Route request to appropriate handler
├── utils/
│   └── response.ts        # Unified HTTP responses
└── types/
    └── index.ts           # TypeScript type definitions
tests/
├── upload.test.ts         # Upload functionality tests
├── retrieve.test.ts       # Retrieve functionality tests
├── delete.test.ts         # Delete functionality tests
├── router.test.ts         # Router tests
├── s3Service.test.ts      # S3 service tests
└── dbService.test.ts      # DynamoDB service tests
template.yaml              # AWS SAM template
package.json               # Node.js dependencies
tsconfig.json              # TypeScript configuration
jest.config.js             # Jest testing configuration
README.md                  # This file
```

---

## 🛠 Setup Instructions

### Prerequisites
- Node.js 18.x or later
- AWS CLI configured with appropriate permissions
- AWS SAM CLI installed
- Docker (for local testing)

### Installation
1. **Clone and setup the project:**
   ```bash
   cd BAH
   npm install
   ```

2. **Build the TypeScript code:**
   ```bash
   npm run build
   ```

3. **Run unit tests:**
   ```bash
   npm test
   # or with coverage
   npm run test:coverage
   ```

### Local Development
1. **Start the local API:**
   ```bash
   sam build
   sam local start-api
   ```
   
   The API will be available at `http://localhost:3000`

### Deployment
1. **Deploy to AWS:**
   ```bash
   sam build
   sam deploy --guided
   ```
   
   Follow the prompts to configure your deployment.


---

## 🗄 DynamoDB Table Schema
| Field | Type | Description |
|--------|------|-------------|
| `imageId` (PK) | string | Unique identifier for stored image |
| `s3Key` | string | Object key in S3 bucket |
| `createdAt` | string | Timestamp |

---

## 🪣 S3 Storage Layout


images/<uuid>.png


---

## 🧪 Example cURL Commands

### **Upload**
```bash
curl -X POST \
  -H "Content-Type: image/png" \
  --data-binary "@sample.png" \
  http://localhost:3000/upload
```

**Response:**
```json
{
  "imageId": "550e8400-e29b-41d4-a716-446655440000",
  "accessLink": "http://localhost:3000/image/550e8400-e29b-41d4-a716-446655440000",
  "message": "Image uploaded successfully"
}
```

### **Retrieve**
```bash
curl -X GET \
  http://localhost:3000/image/550e8400-e29b-41d4-a716-446655440000 \
  --output downloaded.png
```

### **Delete**
```bash
curl -X DELETE http://localhost:3000/image/550e8400-e29b-41d4-a716-446655440000
```

**Response:**
```json
{
  "message": "Image deleted successfully"
}
```

### **Production URLs**
After deployment to AWS, replace `localhost:3000` with your API Gateway URL:
```bash
# Upload
curl -X POST \
  -H "Content-Type: image/png" \
  --data-binary "@sample.png" \
  https://<api-gateway-id>.execute-api.<region>.amazonaws.com/dev/upload

# Retrieve
curl -X GET \
  https://<api-gateway-id>.execute-api.<region>.amazonaws.com/dev/image/<imageId> \
  --output downloaded.png

# Delete
curl -X DELETE https://<api-gateway-id>.execute-api.<region>.amazonaws.com/dev/image/<imageId>
```

---

## 🧪 Unit Testing Requirements

✅ **Uses Jest** with TypeScript support  
✅ **Mock AWS SDK** using `aws-sdk-client-mock`  
✅ **Tests include:**
- Successful upload with various image types
- Image not found cases for retrieve and delete
- Successful delete operations
- Route handling and parameter extraction
- Service layer error handling
- Input validation

**Target coverage: > 80%**

---

## 📦 Deliverables

✅ **Complete source code** in TypeScript  
✅ **IaC template** using AWS SAM  
✅ **Unit tests** with comprehensive coverage  
✅ **README** with setup instructions & cURL examples  
✅ **Works locally** using `sam local start-api`

---

## 💡 Notes

- No authentication required
- No UI / frontend required  
- Focus on clean, production-grade code and structure
- Images are automatically deleted after 30 days (TTL)
- Maximum image size: 10MB
- Supported formats: PNG, JPEG, GIF, WebP, BMP
- CORS enabled for cross-origin requests
