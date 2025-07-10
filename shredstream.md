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

### Receiving and Handling Shred Data from 0slot
0slot will automatically sends the shred data stream to the **IP and port** you provided.
When you receive the data, you can parse and process it using the method you are familiar with, then decide how to use it based on your business requirements.

If you have experience developing with **Jito ShredStream**, this will be very straightforward —
**0slot's shred data packets are fully compatible with Jito's shred data packets**.
That means you can handle 0slot’s data using the same approach as with **Jito ShredStream**, which is called **Jito ShredStream Proxy**.

Please refer to the following documentation to learn how to deploy the Jito ShredStream Proxy:
👉 [Jito Labs - Low Latency Block Updates (Shredstream)](https://docs.jito.wtf/lowlatencytxnfeed/)

> **Note:**
> You can also choose alternative methods to receive and process 0slot shredstream data.
> The use of the **Jito ShredStream Proxy** is **not mandatory**, and the approach depends on your development preferences and experience.

## How to Test and Compare the Speed of Different ShredStreams
To test the latency of different ShredStreams, it's actually quite simple. On the same IP (to avoid the impact of different networks on the test results), use different ports to receive ShredStreams from different service providers and see which provider's identical Shred arrives first.

There is no need to parse the received Shred data; just hash the Shred to identify the same Shred and record the time it was received. By comparing the arrival times of Shreds with the same hash value, you can determine which provider's ShredStream has faster speed and lower latency.

## Contact Us
For any inquiries, please contact us:
- **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)  
- **Twitter/X**: [Follow @0slot_trade](https://x.com/0slot_trade)  
- **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)  
