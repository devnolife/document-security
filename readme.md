### 1. **Struktur Proyek**

```
/document-security
  /pages
    /api
      document.js        # API untuk mengirim dokumen terenkripsi
    /document.js         # Halaman untuk membuka dokumen yang ter-enkripsi
  /utils
    crypto.js            # Fungsi enkripsi dan dekripsi
  /public
  README.md              # Dokumentasi proyek
  package.json
```

### 2. **`crypto.js` (di dalam folder `/utils/`)**

```js
import CryptoJS from "crypto-js";

// Fungsi untuk mengenkripsi dokumen
export const encryptDocument = (documentContent, secretKey) => {
  return CryptoJS.AES.encrypt(documentContent, secretKey).toString();
};

// Fungsi untuk mendekripsi dokumen
export const decryptDocument = (encryptedDocument, secretKey) => {
  const bytes = CryptoJS.AES.decrypt(encryptedDocument, secretKey);
  return bytes.toString(CryptoJS.enc.Utf8);
};
```

### 3. **`/pages/api/document.js` (API Route di Next.js)**

Ini adalah route API untuk mengirimkan dokumen terenkripsi ke client.

```js
import { encryptDocument } from "../../utils/crypto";

export default function handler(req, res) {
  const { secretKey } = req.query;

  const documentContent = "This is a secure document.";

  if (!secretKey) {
    return res.status(400).json({ error: "Secret key is required" });
  }

  const encryptedDocument = encryptDocument(documentContent, secretKey);

  res.status(200).json({ encryptedDocument });
}
```

### 4. **`/pages/document.js` (Halaman untuk Mendekripsi dan Menampilkan Dokumen)**

Pada halaman ini, dokumen terenkripsi diambil dari API dan didekripsi di sisi client menggunakan kunci yang diberikan.

```js
import { useState, useEffect } from "react";
import { decryptDocument } from "../utils/crypto";

export default function Document() {
  const [encryptedDocument, setEncryptedDocument] = useState(null);
  const [decryptedDocument, setDecryptedDocument] = useState(null);
  const [secretKey, setSecretKey] = useState("");

  const fetchDocument = async () => {
    try {
      const response = await fetch(`/api/document?secretKey=${secretKey}`);
      const data = await response.json();
      setEncryptedDocument(data.encryptedDocument);
    } catch (error) {
      console.error("Error fetching document:", error);
    }
  };

  const handleDecrypt = () => {
    if (encryptedDocument && secretKey) {
      const decrypted = decryptDocument(encryptedDocument, secretKey);
      setDecryptedDocument(decrypted);
    }
  };

  return (
    <div>
      <h1>Secure Document Viewer</h1>
      <div>
        <input
          type="text"
          placeholder="Enter Secret Key"
          value={secretKey}
          onChange={(e) => setSecretKey(e.target.value)}
        />
        <button onClick={fetchDocument}>Fetch Encrypted Document</button>
      </div>
      {encryptedDocument && (
        <div>
          <h2>Encrypted Document</h2>
          <pre>{encryptedDocument}</pre>
          <button onClick={handleDecrypt}>Decrypt Document</button>
        </div>
      )}
      {decryptedDocument && (
        <div>
          <h2>Decrypted Document</h2>
          <p>{decryptedDocument}</p>
        </div>
      )}
    </div>
  );
}
```

````markdown
# Secure Document Viewer

This is a simple application built with Next.js that demonstrates how to securely encrypt and decrypt documents. The documents are stored in an encrypted format and can only be viewed within the client-side application by providing the correct secret key. If the document is downloaded or accessed outside the application, it remains encrypted.

## Features

- Encrypt documents using AES encryption.
- Decrypt documents on the client-side with a secret key.
- Prevent unauthorized access to documents by keeping them encrypted outside the application.

## Setup and Installation

1. Clone the repository:

```bash
git clone https://github.com/devnolife/document-security.git
```
````

2. Install dependencies:

```bash
npm install
```

3. Run the development server:

```bash
npm run dev
```

4. Open your browser and navigate to `http://localhost:3000/document`.

## How It Works

- The document is encrypted using AES encryption before it is sent to the client.
- The client fetches the encrypted document from the `/api/document` route.
- The client must provide the correct secret key to decrypt and view the document.
- If the document is downloaded without decryption, it remains in an encrypted format.

## Project Structure

- `/pages/api/document.js`: API route that returns the encrypted document.
- `/pages/document.js`: Client-side page for fetching and decrypting the document.
- `/utils/crypto.js`: Utility functions for encrypting and decrypting documents using AES.

## Security Considerations

- The secret key should not be stored or hardcoded in the client application.
- Make sure to use secure transmission methods (HTTPS) to prevent unauthorized access to the encrypted document.
- This example is simplified for demonstration purposes and may require additional security measures for production use.

## Future Enhancements

- Implement user authentication to further secure access to the encrypted documents.
- Add file upload functionality for users to encrypt and store their own documents.
- Implement document access logging to track when and by whom documents are viewed.

## License

This project is open-source and free to use.

````

### 6. **Dependencies**

Jangan lupa untuk menginstall dependensi yang dibutuhkan:

```bash
npm install crypto-js
````

### Cara Kerja

1. **Enkripsi Dokumen**: Dokumen terenkripsi di sisi server menggunakan kunci rahasia.
2. **Dekripsi Dokumen**: Di sisi client, user memasukkan kunci rahasia untuk mendekripsi dokumen.
3. **Dokumen Terproteksi**: Jika dokumen diunduh tanpa dekripsi, file tersebut tetap terenkripsi dan tidak bisa dibaca.

Ini adalah contoh sederhana yang bisa diperluas lebih lanjut sesuai kebutuhan, misalnya menambahkan otentikasi, izin akses, atau fungsi lainnya.
