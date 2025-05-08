# Blockchain-Based Decentralized Access Control Demo

This project demonstrates a blockchain-based network security system for decentralized access control. It includes a Node.js backend interacting with a simulated blockchain ledger and a React frontend for user interaction.

## Project Structure

```
/
├── backend/                  # Node.js Express server
│   ├── server.js             # Main backend logic, API endpoints
│   └── package.json          # Backend dependencies
├── frontend/                 # React application (created with Vite)
│   ├── src/
│   │   ├── App.jsx           # Main React application component
│   │   ├── App.css           # Styles for App.jsx
│   │   └── main.jsx          # Entry point for React app
│   ├── public/
│   ├── index.html            # Main HTML file for React app
│   └── package.json          # Frontend dependencies
└── README.md                 # This file
```

## Prerequisites

*   Node.js (v16 or later recommended)
*   npm (comes with Node.js)

## Setup and Running

Follow these steps to set up and run the project:

### 1. Backend Setup

Navigate to the `backend` directory and install dependencies:

```bash
cd backend
npm install
```

Start the backend server:

```bash
npm start
```

The backend server will run on `http://localhost:3000` by default. You should see a message like: `Blockchain Access Control API running on http://localhost:3000`.

### 2. Frontend Setup

In a **new terminal window or tab**, navigate to the `frontend` directory and install dependencies:

```bash
cd frontend
npm install
```

Start the frontend development server:

```bash
npm run dev
```

The React application will typically open automatically in your browser or provide a local URL (e.g., `http://localhost:5173`).

### 3. Using the Application

Once both backend and frontend are running:

1.  **User Registration**:
    *   Enter a unique username.
    *   Provide a public key in PEM format. You can generate RSA key pairs using tools like OpenSSL or online generators. For testing, any multi-line string that looks like a PEM key will be accepted by the backend, but for actual signature verification, a valid PEM-encoded public key is needed.
    *   Click "Register User".

2.  **Grant Access**:
    *   Select a registered user from the dropdown.
    *   Enter a resource ID (e.g., `document_alpha`, `printer_main`).
    *   Click "Grant Access". This action will be recorded on the simulated blockchain.

3.  **Request Access**:
    *   Select a registered user.
    *   Enter the resource ID they want to access.
    *   Provide a "Message to Sign". This should be a unique message that the user would sign with their private key (corresponding to the public key they registered).
    *   Paste the Base64 encoded signature of that message into the "Signature (Base64)" field.
        *   **Note on Signatures**: Since this is a simulation and we don't have built-in key generation or signing in the browser, you would typically use an external tool (like OpenSSL command line, a crypto library in a Node.js script, or browser developer console with `crypto.subtle.sign`) to generate the signature for the *exact* message using the private key. Then, you'd Base64 encode that signature and paste it.
    *   Click "Request Access". The backend will verify if access was granted on the blockchain and if the provided signature is valid for the given message and the user's registered public key.

4.  **Blockchain Ledger**:
    *   The latest blocks from the simulated blockchain will be displayed at the bottom of the page, updating automatically after relevant actions.

## Key Features Demonstrated

*   **REST API Backend**: User registration, access grant, access request, and ledger query endpoints.
*   **Simulated Blockchain**: In-memory array acting as a blockchain to store access policies.
*   **Signature Verification**: Access requests are validated using cryptographic signatures (requires external generation of keys and signatures for this demo).
*   **React Frontend**: Interactive UI for all operations, communicating with the backend via `fetch` API.
*   **Modern UI**: Styled with CSS for a clean and responsive experience.

## Generating Keys and Signatures (Example with OpenSSL)

This is for advanced users who want to test the signature verification properly.

1.  **Generate a Private Key**:
    ```bash
    openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
    ```

2.  **Extract Public Key**:
    ```bash
    openssl rsa -pubout -in private_key.pem -out public_key.pem
    ```
    Copy the content of `public_key.pem` for user registration.

3.  **Sign a Message**:
    Create a file (e.g., `message.txt`) with the exact text you will put in the "Message to Sign" field.
    ```bash
    openssl dgst -sha256 -sign private_key.pem -out signature.bin message.txt
    ```

4.  **Base64 Encode Signature**:
    ```bash
    openssl base64 -in signature.bin -out signature.txt -A
    ```
    Copy the content of `signature.txt` and paste it into the "Signature (Base64)" field in the application.

    *(The `-A` flag for `openssl base64` ensures it's a single line; adjust if your OpenSSL version differs or use an online Base64 encoder for `signature.bin`)*

## FAQ

**Q: What is this project about?**

A: This project is a simulation of a blockchain-based network security system for decentralized access control. It features a Node.js backend that manages a policy ledger (simulated blockchain) and a React frontend allowing users to interact with this system.

**Q: How does user registration work?**

A: Users can register through the frontend interface. Upon registration, the system associates a unique identity (username) with cryptographic key information. The `KeyGenerator` component can be used to generate a sample key pair (public and private keys). The public key is then used for registration.

**Q: How is access granted to a resource?**

A: An authorized entity (simulated in this demo by any registered user) can grant access to a specific resource for another registered user. This action is recorded as a transaction on the simulated blockchain, which involves creating and "mining" a new block.

**Q: How does a user request access to a resource?**

A: A registered user can request access to a resource. This involves creating a structured access request message, which is then cryptographically signed using the user's private key (managed within the app session after registration/key generation). The backend verifies this signature against the user's registered public key and checks the blockchain to confirm if access has indeed been granted.

**Q: What is the "blockchain" in this project?**

A: The "blockchain" is an in-memory simulation. Each "block" stores data (like access grants or other policy changes), a timestamp, its own unique hash, the hash of the preceding block, a nonce, and the difficulty level used during its "mining." This structure creates an immutable and auditable log of access policies.

**Q: How is the integrity of the blockchain maintained?**

A: Integrity is maintained via cryptographic hashing. Each block's hash is computed from its content and includes the hash of the previous block, linking them together in a chain. Any attempt to tamper with a block's data will alter its hash, which in turn would break the chain's continuity from that point forward. This mechanism is visualized in the "Blockchain Visualizer" component.

**Q: What is "Proof-of-Work" (PoW) in this simulation?**

A: When a new block (e.g., recording an access grant) is added to the chain, the backend simulates a "mining" process. This involves iterating through "nonce" values until a hash is found that meets a predefined `DIFFICULTY` target (e.g., the hash must start with a specific number of zeros). This makes adding blocks computationally intensive, demonstrating a core concept from real-world blockchain systems.

**Q: Can I see the blockchain?**

A: Yes, the frontend includes a sophisticated "Blockchain Visualizer." This component displays the blocks in the chain, their interconnections, hashes, data payloads (like user, resource, type of action), nonce, and difficulty. You can click on blocks for more details and even simulate data tampering to observe its effects on the chain's validity.

**Q: What happens if I try to tamper with a block in the visualizer?**

A: The visualizer allows you to simulate tampering with a block's data (for any block other than the Genesis block). If you do this, the block's data will change, causing its hash to be recalculated. This new hash will typically no longer match the `prevHash` pointer of the subsequent block. The visualizer will then clearly indicate that the chain is "broken" at that point, highlighting the tampered block and the invalid link.

**Q: What technologies are used?**

A:
*   **Backend**: Node.js, Express.js
*   **Frontend**: React (bootstrapped with Vite), HTML5, CSS3, JavaScript (ES6+)
*   **Cryptography Simulation**: The project simulates cryptographic principles like hashing for block integrity, key pairs for user identity, and digital signatures for verifying access requests. Proof-of-Work is also simulated.

**Q: How is the user experience for signing requests streamlined?**

A: Initially, key management and signing were more manual. The current version simplifies this:
    1.  The `KeyGenerator` can create a public/private key pair.
    2.  Upon successful registration with the generated public key, an `activeSessionUser` is set in the frontend, holding the username, public key, and crucially, the private key for that session.
    3.  When requesting access, the username is auto-filled, and a single "Sign & Request Access" button uses the stored private key of the `activeSessionUser` to automatically sign the structured access request message. This removes the need for manual signature generation and pasting for the active user. 