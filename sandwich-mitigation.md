# A Simple Method to for Sandwich Mitigation ( Known as Anti-Mev )

Thanks to the new feature recently launched by Jito, you can easily implement Anti-Mev. All you need to do is add a specific flag to the transaction you submit to 0slot.

# How does it work

The principle is as follows: As far as we know, the common practice of MEV is to obtain the target transaction and then add a transaction both before and after the target transaction, and then package and send it to Jito. Once your transaction is flagged, Jito will only accept the Bundle where your transaction is in the first position. If an extremely rare malicious validator performs MEV on your transaction by inserting a transaction before yours and then sending it to Jito, Jito will reject it.

# How to do it

The operation of adding specific flag is very simple. As Jito said:

In your Solana transaction, add any valid Solana public key(It doesn't have to be a real pubKey) that starts with jitodontfront to any of the instructions. For example: jitodontfront111111111111111111111111111111 or jitodontfront111111111111111111111111111123

# Example

Please refer to the following sample transaction and documents:

https://solscan.io/tx/3AVBKpPUuVX1dMSfysrXVCKzqncuj9c4UtyFC52eacEYWEEQGe39aPQQJ7AvZguTmFw6fpPdWxXEjRo9sLad3nyL
find: jitodontfront111111111111111111111111111123

Jito docs about this:
https://github.com/jito-labs/jito-docs/blob/main/docs/source/lowlatencytxnsend.md#sandwich-mitigation

# Note

Note: This is not something you are required to do. Whether or not to use this method is up to you, based on your own needs. 

Thanks to Jito. They have done a great job.
