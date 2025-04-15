# Optimizing Network Request Performance Using HTTP Keep-Alive Mechanism

This article details how to leverage **HTTP Keep-Alive** to enhance network performance when using the Solana transaction acceleration service on 0slot.trade, with practical code examples provided.

## Introduction to HTTP Keep-Alive Mechanism

HTTP Keep-Alive is a network optimization mechanism that allows multiple HTTP requests and responses to be sent and received over a single TCP connection. Traditional HTTP requests establish a new TCP connection for each request and close it afterward. This approach is inefficient for frequent requests due to the overhead of repeatedly establishing and closing connections. By keeping the connection open, HTTP Keep-Alive avoids redundant connection setup and teardown processes, significantly improving network request efficiency.

### How HTTP Keep-Alive Works

Below is the basic workflow of the HTTP Keep-Alive mechanism:

- **Without Keep-Alive**:
  1. Client establishes a TCP connection with the server.
  2. Client sends an HTTP request.
  3. Server returns an HTTP response.
  4. TCP connection closes.
  5. Next request requires a new TCP connection.

- **With Keep-Alive**:
  1. Client establishes a TCP connection with the server.
  2. Client sends HTTP Request 1.
  3. Server returns HTTP Response 1.
  4. Connection remains open.
  5. Client sends HTTP Request 2.
  6. Server returns HTTP Response 2.
  7. Connection can be reused until closed.

### Benefits of HTTP Keep-Alive

Using HTTP Keep-Alive offers the following advantages:

1. **Reduced TCP Handshake Overhead**: Eliminates the three-way handshake for every request.
2. **Avoids TCP Slow Start Impact**: Reuses existing connections, bypassing the slow start phase.
3. **Lower System Resource Consumption**: Reduces the number of simultaneous open connections.
4. **Faster Page Load Times**: Particularly effective for webpages requiring multiple resources.

## About **HTTP Keep-Alive** from 0slot.trade

The maximum duration for a 0slot.trade Keep-Alive is 65 seconds. It is recommended to perform an access approximately every 60 seconds.

## Example Code

To demonstrate how to leverage HTTP Keep-Alive in practice, we provide a Python script showing how the Solana transaction acceleration service on 0slot.trade optimizes network requests using HTTP Keep-Alive.

### Code Implementation

```python
import time
import base58
import asyncio
import argparse
from solders.keypair import Keypair
from solders.pubkey import Pubkey
from solders.transaction import Transaction
from solders.message import Message
from solders.system_program import TransferParams, transfer
from solana.rpc.async_api import AsyncClient

"""
This access is unrestricted in content—any request and any response will not affect the Keep-Alive.
For SDKs: You can call the RPC method getHealth, which will return a correct "OK."
For manual HTTP access: You can execute a GET request or omit the apk-key. The advantage is that this access does not count toward TPS calculations.
getHealth refers to the official HTTP RPC method provided by Solana.
https://solana.com/zh/docs/rpc/http/gethealth
python solana sdk is
AsyncClient.is_connected()
golang solana sdk is
rpc.GetHealth()
node.js solana sdk is
rpc.getHealth()
java solana sdk is
client.getApi().getHealth()
rust solana sdk is
rpcClient.get_health()
"""

async def send_solana_transaction(api_key, private_key, tip_key, to_public_key, keep):
    client_for_blockhash = AsyncClient(f"https://api.mainnet-beta.solana.com")
    client_for_send = AsyncClient(f"https://de.0slot.trade?api-key=" + api_key)

    # 获取最新的区块哈希值
    latest_blockhash = await client_for_blockhash.get_latest_blockhash()
    await client_for_blockhash.close()

    # 创建发送方和接收方的密钥对和公钥
    sender = Keypair.from_bytes(base58.b58decode(private_key))
    receiver = Pubkey.from_string(to_public_key)
    tip_receiver = Pubkey.from_string(tip_key)

    # 创建转账指令
    main_transfer_instruction = transfer(TransferParams(from_pubkey=sender.pubkey(), to_pubkey=receiver, lamports=1))
    tip_transfer_instruction = transfer(TransferParams(from_pubkey=sender.pubkey(), to_pubkey=tip_receiver, lamports=100000))

    # 创建消息和交易
    message = Message.new_with_blockhash([main_transfer_instruction, tip_transfer_instruction], payer=sender.pubkey(), blockhash=latest_blockhash.value.blockhash)
    transaction = Transaction.new_unsigned(message)
    transaction.sign([sender], latest_blockhash.value.blockhash)

    try:
        # 如果启用 Keep-Alive，定期检查连接状态
        if keep:
            await client_for_send.is_connected()
        start_time = time.perf_counter()
        result = await client_for_send.send_transaction(transaction)
        end_time = time.perf_counter()
        elapsed_time = end_time - start_time
        print(f"Transaction sent successfully in {elapsed_time:.4f} seconds")
        print("Transaction signature:", result.value)
    except Exception as e:
        print("Error:", str(e))
    finally:
        await client_for_send.close()


async def main():
    parser = argparse.ArgumentParser(description="Send a Solana transaction with speed test")
    parser.add_argument("--api_key", required=True, help="Solana API key for accessing the network.")
    parser.add_argument("--private_key", required=True, help="Sender's private key for signing the transaction.")
    parser.add_argument("--tip_key", required=True, help="Public key of the tip receiver.")
    parser.add_argument("--to_public_key", required=True, help="Public key of the main receiver.")
    args = parser.parse_args()

    # 分别以 keep=False 和 keep=True 的方式发送交易
    await send_solana_transaction(args.api_key, args.private_key, args.tip_key, args.to_public_key, False)
    await send_solana_transaction(args.api_key, args.private_key, args.tip_key, args.to_public_key, True)


if __name__ == "__main__":
    asyncio.run(main())
```

### How to Run the Code

Assuming you have installed the required dependencies (e.g., `solders` and `solana`), run the code using the following command:

```bash
python test_keepalive.py --api_key <your_api_key> --private_key <your_private_key> --tip_key <tip_key> --to_public_key <to_public_key>
```

The results will display the transaction send times with and without Keep-Alive, demonstrating the performance improvement from the Keep-Alive mechanism.

---

We hope this article helps! If you have questions or need further clarification, feel free to reach out.  
Our support team is available through these channels:  

- **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)  
- **Twitter/X**: [Follow @0slot_trade](https://x.com/0slot_trade)  
- **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)
