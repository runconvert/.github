# RunConvert

RunConvert is a high-performance, cloud-native file conversion service built with Go that supports a wide range of file formats including documents, images, videos, audio files, CAD drawings, and more. The platform provides enterprise-grade conversion capabilities with team management, billing integration, and scalable microservices architecture.

## Architecture Overview

RunConvert is built as a distributed system with multiple specialized services:

| Service        | Purpose                                  | Technology                 | Port     |
| -------------- | ---------------------------------------- | -------------------------- | -------- |
| **Core**       | Main API, authentication, billing, teams | Go (Fiber)                 | 9047     |
| **Worker**     | File conversion processing               | Go                         | Internal |
| **Storage**    | File upload/download management          | Go                         | 9057     |
| **OnlyOffice** | Document conversion engine               | OnlyOffice Document Server | 8000     |
| **Redis**      | Caching and session management           | Redis                      | 6379     |
| **Proxy**      | Load balancing and SSL termination       | Nginx                      | 443/80   |

## Supported File Formats

### Document Formats

| Input                          | Output Formats                                     |
| ------------------------------ | -------------------------------------------------- |
| **PDF**                        | DOC, DOCX, RTF, TXT, HTML, Images (PNG, JPG, etc.) |
| **Word** (DOC/DOCX)            | PDF, RTF, TXT, HTML, ODT, Images                   |
| **Excel** (XLS/XLSX)           | PDF, CSV, ODS, HTML, Images                        |
| **PowerPoint** (PPT/PPTX)      | PDF, ODP, Images, HTML                             |
| **OpenDocument** (ODT/ODS/ODP) | PDF, Microsoft Office formats, Images              |

### Image Formats

| Category   | Supported Formats                         |
| ---------- | ----------------------------------------- |
| **Raster** | PNG, JPG/JPEG, GIF, BMP, TIFF, WebP, AVIF |
| **Vector** | SVG, EPS, PS, PDF, AI, EMF, WMF           |
| **Raw**    | CR2, NEF, ARW, DNG, RAW                   |

### Video & Audio Formats

| Type      | Input/Output Formats                           |
| --------- | ---------------------------------------------- |
| **Video** | MP4, AVI, MOV, MKV, WebM, FLV, 3GP, WMV, M4V   |
| **Audio** | MP3, WAV, FLAC, AAC, OGG, M4A, AIFF, WMA, OPUS |

### CAD & Technical Formats

| Format              | Supported Conversions          |
| ------------------- | ------------------------------ |
| **AutoCAD**         | DWG, DXF to PDF, SVG, PNG, JPG |
| **Vector Graphics** | AI, EPS, SVG, PDF conversions  |

### Archive & Other Formats

| Category     | Formats               |
| ------------ | --------------------- |
| **Archives** | ZIP, RAR, 7Z, TAR, GZ |
| **E-books**  | EPUB, MOBI, PDF, FB2  |
| **Fonts**    | TTF, OTF, WOFF, WOFF2 |

## Key Features

### Conversion Capabilities

- **Multi-format Support**: Over 200+ input/output format combinations
- **Quality Assessment**: Automatic quality evaluation for each conversion
- **Batch Processing**: Support for multiple file conversions
- **Engine Selection**: Multiple conversion engines (OnlyOffice, LibreOffice, FFmpeg, ImageMagick)
- **Custom Parameters**: Resolution, quality, compression settings

### Enterprise Features

- **Team Management**: Multi-user teams with role-based access
- **API Key Management**: Secure API access with key rotation
- **Billing Integration**: Stripe-powered payment processing
- **Credit System**: Pay-per-conversion pricing model
- **Usage Analytics**: Detailed conversion statistics and reporting

### Security & Reliability

- **JWT Authentication**: Secure token-based authentication
- **File Encryption**: Encrypted file storage and transmission
- **Rate Limiting**: API rate limiting and abuse prevention
- **Health Monitoring**: Comprehensive health checks and monitoring
- **Auto-scaling**: Container-based scalable architecture

## API Documentation

### Authentication

All API requests require authentication via JWT tokens or API keys.

```bash
# Using JWT Token
curl -H "Authorization: Bearer YOUR_JWT_TOKEN" \
     https://api.runconvert.com/api/jobs

# Using API Key
curl -H "X-API-Key: YOUR_API_KEY" \
     https://api.runconvert.com/api/jobs
```

### Core Endpoints

| Method | Endpoint             | Description             |
| ------ | -------------------- | ----------------------- |
| `POST` | `/api/jobs`          | Create conversion job   |
| `GET`  | `/api/jobs/{id}`     | Get job status          |
| `GET`  | `/api/jobs`          | List user jobs          |
| `POST` | `/api/upload`        | Upload files            |
| `GET`  | `/api/download/{id}` | Download converted file |

### Conversion API Example

```bash
# Create a conversion job
curl -X POST https://api.runconvert.com/api/jobs \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "input_format": "docx",
    "output_format": "pdf",
    "file_url": "https://storage.runconvert.com/files/document.docx",
    "settings": {
      "quality": "high",
      "compression": "medium"
    }
  }'
```

### Response Format

```json
{
  "success": true,
  "data": {
    "job_id": "60a7c8b4f123456789abcdef",
    "status": "processing",
    "progress": 0,
    "input_format": "docx",
    "output_format": "pdf",
    "created_at": "2024-01-15T10:30:00Z",
    "estimated_completion": "2024-01-15T10:32:00Z"
  }
}
```

## Installation & Deployment

### Prerequisites

- Docker and Docker Compose
- Git
- MongoDB database
- Redis instance
- Environment configuration files

### Quick Start with Docker

```bash
# Clone the repository
git clone https://github.com/runconvert/runconvert-backend.git
cd runconvert-backend

# Set up environment files
cp core/.env.example core/.env
cp worker/.env.example worker/.env
cp storage/.env.example storage/.env

# Start all services
docker-compose up -d --build
```

### Environment Configuration

Create the following environment files:

#### Core Service (`.env`)

```env
# Server Configuration
SERVER_PORT=9047
SERVER_MODE=production

# Database
MONGODB_URI=mongodb://localhost:27017/runconvert
REDIS_URL=redis://localhost:6379

# Authentication
JWT_SECRET=your-super-secret-jwt-key
JWT_EXPIRATION=24h

# Billing
STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...

# File Storage
STORAGE_HOST=https://storage.runconvert.com
MAX_FILE_SIZE=104857600  # 100MB
```

#### Worker Service (`.env`)

```env
# OnlyOffice Configuration
ONLYOFFICE_SERVER_URL=http://onlyoffice:80
ONLYOFFICE_JWT_SECRET=your-onlyoffice-jwt-secret

# Conversion Settings
MAX_CONCURRENT_CONVERSIONS=5
CONVERSION_TIMEOUT=600s
TEMP_DIR=/tmp/conversions
```

### Service Configuration

| Service    | Configuration File | Purpose                   |
| ---------- | ------------------ | ------------------------- |
| Core       | `core/.env`        | API server, auth, billing |
| Worker     | `worker/.env`      | Conversion processing     |
| Storage    | `storage/.env`     | File management           |
| OnlyOffice | `onlyoffice/.env`  | Document conversion       |
| Redis      | `redis/.env`       | Cache configuration       |

## Development Setup

### Local Development

```bash
# Install Go dependencies
cd core && go mod download
cd ../worker && go mod download
cd ../storage && go mod download

# Run services individually
cd core && go run cmd/server/main.go
cd worker && go run cmd/worker/main.go
cd storage && go run cmd/server/main.go
```

### Testing

```bash
# Run unit tests
go test ./...

# Run integration tests
docker-compose -f docker-compose.test.yml up --abort-on-container-exit

# Test specific conversion
./scripts/test-archive-worker.sh
```

## Monitoring & Observability

### Health Checks

| Service    | Health Endpoint    | Status          |
| ---------- | ------------------ | --------------- |
| Core       | `GET /health`      | API health      |
| Storage    | `GET /health`      | Storage health  |
| OnlyOffice | `GET /healthcheck` | Document server |
| Redis      | Redis PING         | Cache status    |

### Metrics & Logging

- **Structured Logging**: JSON-formatted logs with correlation IDs
- **Performance Metrics**: Response times, throughput, error rates
- **Resource Monitoring**: CPU, memory, disk usage per service
- **Conversion Analytics**: Success rates, format popularity, processing times

## Production Deployment

### Docker Swarm / Kubernetes

```yaml
# docker-stack.yml
version: "3.8"
services:
  core:
    image: runconvert/core:latest
    deploy:
      replicas: 3
      restart_policy:
        condition: on-failure
    networks:
      - runconvert
    environment:
      - SERVER_MODE=production
```

### Load Balancing

The included Nginx proxy configuration provides:

- SSL termination with Let's Encrypt
- Rate limiting (10 requests/second burst)
- Load balancing across service instances
- Static file serving
- WebSocket support for real-time updates

### Scaling Considerations

| Component    | Scaling Strategy         |
| ------------ | ------------------------ |
| **Core API** | Horizontal (stateless)   |
| **Workers**  | Horizontal (queue-based) |
| **Storage**  | Vertical + CDN           |
| **Database** | Read replicas            |
| **Redis**    | Clustering               |

## Billing & Pricing

### Credit System

RunConvert uses a credit-based pricing model:

| Conversion Type                 | Credits Required |
| ------------------------------- | ---------------- |
| **Document** (PDF, Word, Excel) | 1-3 credits      |
| **Image** (Basic formats)       | 1 credit         |
| **Video/Audio** (per minute)    | 5-10 credits     |
| **CAD** (DWG, DXF)              | 5 credits        |
| **Batch** (per file)            | Variable         |

### Payment Integration

```bash
# Get pricing information
curl https://api.runconvert.com/api/billing/prices/document/100

# Purchase credits
curl -X POST https://api.runconvert.com/api/billing/purchase \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"credits": 100, "payment_method": "pm_card_visa"}'
```

## Security

### Authentication Methods

- **JWT Tokens**: For web applications
- **API Keys**: For server-to-server integration
- **OAuth2**: Google, GitHub, Facebook integration

### Data Protection

- **Encryption**: Files encrypted at rest and in transit
- **Access Control**: Role-based permissions
- **Audit Logging**: Complete audit trail
- **Data Retention**: Configurable file retention policies

### Security Headers

```
Content-Security-Policy: default-src 'self'
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Strict-Transport-Security: max-age=31536000
```

## Support & Documentation

### API Documentation

- **OpenAPI Spec**: Available at `/api/docs`
- **Postman Collection**: Import ready collection
- **SDKs**: Available for JavaScript, Python, Go

### Community & Support

- **Documentation**: [docs.runconvert.com](https://docs.runconvert.com)
- **API Reference**: [api.runconvert.com/docs](https://api.runconvert.com/docs)
- **Status Page**: [status.runconvert.com](https://status.runconvert.com)
- **Support Email**: support@runconvert.com

### Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

**RunConvert** - High-performance file conversion service built for scale.

Copyright 2025 RunConvert LLC. All rights reserved.
