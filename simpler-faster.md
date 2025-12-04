# New Feature: Simpler, Faster Transaction Submission

0slot is introducing a new **Transaction Submission** method. Building upon the traditional submission method (see [How to start with 0slot?](https://github.com/0slot-trade/cookbook/blob/main/how-to-start.md)). This new feature provides a simpler and faster way to submit transactions.

## Principles & Advantages

  * **Skips CORS Preflight:** Eliminates the latency (often tens of milliseconds) caused by CORS preflight checks.
  * **Plain Text instead of JSON Payload:** Using a plain text payload avoids JSON parsing overhead. Additionally, smaller payloads reduce transfer time and costs.

-----

## How to Use

Below is an example of how to submit a transaction using a `curl` command:

```bash
curl -X POST 'http://de.0slot.trade/txn?api-key=KEY' \
-H "Content-Type: text/plain" \
-d "<base64_transaction_string>"
```

### Configuration Details

**1. API Key:**
Replace `KEY` in the URL with the **API KEY** or **TOKEN** provided by 0slot.

**2. Server Selection:**
In the example, `"de.0slot.trade"` is the server domain. We provide faster servers for different global locations. Please contact our sales or technical support team to obtain the servers that best suits your needs.

**3. Required Header:**
You must specify the unique request header:
`Content-Type: text/plain`

**4. Keep-Alive:**
If there are long intervals between calls, you can use the health endpoint to maintain a `keepalived` connection:
`http://de.0slot.trade/health`
 (remember to replace `de.0slot.trade` with your selected server):


**5. Protocols:**
Both **HTTP** and **HTTPS** are supported, but **HTTP** is slightly faster. 0slot recommends using **HTTP** as the first choice.

**6. Anti-MEV and Secure Parameters:**
The existing `anti-mev` and `secure` parameters remain valid.

  * **Anti-MEV:** `http://de.0slot.trade/txn?api-key=KEY&anti-mev=true`
  * **Secure:** `http://de.0slot.trade/txn?api-key=KEY&secure=true`

### Response of Submitting a Transaction

The response of submitting a transaction contains different status codes and plain-text response bodies.  
Below is a table summarizing each status code and its corresponding meaning.

| Status Code | Message (Plain Text)            | Meaning |
|-------------|----------------------------------|---------|
| **200**     | `ok`                             | Transaction submitted successfully |
| **403**     | `api-key key is null`            | The `api-key` in the request URL is empty; a valid `api-key` must be provided |
| **403**     | `api-key does not exist`         | The provided `api-key` does not exist; please verify or contact support |
| **403**     | `api-key has expired`            | The `api-key` has expired; please verify or contact support |
| **419**     | `rate limit exceeded`            | The request frequency has exceeded the account’s allowed rate limit |
| **500**     | `ok`                             | Transaction submission failed |

-----

## Important Notes

> **Backward Compatibility:** The traditional 0slot submission method (refer to [How to start with 0slot?](https://github.com/0slot-trade/cookbook/blob/main/how-to-start.md)) remains fully effective and is not affected by this upgrade. Users may choose to adopt the new Transaction Submission method based on their specific needs.

-----

## Support

For additional assistance, our support team is available through these channels:

  * **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)
  * **Twitter/X**: [Follow @0slot\_trade](https://x.com/0slot_trade)
  * **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)
