# Public configuration files

This README provides a comprehensive overview of the configuration files required for the functioning of rpc system.

## Table of Contents
- [Chain Settings Configuration](#chain-settings-configuration)
    - [Protocols](#protocols)
    - [Chain Details](#chain-details)
    - [Settings](#settings)
- [Compatible clients list](#clients-compatibility-list)
    - [Blacklisting](#blacklisting)
    - [Whitelisting](#whitelisting)

# Chain Settings Configuration

The first of these files is `chains.yaml`, which specifies the settings for different blockchain protocols and their respective chains.

## Protocols
Each protocol section defines settings for a specific blockchain protocol, including protocol-specific parameters and a list of chains associated with that protocol.

### Example Structure
```yaml
protocols:
  - id: protocol1
    label: Protocol 1
    type: type1
    settings:
      parameter1: value1
      parameter2: value2
    chains:
      - id: mainnet
        priority: 100
        chain-id: chain-id1
        short-names: [shortname1, shortname2]
        code: CODE1
        grpcId: 1
      - id: testnet
        priority: 1
        chain-id: chain-id2
        short-names: [shortname3]
        code: CODE2
        grpcId: 2
  - id: protocol2
    label: Protocol 2
    type: type2
    settings:
      parameter3: value3
      parameter4: value4
    chains:
      - id: mainnet
        priority: 50
        chain-id: chain-id3
        short-names: [shortname4]
        code: CODE3
        grpcId: 3
```

### Field Descriptions

| Field                 | Description                                      |
|-----------------------|--------------------------------------------------|
| `id`                  | Identifier for the protocol.                     |
| `label`               | Human-readable name for the protocol.            |
| `type`                | Type of the blockchain protocol.                 |
| `settings`            | Specific settings for the protocol.              |
| `chains`              | List of chains associated with the protocol.     |

## Chain Details
Each chain under a protocol defines specific parameters and settings unique to that chain. These settings ensure that each chain operates correctly within the context of its protocol. As ususal chains are mainnet and all testnets of that protocol.

### Example Structure
```yaml
chains:
  - id: chain-mainnet
    priority: 100
    chain-id: chain-id1
    short-names: [shortname1, shortname2]
    code: CODE1
    grpcId: 1
  - id: chain-testnet
    priority: 1
    chain-id: chain-id2
    short-names: [shortname3]
    code: CODE2
    grpcId: 2
```

### Field Descriptions

| Field                   | Description                                                    |
|-------------------------|----------------------------------------------------------------|
| `id`                    | Identifier for the chain.                                      |
| `priority`              | Priority of the chain. Used in UIs.                            |
| `chain-id`              | Identifier for the chain. Different types in different chains. |
| `short-names`           | List of short names for the chain.                             |
| `code`                  | Code used to identify the chain.                               |
| `grpcId`                | gRPC identifier for the chain from api module                  |
| `settings`              | Specific settings for the chain.                            |


## Settings
The settings section contains parameters that apply to specific chain. Global parameters located at top level as default, then more specific parameters defined in protocol section and last one could be specified on each chain separatly. Chain settings overrides protocol settings, protocol settings overrides default settings.

```yaml
settings:
    expected-block-time: 1s
    options:
        validate-peers: false
    lags:
        syncing: 40
        lagging: 20
```

### Field Descriptions

| Field                        | Description                                                       |
|------------------------------|-------------------------------------------------------------------|
| `expected-block-time`        | Default expected time for block generation.                       |
| `lags.syncing`               | Number of blocks considered for syncing.                          |
| `lags.lagging`               | Number of blocks considered for lagging.                          |
| `options.validate-peers`     | Enable validation of peers for chains                             |
| `options.validate-syncing`   | Enable validation of syncing state                                |
| `options.disable-validation` | Disable all validations                                           |

# Clients compatibility list

File `compatible-clients.yaml` describes compatible clietns of blockchains for drpc. It contans of list of clients, optional list of chains and enumaration of allowed or disallowed versions of clients.

Each chain could accept or discard new clients and it should be specified in chains.yaml (tbd)

Example:
```yaml
rules:
  - client: erigon
    networks:
      - ethereum
    blacklist: 
      - v2.40.0
```

## Blacklisting

The first, default mode is blacklisting. In case client have non comaptible versions, it should be specified as blacklist:
```yaml
rules:
  - client: client_with_blacklist
    blacklist: 
      - v1
      - v2
      - v4
```

This means that all clients except v1, v2, and v4 are allowed for usage.

## Whitelisting

The second mode is whitelisting. In case there are specified white list - all other versions become gray. Gray versions means it could be used in tests but not in production requests. Therefore all new versions adds to graylist and could be manually added to white or black list. 

```yaml
rules:
  - client: client_with_whitelist
    whitelist: 
      - v3
    blacklist:
      - v1
      - v2
```
