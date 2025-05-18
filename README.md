# DANG Token on DSURV Platform

## Project Description

DANG is a fungible token built on the Internet Computer blockchain, accessible and manageable through the DSURV web application. Inspired by platforms like Curve Finance, DSURV allows users to create, acquire, and manage their own DANG crypto tokens. Users can perform a variety of operations, including purchasing DANG tokens, receiving tokens, checking their token balance using their unique Principal ID, and transferring tokens to other users via their Canister ID or Principal ID.

Authentication is a key aspect of the DSURV platform. Users secure their accounts and authorize transactions using their Internet Identity. This involves a verification step, typically through a PIN or fingerprint, facilitated by `identity.ic0.app` (using `identityProvider: "https://identity.ic0.app/#authorize"`). Once authenticated via the Dfinity client auth, users are redirected to the DSURV website where they can access all the token functionalities.

This project aims to provide a seamless and secure experience for interacting with custom fungible tokens on the Internet Computer, showcasing the power and flexibility of the IC ecosystem.

## Technologies Used

The project leverages a modern technology stack for both frontend and backend development:

### Backend

* **Language:** Motoko
* **Framework/SDK:** DFINITY Canister SDK (dfx)
* **Blockchain:** Internet Computer Protocol (ICP)
* **Core Components:**
    * `token`: The main canister responsible for DANG token logic (minting, transferring, balance checks) based on the ICRC-1 fungible token standard (or a similar custom implementation).
    * Logic for user management and interaction with the Internet Identity service.

### Frontend

* **Language:** JavaScript/TypeScript (commonly used with frameworks below)
* **Framework:** React.js or Vue.js (or potentially vanilla JavaScript depending on complexity)
* **Styling:** CSS, Tailwind CSS, or other modern styling solutions.
* **Core Components:**
    * User interface for interacting with DANG token functionalities (balance, transfer, buy/get).
    * Integration with Internet Identity for authentication (`@dfinity/auth-client`).
    * Communication with backend canisters (`@dfinity/agent`).

### Tools & Environment

* **DFINITY Canister SDK (`dfx`):** For project management, local development, canister deployment, and interaction.
* **Node.js & npm/yarn:** For managing frontend dependencies and build processes.
* **Operating System:** Development primarily on Linux (WSL Ubuntu is a supported environment).
* **Version Control:** Git & GitHub.

## Setup and Running the Project (Local Development)

These instructions assume you are using Linux WSL Ubuntu.

### Prerequisites

1.  **Install Node.js and npm:**
    ```bash
    sudo apt update
    sudo apt install nodejs npm -y
    ```
2.  **Install DFINITY Canister SDK (`dfx`):**
    ```bash
    sh -ci "$(curl -fsSL [https://internetcomputer.org/install.sh](https://internetcomputer.org/install.sh))"
    ```
    Follow the on-screen instructions. You might need to open a new terminal or source your profile file (e.g., `source ~/.bashrc` or `source ~/.zshrc`) for `dfx` to be available.

### Cloning the Project

1.  Clone the repository:
    ```bash
    git clone <your-repository-url>
    cd <your-project-directory>
    ```

### Installing Dependencies

1.  Install project dependencies (typically for the frontend):
    ```bash
    npm install
    # or if you use yarn
    # yarn install
    ```

### Starting the Local Replica

1.  Start the local Internet Computer replica. This needs to run in a separate terminal window or in the background.
    ```bash
    dfx start --clean --background
    ```
    * `--clean`: Clears any previous state, useful for a fresh start.
    * `--background`: Runs the replica in the background.

### Deploying Canisters Locally

1.  Deploy your canisters to the local replica:
    ```bash
    dfx deploy
    ```
    This command will:
    * Build your Motoko backend code.
    * Build your frontend assets (if configured in `dfx.json`).
    * Create and install the canisters on your local replica.
    * You will see output including the canister IDs for `token` and `token_assets` (or your frontend canister name).

### Accessing the Local Frontend

1.  After successful deployment, `dfx deploy` will output the URL for your frontend canister. It usually looks something like:
    `http://<token_assets_canister_id>.localhost:4943/`
    Open this URL in your web browser.

## Checking Your DANG Token Balance (Local)

1.  **Find out your Principal ID:** This is your unique identifier on the Internet Computer.
    ```bash
    dfx identity get-principal
    ```
2.  **Save it somewhere.** For example, if your principal ID is `aaaaa-aaaal-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-aa`, note it down.

3.  **Format and store it in a command line variable (optional but recommended for ease of use):**
    ```bash
    OWNER_PUBLIC_KEY="principal \"$(dfx identity get-principal)\""
    ```
4.  **Check that step 3 worked by printing it out:**
    ```bash
    echo $OWNER_PUBLIC_KEY
    ```
    It should output something like: `principal "aaaaa-aaaal-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-aa"`

5.  **Check the owner's DANG token balance:**
    Replace `token` with the actual name of your token canister if it's different (check `dfx.json` or the output of `dfx deploy`).
    ```bash
    dfx canister call token balanceOf "($OWNER_PUBLIC_KEY)"
    ```
    This will return your balance, likely `(0 : nat64)` or `(0 : nat)` if you haven't received any tokens yet.

## Charging the Canister with DANG Tokens (Local)

This step is typically for pre-funding a canister (e.g., the `token` canister itself or another canister that needs DANG tokens for its operations).

1.  **Check your `token` canister ID:**
    ```bash
    dfx canister id token
    ```
2.  **Save the `token` canister ID into a command line variable:**
    ```bash
    CANISTER_PUBLIC_KEY="principal \"$(dfx canister id token)\""
    ```
3.  **Check that the canister ID has been successfully saved:**
    ```bash
    echo $CANISTER_PUBLIC_KEY
    ```
4.  **Transfer DANG tokens to the `token` canister's Principal ID:**
    This command assumes your `token` canister has a `transfer` method that allows transferring tokens to a principal. The `500_000_000` is an example amount; adjust as needed. This command also implies that the identity currently executing `dfx` commands (your default identity) is the one that initially holds or can mint these tokens.
    ```bash
    dfx canister call token transfer "($CANISTER_PUBLIC_KEY, 500_000_000)"
    ```
    *Note: The exact method signature for transferring tokens might vary based on your token standard implementation (e.g., ICRC-1 uses `icrc1_transfer`). Ensure the `transfer` method and its arguments match your canister's interface.*

## Deploying the Project to the Live IC Network

Deploying to the Internet Computer mainnet requires Cycles to pay for computation and storage.

### Prerequisites for Live Deployment

1.  **Acquire Cycles:** You need a Cycles Wallet with sufficient Cycles. You can get Cycles from DFINITY or other providers.
2.  **Ensure your `dfx.json` is configured for the `ic` network.** Usually, this is set up by default.
3.  **Make sure your canisters are assigned a Cycles Wallet in `dfx.json` or that your `dfx` identity has a wallet configured to pay for canister creation.**
    Example in `dfx.json` (under `canisters.<canister_name>`):
    ```json
    "cycles": "your_cycles_wallet_canister_id"
    ```
    Alternatively, `dfx` will prompt you to use your default Cycles Wallet if one is configured with `dfx identity set-wallet <wallet_id>`.

### Deployment Steps

1.  **Create and deploy canisters to the `ic` network:**
    ```bash
    dfx deploy --network ic
    ```
    This process can take a few minutes. `dfx` will build your canisters and deploy them to the live Internet Computer. It will also deposit an initial amount of Cycles into your new canisters if you haven't specified a `cycles_payment` amount.

2.  **Check the live `token` canister ID:**
    ```bash
    dfx canister --network ic id token
    ```
3.  **Save the live `token` canister ID to a command line variable:**
    ```bash
    LIVE_CANISTER_KEY="principal \"$(dfx canister --network ic id token)\""
    ```
4.  **Check that it worked:**
    ```bash
    echo $LIVE_CANISTER_KEY
    ```
5.  **Transfer some DANG tokens to the live `token` canister (if needed, similar to local charging):**
    This assumes your identity has the authority to mint/transfer tokens on the live network.
    ```bash
    dfx canister --network ic call token transfer "($LIVE_CANISTER_KEY, 50_000_000)"
    ```
    *Again, ensure the `transfer` method and arguments are correct for your live canister.*

6.  **Get the live canister ID for your frontend assets (e.g., `token_assets`):**
    ```bash
    dfx canister --network ic id token_assets
    ```
    Replace `token_assets` with the name of your frontend canister if it's different.

7.  **Form the URL to access your live application:**
    Copy the canister ID obtained in step 6 (e.g., `qoctq-giaaa-aaaaa-aaaea-cai`) and append `.icp0.io` (the new standard, preferred over `.raw.ic0.app`) or `.raw.ic0.app` to the end.
    * **Preferred:** `https://<frontend_canister_id>.icp0.io`
        Example: `https://qoctq-giaaa-aaaaa-aaaea-cai.icp0.io`
    * **Alternative:** `https://<frontend_canister_id>.raw.ic0.app`
        Example: `https://qoctq-giaaa-aaaaa-aaaea-cai.raw.ic0.app`

    Open this URL in your browser to interact with your live dApp.

## Applications and Advantages

The DANG token and DSURV platform showcase a powerful model for decentralized finance (DeFi) and custom token economies on the Internet Computer.

### Advantages:

* **True Web3 Experience:** Users interact directly with the blockchain via a web browser, without needing browser extensions for most operations once authenticated with Internet Identity.
* **Enhanced Security:** Leverages the robust security features of the Internet Computer and the secure authentication flow of Internet Identity (including biometrics/PIN). User assets are controlled by their cryptographic keys.
* **Decentralization & Transparency:** All transactions and token states are recorded on the immutable Internet Computer blockchain, providing transparency and auditability.
* **Scalability & Performance:** The Internet Computer is designed for web-speed performance and scalability, allowing for a smooth user experience even with a growing user base.
* **Reduced Gas Fees:** Unlike traditional blockchains, the IC uses a "reverse gas model" where developers (canisters) pay for computation with Cycles. This often translates to zero gas fees for end-users performing transactions.
* **Custom Token Economies:** Enables the creation of bespoke token ecosystems for various applications, such as community rewards, governance, in-app currencies, or loyalty programs.
* **Seamless Integration:** The use of standard web technologies for the frontend and Motoko (a language designed for the IC) for the backend allows for efficient development and integration.
* **User-Friendly Authentication:** Internet Identity provides a more user-friendly authentication mechanism compared to managing raw private keys, making Web3 more accessible to a broader audience.

### Potential Applications:

* **Community Building:** Create tokens to reward active community members or for DAO governance.
* **Decentralized Finance (DeFi):** Serve as a foundational token for more complex DeFi applications like lending, borrowing, or staking within the DSURV ecosystem or beyond.
* **Gaming:** Integrate DANG tokens as in-game currency or for NFT-related functionalities.
* **Content Platforms:** Reward content creators and consumers with DANG tokens.
* **Loyalty Programs:** Businesses can use DANG tokens to implement decentralized loyalty and reward systems.

## Key Features

* **DANG Token Creation & Management:** Users can acquire DANG tokens through the DSURV platform.
* **Secure Authentication:** Integration with Internet Identity for robust and user-friendly login using PIN or biometrics.
* **Token Balance Check:** Users can easily view their current DANG token holdings.
* **Token Transfers:** Ability to send DANG tokens to other Principal IDs or Canister IDs.
* **Receive Tokens:** Functionality to receive DANG tokens from other users.
* **On-Chain Operations:** All token transactions are processed and recorded directly on the Internet Computer blockchain.
* **Web-Based Interface:** Accessible through any modern web browser via the DSURV website.

## How to Use the DSURV Website

1.  **Navigate to the DSURV website URL** (either local or live).
2.  **Authenticate:** You will be prompted to authenticate using your Internet Identity.
    * You'll be redirected to `identity.ic0.app`.
    * Enter your Internet Identity anchor/number.
    * Verify your identity using your registered device (e.g., with a PIN, fingerprint, or security key).
    * Authorize the DSURV application to use your identity.
3.  **Access Dashboard/Features:** Once authenticated, you will be redirected back to the DSURV platform.
4.  **Interact with DANG Tokens:**
    * **Check Balance:** Your DANG token balance will typically be displayed.
    * **Buy/Get Tokens:** Follow the platform's interface to acquire DANG tokens (details depend on the specific implementation, e.g., via a faucet, swap, or purchase).
    * **Transfer Tokens:** Use the transfer feature by entering the recipient's Principal ID or Canister ID and the amount of DANG you wish to send. Confirm the transaction.
    * **Receive Tokens:** Your Principal ID is your address for receiving DANG tokens. Share it with others who want to send you tokens.
5.  **Log Out (If applicable):** Securely log out of your session if the platform provides such an option.

---

This README provides a comprehensive guide for developers and users of the DANG token and DSURV platform. Remember to replace placeholders like `<your-repository-url>` and `<your-project-directory>` with your actual project details.
