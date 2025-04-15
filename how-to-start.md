# How to start with 0slot?

## 1. Your TOKEN  
### Your TOKEN is : xxxxxxxx

In the following instructions, `$TOKEN` represents your TOKEN. Please replace it with your actual TOKEN when using.

## 2. About staked_conn  
The usage of the `staked_conn` interface is similar to the RPC interface, primarily providing the `sendTransaction` method, which directly connects to our validator node.  

When calling the `sendTransaction` method of `staked_conn`, please note the following:  
- A maximum of 5 calls per second is allowed.  
- You need to transfer an amount greater than or equal to 0.0001 SOL to any of the following accounts:  
  - Eb2KpSC8uMt9GmzyAEm5Eb1AAAgTjRaXWFjKyFXHZxF3  
  - FCjUJZ1qozm1e8romw216qyfQMaaWKxWsuySnumVCCNe  
  - ENxTEjSQ1YabmUpXAdCgevnHQ9MHdLv8tzFiuiYJqa13  
  - 6rYLG55Q9RpsPGvqdPNJs4z5WTxJVatMB8zV3WJhs5EK  
  - Cix2bHfqPcKcM233mzxbLk14kSggUUiz2A87fJtGivXr  

## 3. Submitting via curl Command  
```bash 
curl -X POST 'http://ny.0slot.trade?api-key=$TOKEN' \
-d '{
    "jsonrpc": "2.0",
    "id": $UUID,
    "method": "sendTransaction",
    "params": [ 
        "<base64_encoded_tx>",
        { "encoding": "base64" }
    ] 
}'
  ```
## Important Notes:
1) Use **http** instead of https! http is faster! You must use http to get faster speeds.  
2) `$UUID` is the unique identifier for this request.  
3) We provide faster servers in different locations for users. Please test and select the most suitable server from the list below, then replace it in the URL above:  
   - **NY**:  ny1.0slot.trade  
   - **Frankfurt**:  de1.0slot.trade  
   - **AMS NL**:  ams1.0slot.trade  

## 4. Error Codes  
- **API Key Expired**
```json
{"id":"1","jsonrpc":"2.0","error":{"code":403,"message":"API key has expired"}}
  ```
- **Non-sendTransaction Method**
```json
{"id":"1","jsonrpc":"2.0","error":{"code":403,"message":"Invalid method"}}
  ```
- **Rate Limit Exceeded**
```json
{"id":"1","jsonrpc":"2.0","error":{"code":419,"message":"Rate limit exceeaded"}}
```

## 5. Code Implementation
Add an instruction to the Transaction (preferably inserted at the beginning):
```javascript
transaction.addInstruction(
    fromPublicKey,
    'Eb2KpSC8uMt9GmzyAEm5Eb1AAAgTjRaXWFjKyFXHZxF3',
    100000
);
```
**Important Note:**  
The RPC service supports keepalive connections with a 65-second timeout. To optimize performance, we recommend sending periodic HTTP requests (of any type) to maintain persistent connections and minimize request latency.

---

For additional assistance with the `staked_conn` interface, our support team is available through these channels:  

- **Discord Support**: [Join our community](https://discord.com/invite/Qd6txfyS)  
- **Twitter/X**: [Follow @0slot_trade](https://x.com/0slot_trade)  
- **Telegram**: [Message @kurt0slot](https://t.me/kurt0slot)  
