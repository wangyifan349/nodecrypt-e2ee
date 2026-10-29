# 🔐 NodeCrypt E2EE

NodeCrypt E2EE is a lightweight, anonymous, real-time encrypted group chat application. The browser performs the actual encryption and decryption; the FastAPI server acts as a blind WebSocket relay. 🌐

This is a single-file rewrite of [shuaiplus/nodecrypt](https://github.com/shuaiplus/nodecrypt). The complete web client, UI, browser cryptography, relay protocol, and FastAPI server are embedded in `nodecrypt_e2ee.py`.

## ✨ Features

- 👥 End-to-end encrypted group rooms
- 💬 Public group chat and encrypted private messages
- 🖼️ Image messages and encrypted file transfer
- 🔐 Browser-side encryption with no plaintext chat processing on the server
- 🧩 Collapsible rooms panel and members panel
- 🧠 Optional opaque ciphertext archiving
- 🛠️ No Node.js, npm, frontend build step, or separate web server

## 🚀 Install and run

```bash
git clone https://github.com/wangyifan349/nodecrypt-e2ee.git
cd nodecrypt-e2ee
pip install fastapi uvicorn cryptography
python3 nodecrypt_e2ee.py
```

Open <http://127.0.0.1:8000/> in a modern browser.

These are the only required commands. The project does not require npm, Node.js, Vite, Docker, or a separate frontend process. 🐍

## 🔒 Encryption model

The encryption flow is intentionally simple and remains client-centered:

1. The browser generates a P-384 ECDH key pair for the relay connection.
2. The server signs its P-384 public key with a temporary RSA-2048 identity key.
3. After verification, the browser and server use AES-256-CBC for encrypted relay envelopes.
4. Each group member has a separate Curve25519 shared key with every other member.
5. Chat content is encrypted in the browser with ChaCha20 before it is sent through the relay.
6. The room password is mixed into the client-to-client key derivation, isolating rooms with different passwords.

The server can see connection metadata such as timing, opaque client IDs, room hashes, and ciphertext sizes. It cannot see usernames, messages, images, file contents, room passwords, or client-to-client decryption keys. Use HTTPS/WSS and strong room passwords in production. ⚠️

## 🧠 Ciphertext memory

The optional memory feature stores only opaque encrypted payloads. It never stores plaintext chat content. The default database is placed in the operating system temporary directory; set `NODECRYPT_DB_PATH` if persistent storage is required.

Because the unchanged client uses ephemeral client-to-client keys, this archive is not presented as readable history after a new connection. The server never decrypts, reconstructs, or fabricates chat history.

## ⚙️ Configuration

Useful environment variables:

```text
HOST=0.0.0.0
PORT=8000
NODECRYPT_MEMORY_ENABLED=1
NODECRYPT_DB_PATH=/path/to/nodecrypt.sqlite3
NODECRYPT_MEMORY_RETENTION_DAYS=30
NODECRYPT_MEMORY_MAX_MESSAGES=1000
```

## 📚 Attribution and license

Based on and rewritten from [github.com/shuaiplus/nodecrypt](https://github.com/shuaiplus/nodecrypt). 🙏

This project is licensed under **AGPL-3.0-only**. 📄
