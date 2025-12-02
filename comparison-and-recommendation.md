# Comparison and Recommendation of the Three Transaction Submission Methods

0slot supports three different Transaction Submission methods:

- **json-tx**  
  Reference: [**How to start with 0slot?**](https://github.com/0slot-trade/cookbook/blob/main/how-to-start.md)

- **plain-text-tx**  
  Reference: [**New Feature: Simpler, Faster Transaction Submission**](https://github.com/0slot-trade/cookbook/blob/main/simpler-faster.md)

- **binary-tx**  
  Reference: [**Binary-Tx: A Faster Way for Tx Submission**](https://github.com/0slot-trade/cookbook/blob/main/binary-tx.md)

Among these three methods, **binary-tx offers the lowest latency**, and therefore it is recommended that users choose **binary-tx** whenever possible when submitting transactions to 0slot.


## Why Binary-Tx Is Faster

### 1. Base64 Encoding Overhead in json-tx and plain-text-tx
Both **json-tx** and **plain-text-tx** require the transaction data to be encoded into **base64** before transmission.  
This introduces two performance costs:

- **Base64 increases the data size by approximately 30%.**
- The original raw transaction size is already very close to the maximum size of a TCP/IP packet.  
  After base64 encoding, the enlarged data typically exceeds a single packet, causing it to be split into **two packets** during transmission.

Packet splitting leads to:
- Additional network processing
- Additional transmission time  
➡️ Ultimately resulting in **higher submission latency**.

### 2. Binary-Tx Avoids Packet Splitting
With **binary-tx**, the client sends **raw binary transaction bytes** directly.

This means:
- **No base64 encoding is required**
- **No increase in data size**
- **No TCP packet splitting due to encoding**

As a result:
- Network processing overhead is minimized  
- Transmission time is reduced  
- End-to-end latency is significantly improved  

Additionally, binary-tx reduces:
- Client-side encoding time  
- Server-side decoding time  

➡️ Delivering **maximum efficiency** in both processing and network performance.


## Recommendation
For optimal performance and minimal latency when submitting transactions through 0slot,  
**binary-tx is the strongly recommended Transaction Submission method**.

## Support
For additional assistance, our support team is available through these channels:
  * **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)
  * **Twitter/X**: [Follow @0slot\_trade](https://x.com/0slot_trade)
  * **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)
