# Mock AA Setup
Build with ERC-4337 Smart accounts, Bundlers, Paymasters, & User Operations

### Account Support
AA supports 5 types of accounts natively (but can easily be extended to support any compatible ERC-4337 account). The below table details which EntryPoints each account is valid for.

| Account       | EntryPoint v0.7 | EntryPoint v0.6 |
|---------------|-----------------|-----------------|
| Safe          | ✅              | ✅              |
| SimpleAccount | ✅              | ✅              |
| Kernel        | ✅              | ✅              |
| Biconomy      | ❌              | ✅              |
| LightAccount  | ❌              | ✅              |

### Run
To start the test environment, run

```bash
docker compose up
```

Once docker has started, the following services can be accessed locally through the following endpoints:

```bash
Anvil at localhost:8545
Alto Bundler at localhost:4337
Mock Paymaster at localhost:3000
```

### Custom Extension
Extension: Testing against forked state

If you want your tests to run against a live blockchain, you can slightly edit the `docker-compose.yaml` file to fork from the latest block by adding the anvil flag `--fork-url` and the environment variable `SKIP_DEPLOYMENTS` to skip the local contract deployments.

Example:
```dockerfile
services:
  anvil:
    image: ghcr.io/foundry-rs/foundry:nightly-f6208d8db68f9acbe4ff8cd76958309efb61ea0b
    ports: [ "8545:8545" ]
+    entrypoint: [ "anvil", "--fork-url", "https://rpc.sepolia.org", "--host", "0.0.0.0", "--block-time", "0.1", "--silent"] 
     platform: linux/amd64/v8
 
  mock-paymaster:
    image: ghcr.io/pimlicolabs/mock-verifying-paymaster:main
    ports: [ "3000:3000" ]
    environment:
      - ALTO_RPC=http://alto:4337
      - ANVIL_RPC=http://anvil:8545
 
  alto:
    image: ghcr.io/pimlicolabs/mock-alto-bundler:main
    ports: [ "4337:4337" ]
    environment:
       - ANVIL_RPC=http://anvil:8545
+      - SKIP_DEPLOYMENTS=true
```