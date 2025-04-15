# Using Durable Nonce to Send the Same Transaction to Multiple Landing Services

In the Solana blockchain, Durable Nonce is a powerful tool that allows creating and signing transactions that can be submitted at any time in the future. Unlike regular blockhash-based transactions, Durable Nonce transactions do not expire due to blockhash obsolescence, making them particularly suitable for scenarios requiring offline signing or delayed submission. This article details how to use Durable Nonce to send the same transaction to multiple Landing Services and explains the benefits of this approach.

## 1. How Durable Nonce Works

In Solana, regular transactions use Recent Blockhash to prevent double-spending. However, Recent Blockhash is only valid for the last 150 blocks (approximately 80-90 seconds), which limits transaction submission time. Durable Nonce addresses this by introducing a special account (Nonce Account) that stores a unique Nonce value, replacing Recent Blockhash to ensure each transaction's uniqueness.

A key feature of Durable Nonce transactions is that every transaction must include the `nonceAdvance` instruction as its first instruction. This instruction updates the Nonce value in the Nonce Account, ensuring each transaction uses a unique Nonce. Even if a transaction fails, the Nonce is advanced to prevent reuse.

## 2. Benefits of Using Durable Nonce with Multiple Landing Services

Sending the same transaction to multiple Landing Services primarily increases overall transaction success rates. Practical scenarios may include:

- **Network latency or instability**: Some Landing Services may fail to submit transactions promptly due to network issues.
- **Fee-related issues**: Transactions might be rejected by some Landing Services due to insufficient fees.
- **Transaction congestion**: High traffic may cause delays in processing transactions at certain Landing Services.

By sending the same transaction to multiple Landing Services, you significantly improve the likelihood of successful submission. The persistence and uniqueness of Durable Nonce ensure that even if some Landing Services fail, others can still process the transaction successfully.

## 3. Implementation Steps

Below are the steps to send the same transaction to multiple Landing Services using Durable Nonce:

### 3.1 Create a Nonce Account

First, create a Nonce Account and fund it sufficiently to meet rent exemption requirements.

#### CLI Steps to Create a Solana Nonce Account
##### Generate Nonce Account Keypair
```bash
solana-keygen new -o nonce-account.json
```
**Explanation**:  
  - This command generates a new keypair and saves it to `nonce-account.json`.
  - The keypair includes a public/private key pair identifying the Nonce Account.

##### Create Nonce Account Using Sender's Account
```bash
solana -k sender.json create-nonce-account nonce-account.json 0.0015
```
**Parameter Breakdown**:  
  - `-k sender.json`: Specifies the sender's keypair file paying the transaction fee.
  - `nonce-account.json`: The Nonce Account keypair file generated earlier.
  - `0.0015`: Initial SOL balance allocated to the Nonce Account (in SOL).

**Function**:  
  - This command deducts the specified SOL from the sender's account to create and initialize the Nonce Account.
  - The Nonce Account stores an on-chain Durable Nonce, enabling offline transactions.

### 3.2 Full Python Example Using Durable Nonce

Next, we demonstrate through Python code how to construct and submit the same transaction to different Landing Services (de.0slot.trade and ny.0slot.trade) using the same Nonce Instruction and the pre-created Nonce Account.

We first present the complete code, which can be saved as a .py file and executed. Key sections are explained afterward.

#### 3.2.1 Complete Code

```python
import base58
import asyncio
import argparse
from solders.hash import Hash
from solders.pubkey import Pubkey
from solders.keypair import Keypair
from solders.message import Message
from solders.transaction import Transaction
from solders.system_program import TransferParams, transfer
from solana.rpc.async_api import AsyncClient

async def send_solana_transaction(api_key, private_key, nonce_public_key, to_public_key):
    # Initialize clients for different regions
    client_for_blockhash = AsyncClient(f"https://api.mainnet-beta.solana.com")
    de_sender = AsyncClient(f"https://de.0slot.trade?api-key=" + api_key)  # Germany endpoint
    ny_sender = AsyncClient(f"https://ny.0slot.trade?api-key=" + api_key)  # New York endpoint

    # Create keypair from private key
    sender = Keypair.from_bytes(base58.b58decode(private_key))

    # Main recipient address
    receiver = Pubkey.from_string(to_public_key)
    # Our TIP receiving addresses - using different addresses for DE and NY to simulate multiple Landing Services
    de_tip_receiver = Pubkey.from_string("6fQaVhYZA4w3MBSXjJ81Vf6W1EDYeUPXpgVQ6UQyU1Av")
    ny_tip_receiver = Pubkey.from_string("4HiwLEP2Bzqj3hM2ENxJuzhcPCdsafwiet3oGkMkuQY4")
    # Nonce account public key created by sender - must be associated
    # solana-keygen new -o nonce-account.json
    # solana -k sender.json create-nonce-account nonce-account.json 0.0015
    nonce_account_pubkey = Pubkey.from_string(nonce_public_key)

    # Get nonce account info to extract the current nonce value
    get_account_resp = await client_for_blockhash.get_account_info(nonce_account_pubkey)
    await client_for_blockhash.close()

    # The nonce is stored in the account data at bytes 40-72
    # This matches the Rust NonceState structure layout:
    # struct NonceState {
    #     version: u32,          // 4 bytes (offset 0-3)
    #     state: u32,            // 4 bytes (offset 4-7)
    #     authorized_pubkey: Pubkey,  // 32 bytes (offset 8-39)
    #     nonce: Pubkey,         // 32 bytes (offset 40-71) ← we need this
    #     fee_calculator: FeeCalculator,  // 8 bytes (offset 72-79)
    # }
    nonce = get_account_resp.value.data[40:72]
    nonce_hash = Hash.from_bytes(nonce)

    # Create transaction instructions for Germany endpoint
    # Includes:
    # 1. Main transfer (1 lamport)
    # 2. Tip transfer (100,000 lamports = 0.0001 SOL)
    de_instructions = [
        transfer(
            TransferParams(
                from_pubkey=sender.pubkey(),
                to_pubkey=receiver,
                lamports=1
            )
        ),
        transfer(
            TransferParams(
                from_pubkey=sender.pubkey(),
                to_pubkey=de_tip_receiver,
                lamports=100000
            )
        )
    ]
    
    # Create transaction instructions for New York endpoint (same structure)
    ny_instructions = [
        transfer(
            TransferParams(
                from_pubkey=sender.pubkey(),
                to_pubkey=receiver,
                lamports=1
            )
        ),
        transfer(
            TransferParams(
                from_pubkey=sender.pubkey(),
                to_pubkey=ny_tip_receiver,
                lamports=100000
            )
        )
    ]
    
    # Create messages with nonce for each transaction
    de_message = Message.new_with_nonce(
        de_instructions,
        payer=sender.pubkey(),
        nonce_account_pubkey=nonce_account_pubkey,
        nonce_authority_pubkey=sender.pubkey(),
    )
    ny_message = Message.new_with_nonce(
        ny_instructions,
        payer=sender.pubkey(),
        nonce_account_pubkey=nonce_account_pubkey,
        nonce_authority_pubkey=sender.pubkey(),
    )

    # Create unsigned transactions
    de_transaction = Transaction.new_unsigned(de_message)
    ny_transaction = Transaction.new_unsigned(ny_message)

    # Sign transactions with the same nonce
    de_transaction.sign([sender], nonce_hash)
    ny_transaction.sign([sender], nonce_hash)

    try:
        # Send both transactions concurrently using asyncio
        de_task = de_sender.send_transaction(de_transaction)
        ny_task = ny_sender.send_transaction(ny_transaction)
        results = await asyncio.gather(
            de_task,
            ny_task,
            return_exceptions=True  # Don't fail if one transaction fails
        )
        
        # Process results
        for i, result in enumerate(results):
            if isinstance(result, Exception):
                print(f"Error sending to {'DE' if i == 0 else 'NY'}: {str(result)}")
            else:
                print(f"Transaction signature ({'DE' if i == 0 else 'NY'}):", result.value)
    except Exception as e:
        print("Error:", str(e))
    finally:
        # Clean up connections
        await de_sender.close()
        await ny_sender.close()

async def main():
    # Set up command line argument parser
    parser = argparse.ArgumentParser(description="Send a Solana transaction")
    parser.add_argument("--api_key", required=True, help="Solana API key for accessing the network.")
    parser.add_argument("--private_key", required=True, help="Sender's private key for signing the transaction.")
    parser.add_argument("--nonce_public_key", required=True, help="Sender's nonce account public key")
    parser.add_argument("--to_public_key", required=True, help="Public key of the main receiver.")

    args = parser.parse_args()

    await send_solana_transaction(
        args.api_key,
        args.private_key,
        args.nonce_public_key,
        args.to_public_key,
    )

if __name__ == "__main__":
    asyncio.run(main())

```

#### 3.2.2 Creating Transaction Messages for Different Landing Services

In the complete code above, the key section for constructing transaction messages using Nonce Instruction for de.0slot.trade (Germany endpoint) is:

```python
# Create transaction instructions for Germany endpoint
    # Includes:
    # 1. Main transfer (1 lamport)
    # 2. Tip transfer (100,000 lamports = 0.0001 SOL)
    de_instructions = [
        transfer(
            TransferParams(
                from_pubkey=sender.pubkey(),
                to_pubkey=receiver,
                lamports=1
            )
        ),
        transfer(
            TransferParams(
                from_pubkey=sender.pubkey(),
                to_pubkey=de_tip_receiver,
                lamports=100000
            )
        )
    ]
    # Create messages with nonce for each transaction
    de_message = Message.new_with_nonce(
        de_instructions,
        payer=sender.pubkey(),
        nonce_account_pubkey=nonce_account_pubkey,
        nonce_authority_pubkey=sender.pubkey(),
    )
```
The code for ny.0slot.trade (New York endpoint) follows the same structure.

#### 3.2.3 Submitting All Transactions Concurrently

```python
# Send both transactions concurrently using asyncio
        de_task = de_sender.send_transaction(de_transaction)
        ny_task = ny_sender.send_transaction(ny_transaction)
        results = await asyncio.gather(
            de_task,
            ny_task,
            return_exceptions=True  # Don't fail if one transaction fails
        )
```

## 4. Summary

By leveraging Solana's Durable Nonce, we can create and sign transactions that remain valid for future submission. Sending the same transaction to multiple Landing Services significantly improves overall success rates, particularly under network instability or congestion. The persistence and uniqueness of Durable Nonce ensure that even if some Landing Services fail, others can still process the transaction successfully.

## References

- [Solana Durable Nonces - Official Docs](https://solana.com/developers/guides/advanced/introduction-to-durable-nonces)
- [Durable & Offline Transaction Signing using Nonces - GitHub](https://github.com/0xproflupin/solana-durable-nonces)

---

We hope this article helps! For questions or further clarification, contact us via:  

- **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)  
- **Twitter/X**: [Follow @0slot_trade](https://x.com/0slot_trade)  
- **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)
```
