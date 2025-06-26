# Anti-MEV Support on 0slot

Among the many Solana transaction acceleration service providers, **0slot offers the best Anti-MEV performance**, earning widespread recognition from traders.

## How to Enable Anti-MEV support on 0slot?

When using 0slot's service to submit a transaction, you can **enable Anti-MEV** simply by adding the parameter `anti-mev=true` to the submission URL.

For example, change your submission endpoint from:

```
https://ny.0slot.trade?api-key=YOUR_API_KEY
```

to:

```
https://ny.0slot.trade?api-key=YOUR_API_KEY&anti-mev=true
```

*(Please replace the domain and `YOUR_API_KEY` with your actual configuration.)*

---

## How It Works?

As a leading Solana transaction acceleration service provider, **0slot operates a large number of validator nodes and handles one of the highest volumes of transaction submissions** on the network. Based on extensive data analysis and proprietary technology, 0slot is able to intelligently identify **malicious validator nodes**.

0slot maintains a dynamic blacklist of malicious validators. When a transaction is submitted using the `anti-mev=true` flag, 0slot checks the current **Leader node** in the Solana cluster. If the leader is identified as malicious, the transaction will **not be sent immediately**. Instead, 0slot waits and forwards the transaction to the **next available non-malicious leader**, effectively avoiding sandwich attacks.

---

## Jito’s Anti-MEV Features

We **strongly recommend enabling Jito’s Anti-MEV features** alongside 0slot for maximum protection.

See Jito's documentation here:

**[Jito's Sandwich Mitigation →](https://github.com/jito-labs/jito-docs/blob/main/docs/source/lowlatencytxnsend.md#sandwich-mitigation)**

---

## About Sandwich Attacks

A **Sandwich Attack** is a common **Maximum Extractable Value (MEV)** strategy used on blockchain networks like Solana. The attacker profits by manipulating the order of transactions.

Here's how it works:

1. The attacker monitors pending transactions, e.g., a user buying a token.
2. They place a **front-running** transaction to buy the token at a lower price, causing the price to increase.
3. After the user’s transaction is executed at the now higher price, the attacker **back-runs** by selling the tokens for a profit.

This harms users by **forcing them to pay higher prices**, while the attacker captures the price difference.

On Solana, traditional sandwich attacks are more difficult due to its **lack of a public mempool**. However, malicious validators can still carry out such attacks by modifying their client software or leveraging tools like Jito to create private transaction pools, allowing them to reorder transactions.

## The "secure" Option on 0slot
0slot also provides an additional parameter named "secure" to further enhance Anti-MEV protection. For more details, please contact customer support.
Our support team is available through these channels:  

- **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)  
- **Twitter/X**: [Follow @0slot_trade](https://x.com/0slot_trade)  
- **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)  
