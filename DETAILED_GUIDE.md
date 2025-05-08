# Blockchain-Based Decentralized Access Control Demo - Full Guide

This project demonstrates a blockchain-based network security system for decentralized access control. It features a Node.js backend with a simulated Proof-of-Work blockchain and a React frontend with advanced visualizations and cryptographic tools.

## Core Concepts Used

1.  **Blockchain Technology**:
    *   **Blocks**: Units of data containing an index, timestamp, data (access grants), previous block's hash, a nonce, and its own hash.
    *   **Chaining**: Each block cryptographically links to the previous one using hashes, forming an immutable chain.
    *   **Hashing (SHA-256)**: Used to generate a unique fingerprint (hash) for each block. Any change in block data results in a different hash.
    *   **Proof-of-Work (Simulated)**:
        *   **Nonce**: A "number used once" that is incrementally changed during mining.
        *   **Difficulty**: A predefined target (e.g., hash must start with a certain number of zeros).
        *   **Mining**: The (simulated) process of finding a nonce that, when combined with other block data, produces a hash meeting the difficulty target.
    *   **Immutability**: Once a block is added, it cannot be altered without invalidating its hash and breaking the chain from that point onwards. The frontend visualizer demonstrates this with a tampering simulation.
    *   **Genesis Block**: The first block in the chain, with a `prevHash` of '0' and a default nonce.

2.  **Cryptography**:
    *   **Asymmetric Key Cryptography (RSA)**: Uses public-private key pairs.
        *   **Public Key**: Shared openly, used to verify signatures and for registration.
        *   **Private Key**: Kept secret by the user, used to create digital signatures.
    *   **Digital Signatures (RSASSA-PKCS1-v1_5 with SHA-256)**:
        *   Users sign messages (structured access requests) with their private key.
        *   The backend verifies these signatures using the user's registered public key to authenticate the request.
    *   **PEM Format**: Standard format for storing and transmitting cryptographic keys.

3.  **REST API Backend (Node.js & Express)**:
    *   Exposes endpoints for: user registration, granting resource access (creates a new block), requesting access (verifies against blockchain and signature), listing users, and fetching the blockchain.
    *   In-memory data stores for users and the blockchain chain.

4.  **React Frontend (Vite)**:
    *   Provides a user interface for all interactions.
    *   **Key Generator**: In-browser tool to generate RSA public/private key pairs (for testing).
    *   **Signature Generator**: In-browser tool to sign structured access request messages using a provided private key.
    *   **Blockchain Visualizer**: Advanced, interactive component showing:
        *   Blocks with their details (index, timestamp, data, nonce, difficulty, hashes).
        *   Animated chain links.
        *   Visual indication of chain integrity (valid/broken).
        *   Simulation of new block mining (with difficulty and nonce searching).
        *   Interactive block tampering simulation to demonstrate immutability.
        *   Popovers for detailed block inspection.
        *   Legend for visual elements.

## Project Structure

```
/
├── backend/
│   ├── server.js         # Main backend logic, API, PoW blockchain simulation
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── App.jsx               # Main React application component
│   │   ├── App.css               # Global styles for App.jsx
│   │   ├── main.jsx              # React app entry point
│   │   ├── index.css             # Root CSS, font setup
│   │   └── components/           # Reusable React components
│   │       ├── BlockchainVisualizer.jsx
│   │       ├── BlockchainVisualizer.css
│   │       ├── SignatureGenerator.jsx
│   │       ├── SignatureGenerator.css
│   │       ├── KeyGenerator.jsx
│   │       └── KeyGenerator.css
│   ├── public/                 # Static assets (not used for sample_key.txt anymore)
│   ├── index.html
│   ├── vite.config.js
│   └── package.json
└── README.md                     # This file (will be updated)
```

## Setup and Installation

**Prerequisites**:
*   Node.js (v16 or later recommended)
*   npm (comes with Node.js)

**Steps**:

1.  **Clone/Download the Project**

2.  **Backend Setup**:
    *   Open a terminal and navigate to the `backend` directory:
        ```bash
        cd backend
        ```
    *   Install dependencies:
        ```bash
        npm install
        ```

3.  **Frontend Setup**:
    *   Open a **new, separate** terminal and navigate to the `frontend` directory:
        ```bash
        cd frontend
        ```
    *   Install dependencies:
        ```bash
        npm install
        ```

## Running the Application

1.  **Start the Backend Server**:
    *   In the `backend` terminal, run:
        ```bash
        npm start 
        # Or: node server.js
        ```
    *   The server will start, typically on `http://localhost:3001`. You'll see logs including `Blockchain Access Control API running on http://localhost:3001 with Difficulty X`.
    *   The backend automatically creates a Genesis block when it starts.

2.  **Start the Frontend Development Server**:
    *   In the `frontend` terminal, run:
        ```bash
        npm run dev
        ```
    *   Vite will compile the React app and provide a local URL (e.g., `http://localhost:5173` or similar). Open this URL in your web browser.

## Workflow: How It Works Step-by-Step

1.  **Generate Keys (Frontend)**:
    *   In the "User Registration" card, click the "Generate New RSA Key Pair" button.
    *   The `KeyGenerator` component uses `window.crypto.subtle.generateKey` to create a 2048-bit RSA key pair.
    *   The public key (in PEM format) is automatically populated into the "Public Key" field for registration.
    *   The private key (in PEM format) is displayed; **copy and save this securely**. You'll need it to sign requests.

2.  **Register User (Frontend -> Backend)**:
    *   Enter a unique username.
    *   The public key from step 1 is already there.
    *   Click "Register User".
    *   Frontend sends a POST request to `/api/register` with `username` and `publicKeyPem`.
    *   Backend (`server.js`):
        *   Validates input.
        *   Stores the user and their public key in the `users` Map.
        *   Logs the registration event.
        *   Returns a success message.

3.  **Grant Access (Frontend -> Backend -> Blockchain)**:
    *   In the "Grant Access" card, select the registered user and enter a Resource ID (e.g., `file_A`).
    *   Click "Grant Access".
    *   Frontend sends a POST request to `/api/grant-access` with `username` and `resource`.
    *   Backend (`server.js`):
        *   Validates input and checks if the user exists.
        *   Calls `blockchain.addBlock({ type: 'accessGrant', resource, username })`.
        *   **Inside `addBlock` (Blockchain Simulation)**:
            *   A new block structure is prepared (index, timestamp, data, prevHash).
            *   `mineBlock` is called: It (simulated) iterates `nonce` values, computing a hash with each nonce (`computeHash`).
            *   The goal is to find a hash that meets the `DIFFICULTY` (e.g., starts with '00').
            *   The backend logs the mining process (difficulty, attempts, nonce found).
            *   Once a valid nonce/hash is found (or simulation limit reached), the block is finalized with this nonce, hash, and current difficulty.
            *   The new block is added to the `this.chain` array.
        *   The backend logs the grant success and block details.
        *   Returns a success message and the newly created block.
    *   Frontend: Updates the message banner and fetches the latest blockchain to refresh the visualizer, showing the new block being "mined" and added.

4.  **Request Access (Frontend -> Backend -> Blockchain & Crypto Verification)**:
    *   In the "Request Access" card:
        *   Select the user, enter the exact Resource ID they were granted access to.
        *   Enter any details in the "Message Details (Optional)" field.
    *   **Signature Generation (Frontend - `SignatureGenerator`)**:
        *   The `App.jsx` component constructs a structured JSON message for signing. This message includes `action`, `user`, `resource`, `timestamp`, and the `details` you entered.
            ```json
            {
              "action": "requestAccess",
              "user": "your_username",
              "resource": "file_A",
              "timestamp": "2023-01-01T12:00:00.000Z",
              "details": "your_optional_details"
            }
            ```
        *   This structured message (as a string) is passed to the `SignatureGenerator` component.
        *   In the `SignatureGenerator`:
            *   Paste the **private key** you saved from Step 1.
            *   The structured message to be signed is displayed (read-only).
            *   Click "Generate Signature for Request".
            *   `window.crypto.subtle.sign` is used with the private key and the stringified structured message to create a digital signature.
            *   The signature (Base64 encoded) is displayed and automatically passed back to the `App.jsx` component, populating the main form's (hidden/internal) signature field.
    *   Click "Request Access" on the main form (or use "Quick Access" for simplified testing).
    *   Frontend sends a POST request to `/api/request-access` with `username`, `resource`, the **stringified structured message**, and the `signature`.
    *   Backend (`server.js`):
        *   Logs the received request details.
        *   Validates input and checks if the user exists.
        *   **Policy Check**: Iterates through `blockchain.chain` to find an `accessGrant` block matching the user and resource.
            *   If no grant is found, logs failure and returns 403 Forbidden.
        *   **Signature Verification**: Calls `verifySignature(user.publicKeyPem, signature, message)`.
            *   The `message` here is the stringified structured JSON received from the client.
            *   The backend uses `crypto.createVerify('SHA256').verify()` with the user's registered public key to check if the signature is valid for the given message.
            *   Logs the verification attempt and result.
            *   If the signature is invalid, logs failure and returns 403 Forbidden.
        *   If both checks pass, logs success and returns 200 OK with an access granted message.
    *   Frontend: Displays success or failure message. If successful, the blockchain visualizer might show an access log block if implemented.

5.  **Blockchain Visualization (Frontend - `BlockchainVisualizer`)**:
    *   Continuously (or via polling/updates) fetches the chain from `/api/chain`.
    *   Renders each block, showing its index, timestamp, data (parsed for grant type), nonce, difficulty, and truncated hashes.
    *   **Chain Integrity**: Calculates validity by checking if `block[i].prevHash === block[i-1].hash`. Displays a "Chain Valid" or "Chain Broken" status.
    *   **Invalid Blocks**: If a hash mismatch is detected, the invalid block and the connector to it are visually marked (e.g., red border, broken line icon).
    *   **Tampering Simulation**: Clicking a block and then "Simulate Tamper" in its popover:
        *   Modifies the block's data locally in the frontend state.
        *   Recalculates its hash (using a simplified JS hash function for visualization only).
        *   The visualizer instantly shows the new (tampered) hash and how it breaks the chain validity from that point onwards. The `originalHash` is preserved to show the difference and allow resetting.
    *   **Mining Animation**: When a new block is fetched that wasn't previously displayed, a "Mining New Block..." animation plays, showing difficulty and nonce searching before the block appears in the chain.
    *   **Popovers**: Clicking a block shows a modal with all its details, including a warning if the block is part of a broken chain segment or if it's the tampered block itself.
    *   **Legend**: Explains the meaning of different visual cues (colors, icons for block types, validity, tampering).

## Troubleshooting Common Issues

*   **"Failed to fetch" / `ERR_CONNECTION_REFUSED`**: Backend server is not running or not accessible at the URL configured in frontend (`API_BASE` in `App.jsx`). Check if the backend terminal shows it running on the correct port (e.g., 3001).
*   **"Invalid signature"**: 
    1.  The private key used for signing in `SignatureGenerator` does not match the public key registered for the user.
    2.  The message being signed by `SignatureGenerator` is not *exactly* the same stringified structured JSON that the backend is expecting. (The UI now handles passing this correctly).
    3.  Copy-paste errors in keys or message if manual input was involved.
    *   **Solution**: Always use the `KeyGenerator` to get a fresh pair. Copy the private key carefully. Let the `SignatureGenerator` use the auto-filled structured message.
*   **403 Forbidden on Access Request**: 
    *   Could be "Invalid signature" (see above).
    *   Could be "Access not granted on blockchain": Ensure you have successfully run the "Grant Access" step for the specific user and resource ID *before* attempting to request access.
*   **Frontend UI issues / Vite errors**: Open browser developer console (F12) for JavaScript errors. Ensure `npm install` was successful in the `frontend` directory.

This detailed workflow and conceptual explanation should cover all aspects of the project! 