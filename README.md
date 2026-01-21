# Toy Examples for Formal Analysis Workshop

This repository contains a set of small, simplified protocol models written in SAPIC. The goal of the workshop is to help participants build intuition about formal verification, protocol flaws, and the mechanics of Diffie–Hellman (DH).

## 📁 Repository Structure

```bash
.
├── README.md             # This file
├── 0Xproperties.splib    # Reusable lemmas (Secrecy, Injective Agreement)
├── headers.splib         # SAPIC/Proverif export headers and flag events
│
├── 01_auth_and_secrecy_fail.spthy
├── 02_auth_fails.spthy
├── 03_secrecy_fails.spthy
├── 04_auth_and_secrecy_hold.spthy

```

- headres.splib provides exporting queries to Proverif and flag events
- 0Xpropoerties.splib contains the lemmas for file 0X
- each .spthy file is self-contained and demonstrates a specific property (or failure)

### 01_auth_and_secrecy_fail.spthy

- Purpose: Baseline DH exchange with no cryptographic protection.
- What it shows: A raw DH exchange where an attacker can act as a Man-in-the-Middle (MitM).
- Expected result: Both Secrecy and Authentication are falsified.
- Teaching Point : DH provides forward secrecy if implemented correctly, but on its own, it provides no protection against an active attacker.

### 02_auth_fails.spthy

- Purpose: Demonstrates identity misbinding and lack of freshness.
- Protocol : Shares are signed, but the signature only covers the DH share (G_B) and not the peer's contribution (G_A).
- Expected result: Secrecy holds , but Authentication is falsified .
- Teaching point: Signatures must "bind" the entire context. If B doesn't sign A's share, an attacker can replay B's signature in a different session.

### 03_secrecy_fails.spthy

- Purpose: Demonstrates that authentication does not guarantee secrecy.
- What is changed : The handshake is fully authenticated (binding identities), but the session is secret s is explicitly leaked via out(~s) .
- Expected result: Authentication is verified , but Secrecy is falsified .
- Teaching point: A protocol can be perfectly authenticated while still being insecure if the application layer leaks keys or uses weak encryption.

### 04_auth_and_secrecy_hold.spthy

- Purpose: Corrected version where both properties hold.
- What is fixed:
    - Both parties sign the tuple <G_A, G_B, identity>.
    - By signing the peer's DH share, each party proves they are participating in the current session.
    - No secret material is leaked
- Expected result: Both Secrecy and Authentication are verified.
- Teaching point: Secure key exchange requires binding ephemeral shares to long-term identities.

## How to Run (without Docker)

```bash
tamarin-prover 04_auth_and_secrecy_hold.spthy --prove
```

## 🛠️ Setup (Docker Recommended)

To avoid installing Maude, Tamarin, and ProVerif manually, use the provided Docker environment.

### 1. Prerequisites

- Install Docker Desktop.
- Windows Users: Open Docker Desktop Settings > Resources > WSL Integration and ensure integration is enabled for your Ubuntu/Debian distro.

### 2. Launch the Workshop

Open your terminal in the root folder and run:

```bash
docker-compose up --build
```
Once the logs show Application launched, open your browser to: http://localhost:3001 

## How to Run Analysis

### Option A: Interactive GUI

1. Open the web link above.
2. Click on any .spthy file to load the theory.
3. Click on a Lemma (e.g., secrecy or authentication).
4. Select "Autoprove" to let Tamarin find the proof or the attack.
5. Live Editing: You can edit the files in VS Code on your computer. Simply click "Reload" in the web GUI to see your changes instantly.

### Option B: Command Line (CLI)

If you prefer the terminal, you can run commands through docker-compose without stopping the GUI. 
Open a new terminal window and use:

#### To prove a specific file:

```bash
docker-compose exec tamarin tamarin-prover 04_auth_and_secrecy_hold.spthy --prove
```

#### To run a manual command inside the container

```bash
docker-compose exec tamarin /bin/bash
```

## 🛑 Troubleshooting