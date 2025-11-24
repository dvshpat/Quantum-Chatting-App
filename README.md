# Vajra: A Post-Quantum Secure Chat Application

▶️ [Watch the demo on YouTube](https://youtu.be/3Gy8Ksx9pXc?si=c-tX38NdLCcr03Mg)




This is a real-time, end-to-end encrypted chat application built to be secure not just today, but in the post-quantum future. It addresses the threat of quantum computers breaking current encryption standards by implementing NIST-standardized quantum-resistant cryptographic algorithms.

The application demonstrates that it's possible to build quantum-safe tools without sacrificing the snappy, real-time experience of modern messaging apps.

## Key Features

*   **Quantum-Resistant Security**: Uses **CRYSTALS-Kyber** for Key Encapsulation Mechanism (KEM) to securely establish shared secrets, and **CRYSTALS-Dilithium** for digital signatures to authenticate every message.
*   **Passwordless Authentication**: Implements modern, secure, and phishing-resistant login using **WebAuthn**, allowing users to authenticate with biometrics or hardware security keys.
*   **End-to-End Encryption (E2EE)**: Messages are encrypted on the sender's device and can only be decrypted by the intended recipient. The server has zero knowledge of the message content.
*   **Real-Time Chat**: Built with WebSockets for instant messaging, online status indicators, and access to chat history.
*   **Secure Session Management**: Employs JSON Web Tokens (JWTs) stored in secure, HTTP-only cookies for robust session handling.

## The Tech Stack

*   **Frontend**: A responsive **React.js** Single-Page Application built with **Vite**.
*   **Backend**: A high-performance **FastAPI** (Python) server handling API requests and WebSocket connections.
*   **Database**: **MongoDB** for storing user profiles, credentials, and encrypted message history.
*   **Cryptography**: The `@noble/post-quantum` library for JavaScript implementations of CRYSTALS-Kyber and CRYSTALS-Dilithium.

## How It Works: The Secure Flow

The app's architecture is designed with security and privacy as the top priority.

1.  **Registration & Key Generation**: When a user registers, their browser generates three sets of keys:
    *   A **WebAuthn** credential for passwordless login.
    *   A **CRYSTALS-Kyber** key pair for encryption.
    *   A **CRYSTALS-Dilithium** key pair for digital signatures.
    
    All private keys are stored securely in the browser's local storage and never leave the device. Only the public keys are sent to the server.

2.  **Sending a Message**:
    *   The sender's app fetches the recipient's **Kyber public key** from the server.
    *   It uses this key to perform a Key Encapsulation, generating a unique shared secret and a ciphertext.
    *   The message is encrypted using **AES-GCM** with the shared secret.
    *   The original plaintext message is signed with the sender's **Dilithium private key**.
    *   The final payload (Kyber ciphertext, AES-encrypted message, IV, and Dilithium signature) is sent to the server.

3.  **Receiving a Message**:
    *   The recipient's app uses its **Kyber private key** and the received Kyber ciphertext to decapsulate and regenerate the exact same shared secret.
    *   It uses this secret to decrypt the message with **AES-GCM**.
    *   Finally, it fetches the sender's **Dilithium public key** from the server and uses it to verify the signature against the decrypted message, ensuring authenticity and integrity.

This entire cryptographic process happens seamlessly on the client side, maintaining a zero-knowledge environment where the server cannot decipher the content of any communication.

## Get it Running

Follow these steps to run the application locally.

### Prerequisites
*   Node.js & npm
*   Python 3.9+ & pip
*   A MongoDB Atlas account (or a local MongoDB instance)
*   Git

### Backend Setup

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/klath123/codeofhonour.git
    cd codeofhonour
    ```

2.  **Navigate to the backend directory and install dependencies:**
    ```bash
    cd backend
    pip install -r requirements.txt
    ```

3.  **Create a `.env` file** in the `backend` directory and add your configuration. See `src/config.py` for all required variables.
    ```env
    # Example .env file
    DATABASE_URL=mongodb+srv://<user>:<password>@cluster.mongodb.net/
    SECRET_KEY=your_super_secret_jwt_key
    ALGORITHM=HS256
    ACCESS_TOKEN_EXPIRE_MINUTES=10080 # 7 days
    
    # WebAuthn Settings
    RP_ID=localhost
    RP_NAME=CodeOfHonour
    FRONTEND_ORIGIN=https://localhost:5173
    ```

4.  **Run the backend server:**
    ```bash
    uvicorn src:app --reload --ssl-keyfile localhost-key.pem --ssl-certfile localhost.pem
    ```
    The server will be running on `https://localhost:8000`.

### Frontend Setup

1.  **Navigate to the frontend directory and install dependencies:**
    ```bash
    cd ../frontend
    npm install
    ```

2.  **Run the frontend development server:**
    ```bash
    npm run dev
    ```

3.  Open your browser and navigate to **`https://localhost:5173`** to use the application.
