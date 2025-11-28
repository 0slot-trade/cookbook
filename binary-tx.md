# Binary-Tx: A More Faster Way for Tx Submission

0slot introduces a new, highly efficient transaction submission method called **Binary-Tx**.  
By sending  **raw binary Solana transactions** directly to a 0slot endpoint, transactions can be submitted with significantly lower latency.

---

## How It Works & Why It’s Faster

### **Binary Transaction Format**
Binary-Tx sends **raw binary transaction bytes**, instead of JSON-RPC–style or base64-encoded payloads.  
This eliminates unnecessary both client-side and server-side encoding/decoding, reducing overall processing time.

---

## How to Use

Below is an example **Go-lang** function demonstrating how to send a binary Solana transaction to a 0slot endpoint:

```go
// Sends a raw (binary) Solana transaction to a 0slot endpoint.
// The 0slot endpoint accepts raw binary transactions instead of JSON-RPC style or base64 style payloads.
func sendBinaryTx(ctx context.Context, client *http.Client, zeroslotEndpoint string, tx solana.Transaction) {
    // Marshal the Solana transaction into raw binary format — this is required.
    txBytes, err := tx.MarshalBinary()
    if err != nil {
        fmt.Printf("failed to marshal transaction: %v\n", err)
        return
    }

    // Create a POST request with the binary transaction as the request body.
    req, err := http.NewRequestWithContext(ctx, http.MethodPost, zeroslotEndpoint, bytes.NewBuffer(txBytes))
    if err != nil {
        fmt.Printf("failed to create request: %v\n", err)
        return
    }

    // Optional headers — 0slot does not require special headers.
    req.Header = http.Header{}
    req.Header.Set("User-Agent", "")

    // Execute the HTTP request using the provided http.Client.
    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("failed to send transaction: %v\n", err)
        return
    }
    defer resp.Body.Close()

    // Check for non-successful status codes.
    if resp.StatusCode < 200 || resp.StatusCode >= 300 {
        body, _ := io.ReadAll(resp.Body)
        fmt.Printf("code %d, body %s\n", resp.StatusCode, string(body))
        return
    }

    fmt.Println("transaction submitted successfully")
}
````

## Parameters & Usage Details

### **`zeroslotEndpoint`**

Example:

```
http://de.0slot.trade/txb?api-key=KEY
```

* `KEY` is your **API Key / Token** provided by 0slot.
* `de.0slot.trade` is one of 0slot’s global servers. 0slot provides multiple regional servers — test and choose the fastest one. 
Below are the available 0slot server addresses by region:
- **New York (NY):** `ny.0slot.trade`  
- **Frankfurt (DE):** `de.0slot.trade`  
- **Amsterdam, Netherlands (AMS NL):** `ams.0slot.trade`  
- **Tokyo (JP):** `jp.0slot.trade`  
- **Los Angeles (LA):** `la.0slot.trade`


### **Binary Transaction Payload**

You must call:

```go
tx.MarshalBinary()
```

This produces the raw **binary transaction bytes** required by Binary-Tx.
Simply POST this binary payload to the `zeroslotEndpoint` — this is the core of the Binary-Tx submission method.

### **Response of Binary-Tx**

The response of Binary-Tx contains different status codes and plain-text response bodies.  
Below is a table summarizing each status code and its corresponding meaning.

| Status Code | Message (Plain Text)            | Meaning |
|-------------|----------------------------------|---------|
| **200**     | `ok`                             | Transaction submitted successfully |
| **403**     | `api-key key is null`            | The `api-key` in the request URL is empty; a valid `api-key` must be provided |
| **403**     | `api-key does not exist`         | The provided `api-key` does not exist; please verify or contact support |
| **403**     | `api-key has expired`            | The `api-key` has expired; please verify or contact support |
| **419**     | `rate limit exceeded`            | The request frequency has exceeded the account’s allowed rate limit |
| **500**     | `ok`                             | Transaction submission failed |

### **Other Notes**
**1. Keep-Alive:**
If there are long intervals between calls, you can use the health endpoint to maintain a `keepalived` connection:
`http://de.0slot.trade/health`
 (remember to replace `de.0slot.trade` with your selected server):

**2. Protocols:**
Both **HTTP** and **HTTPS** are supported.

**3. Anti-MEV and Secure Parameters:**
The existing `anti-mev` and `secure` parameters remain valid.

  * **Anti-MEV:** `http://de.0slot.trade/txb?api-key=KEY&anti-mev=true`
  * **Secure:** `http://de.0slot.trade/txb?api-key=KEY&secure=true`



## Important Notes

**Backward Compatibility:** 
**Binary-Tx** is an optional new feature added to 0slot. The existing functionalities are completely unaffected and remain fully supported. Users can choose whichever transaction submission method best fits their needs.

-----

## Support

For additional assistance, our support team is available through these channels:

  * **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)
  * **Twitter/X**: [Follow @0slot\_trade](https://x.com/0slot_trade)
  * **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)
