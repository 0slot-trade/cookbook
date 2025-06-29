# Anti-MEV on 0slot

Among the many Solana transaction acceleration service providers,
**0slot stands out as the best**, minimizing the probability of transactions being targeted by sandwich attacks.

## How to Enable Anti-MEV on 0slot?

When submitting transactions through 0slot, to **maximize protection against sandwich attacks**, we strongly recommend taking the following two steps:

### 1. **Use Jito's New Feature: Sandwich Mitigation**

> In your Solana transaction, add any valid Solana public key that starts with `jitodontfront` to any of the instructions.
> For example:
> `jitodontfront111111111111111111111111111111`
> or
> `jitodontfront111111111111111111111111111123`

> Any bundle containing a transaction with the `jitodontfront` account will be **rejected by the block engine unless that transaction appears first (at index 0)** in the bundle.
> Transactions and bundles that do not contain this account are not impacted by this change.

For details, see Jito’s official documentation:
**[Jito's Sandwich Mitigation →](https://github.com/jito-labs/jito-docs/blob/main/docs/source/lowlatencytxnsend.md#sandwich-mitigation)**

 💡 Note: Using Jito’s Sandwich Mitigation feature **does not affect** your transaction's landing speed.

### 2. **Use the `anti-mev` Parameter When Submitting**

When submitting a transaction, you can **enable Anti-MEV** simply by adding the parameter `anti-mev=true` to your submission URL.

For example, change your endpoint from:

```
https://ny.0slot.trade?api-key=YOUR_API_KEY
```

to:

```
https://ny.0slot.trade?api-key=YOUR_API_KEY&anti-mev=true
```

#### Notes:

* Be sure to use your actual API key and the correct domain.
* The value of `anti-mev` **must be `true`** — values such as `"1"` or `"yes"` are **not valid**.
* In rare cases, this method may detect a **malicious leader** and skip them, slightly delaying the transaction.

By performing both of the above steps on 0slot, you can achieve **the best industry-level protection** against sandwich attacks.
However, 0slot **does not guarantee 100% immunity**, and therefore **does not offer compensation for sandwich losses**.

We also recommend:

* Setting **low slippage** limits
* Ensuring information security
  To further reduce your exposure to such risks.

---

## How It Works?

In simple terms, a **Sandwich Attack** is a tactic where an attacker deliberately “sandwiches” a user's normal transaction.

Here’s how it works:

1. The attacker first places a **front-run transaction** — buying before the user.
2. Then comes the user's transaction.
3. Finally, the attacker places a **back-run transaction** — selling after the user’s transaction completes.

This allows the attacker to **buy low and sell high**, profiting from the price movement caused by the user’s order.
Meanwhile, the user ends up buying at a higher price or selling at a lower price, resulting in a **financial loss**.

### There Are Two Common Ways to Perform a Sandwich Attack:

1. **Basic Method: Using a Jito Bundle**

   * The attacker wraps three transactions (front-run, user’s, back-run) into a **Jito Bundle**, and submits it to a Solana validator.
   * This guarantees precise transaction order, making the attack **stable and highly effective**.

2. **Advanced Method: Using Normal Transactions to Front- and Back-Run**

   * Instead of using bundles, the attacker watches the mempool and quickly sends two **regular transactions** before and after the user’s.
   * This method is more complex and depends on **network speed and fee settings**, but it’s more flexible and doesn’t rely on Jito.

## Why 0slot’s Anti-MEV Protection Works

0slot's protection mechanisms are based on these two insights:

1. **Jito's Sandwich Mitigation** helps defend against attacks that use Jito Bundles.

> Any bundle containing a transaction with the `jitodontfront` account will be rejected by the block engine unless it is the first transaction in the bundle.

 This means that Jito only accepts bundle containing a transaction with the `jitodontfront` account **if it is the first transaction**, ensuring it can **never be front-run** and **never be sandwiched**.

2. **The `anti-mev=true` parameter** helps defend against attacks using standard transaction ordering.
   As a leading Solana transaction accelerator, **0slot runs a large number of validators** and handles **one of the highest transaction volumes** in the Solana ecosystem.

Thanks to extensive data analysis and proprietary tech, 0slot can **intelligently detect malicious validators**.

* 0slot maintains a **real-time blacklist** of known malicious leaders.
* When `anti-mev=true` is enabled, 0slot checks the current Solana **leader**.
* If the leader is found to be malicious, the transaction is **withheld** and then **re-sent to the next safe leader**.
* This **effectively avoids sandwich attacks** without relying on third-party tools.

---

## Even More

We also offer **even more secure submission methods**,
providing maximum protection — but note that this may slightly reduce transaction speed and success rate.

If you require such advanced protection,
please **contact the 0slot team** directly.

- **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)  
- **Twitter/X**: [Follow @0slot_trade](https://x.com/0slot_trade)  
- **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)  
