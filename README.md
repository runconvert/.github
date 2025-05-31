# RunConvert

**RunConvert** is a fast, secure, and easy-to-use online file converter that supports thousands of file format conversions including documents, images, audio, video, CAD, vector, archives, fonts, eBooks, spreadsheets, and more.

🌐 [Visit RunConvert](https://www.runconvert.com)

---

## 🚀 Features

- 🔄 Supports 4000+ file conversions
- 🧠 Smart format detection
- 🌍 Location-based server routing for faster conversion (US & AU)
- 📥 Upload large files in chunks (S3 compatible)
- 🧩 Real-time conversion status via WebSockets
- ⚙️ API ready (JSON, multipart/form-data)
- 🛡️ Secure file handling with auto-deletion

---

## 📦 Tech Stack

- **Frontend**: Next.js 15+, Tailwind CSS
- **Backend**: Node.js, Express, Docker, WebSocket (Socket.IO)
- **Storage**: AWS S3
- **Database**: MongoDB (Mongoose)
- **CI/CD**: GitHub Actions
- **Deployment**: Oracle Cloud, AWS EC2
- **Others**: Zod, n8n, Redis (queue), Riverpod (Flutter app)

---

## 🛠️ Local Development

```bash
# Clone the repo
git clone https://github.com/your-org/runconvert.git
cd runconvert

# Install dependencies
npm install

# Start the backend server
npm run dev

# Start Next.js frontend (if in monorepo)
cd frontend
npm run dev
