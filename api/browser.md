# MetaMask In Browser API

MetaMask injects an EIP-1193 style provider object into the context of websites the user visits.

On desktop, this API is provided to child iframes as well as any parent frames.

On mobile due to current platform constraints, this API is provided only to the top-level frame, and cannot be relied on in iframes.

The EIP-1193 JavaScript object exposes methods to interact with an [Ethereum JSON-RPC API](./rpc.md).

