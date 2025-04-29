# Introduction to 0slot ShredStream

## About 0slot ShredStream
0slot ShredStream is a value-added service provided by 0slot for top traders or platforms. By using 0slot ShredStream, users can access Shred data on Solana with extremely low latency. This service is particularly suitable for scenarios requiring real-time data, such as high-frequency trading, validation operations, and RPC nodes. Feedback from multiple top traders indicates that 0slot ShredStream is faster than other ShredStream services, with an average lead time of 1-5 milliseconds.

## Implementation Principle
0slot operates a number of validator nodes and collaborates with a large number of other validator nodes to collect Shreds from all these nodes. The collected Shreds are analyzed and merged using optimized algorithms, then transmitted efficiently through AWS Direct Connect for global distribution.

## Advantages of 0slot ShredStream
- **Ultra-Low Latency**: Offers an average lead of 1-5 milliseconds compared to other ShredStream services.
- **Stable and Reliable Service**: Proven by real-world usage from numerous top traders.
- **Zero Configuration**: Users only need to provide an IP and port to receive Shreds.

**Note:**
- The transmission speed of ShredStream is related to the current network status, user's server location, and network conditions; there is no absolute fastest;
- Using 0slot ShredStream is generally 20-40ms faster than not using any ShredStream service;
- It is recommended that users, in addition to 0slot ShredStream, also use other services like Jito and bloXroute's ShredStream simultaneously to achieve overall faster speeds;
- According to our users' tests and usage, 0slot ShredStream is on average about 20% faster than those of Jito and bloXroute.

## How to Access and Use 0slot ShredStream
- **Restricted Access**: 0slot ShredStream does not offer open registration. Due to resource limitations and to ensure service quality, it is only available for purchase to users of our on-chain services.
- **Technical Requirements**: Users need to have certain capabilities for integrating and developing with ShredStream.
- **Setup**: Provide 0slot with an IP and port to receive the Shred data stream.

**Prerequisites**

To utilize the processing capabilities of Jito ShredStream Proxy for receiving, parsing, and forwarding Shreds, 0slot ShredStream recommends direct integration with Jito ShredStream Proxy. Users must be familiar with and have successfully deployed Jito ShredStream Proxy. If not, please refer to the following documentation to learn and deploy Jito ShredStream Proxy:  
[Jito Labs - Low Latency Block Updates (Shredstream)](https://docs.jito.wtf/lowlatencytxnfeed/)

### The Details of Using 0slot ShredStream
- Jito ShredStream Proxy takes port **20000** by default on the local machine to receive Shred data sent from the Jito server.
- 0slot Shred data packets are fully compatible with Jito's Shred data packets. Therefore, provide the same port to 0slot, and 0slot's Shreds will automatically be sent to that port.
- Upon receiving Shreds, Jito ShredStream Proxy will forward them unchanged to the **TVU port**, resulting in lower latency for Shred data received at the TVU port.
- Additionally, Jito ShredStream Proxy creates a gRPC service on the local **port 9999** to parse Shred data. Anyone connecting to this port can access the parsed **unprocessed-tx** data. Users can develop various trading applications, such as backrun arbitrage, based on their business needs.
- Parsing Shreds with gRPC:
To parse Shreds and obtain unprocessed transactions (unprocessed-tx) using gRPC, refer to the following code example:  
[Jito Labs - Examples of Deshred](https://github.com/jito-labs/shredstream-proxy/blob/master/examples/deshred.rs)

## How to Test and Compare the Speed of Different ShredStreams
To test the latency of different ShredStreams, it's actually quite simple. On the same IP (to avoid the impact of different networks on the test results), use different ports to receive ShredStreams from different service providers and see which provider's identical Shred arrives first.

There is no need to parse the received Shred data; just hash the Shred to identify the same Shred and record the time it was received. By comparing the arrival times of Shreds with the same hash value, you can determine which provider's ShredStream has faster speed and lower latency.

## Contact Us
For any inquiries, please contact us:
- **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)  
- **Twitter/X**: [Follow @0slot_trade](https://x.com/0slot_trade)  
- **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)  
