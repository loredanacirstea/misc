# IBC - Evmos - Hermes

Inspired by https://github.com/informalsystems/hermes-ibc-workshop
Using Hermes to relay messages through IBC between 2 Evmos chains.

```
cargo build --release --bin hermes
```

- replace `controibc` with whatever port you need
- assign the relayer address tokens in the evmos chains


```
RELAYER_CONFIG=~/go/src/hermes-ibc-workshop/relayer/config.toml

hermes -c $RELAYER_CONFIG keys restore --mnemonic "practice expose angle volume bread ivory test yellow gun odor giraffe evidence region dismiss nasty define depend animal client game goat naive woman burger" --hd-path "m/44'/60'/0'/0/0" evmos_9000-1

hermes -c $RELAYER_CONFIG keys restore --mnemonic "practice expose angle volume bread ivory test yellow gun odor giraffe evidence region dismiss nasty define depend animal client game goat naive woman burger" --hd-path "m/44'/60'/0'/0/0" evmos_9000-2


hermes -c $RELAYER_CONFIG tx raw create-client evmos_9000-1 evmos_9000-2
hermes -c $RELAYER_CONFIG tx raw create-client evmos_9000-2 evmos_9000-1
hermes -c $RELAYER_CONFIG tx raw update-client evmos_9000-1 07-tendermint-0
hermes -c $RELAYER_CONFIG tx raw update-client evmos_9000-2 07-tendermint-0

hermes -c $RELAYER_CONFIG tx raw conn-init evmos_9000-1 evmos_9000-2 07-tendermint-0 07-tendermint-0
hermes -c $RELAYER_CONFIG tx raw conn-try evmos_9000-2 evmos_9000-1 07-tendermint-0 07-tendermint-0 -s connection-0
hermes -c $RELAYER_CONFIG tx raw conn-ack evmos_9000-1 evmos_9000-2 07-tendermint-0 07-tendermint-0 -d connection-0 -s connection-0
hermes -c $RELAYER_CONFIG tx raw conn-confirm evmos_9000-2 evmos_9000-1 07-tendermint-0 07-tendermint-0 -d connection-0 -s connection-0

hermes -c $RELAYER_CONFIG create channel --port-a controibc --port-b controibc -c connection-0 -o UNORDERED --channel-version controibc-1 evmos_9000-1

hermes -c $RELAYER_CONFIG start



```


## config.toml

```
[global]
log_level = 'trace'

[mode]

[mode.clients]
enabled = true
refresh = true
misbehaviour = true

[mode.connections]
enabled = false

[mode.channels]
enabled = false

[mode.packets]
enabled = true
clear_interval = 100
clear_on_start = true
tx_confirmation = true

[rest]
enabled = true
host = '127.0.0.1'
port = 3000

[telemetry]
enabled = true
host = '127.0.0.1'
port = 3001

[[chains]]
id = 'evmos_9000-1'
rpc_addr = 'http://192.168.0.106:26657'
grpc_addr = 'http://192.168.0.106:9090'
websocket_addr = 'ws://192.168.0.106:26657/websocket'
rpc_timeout = '10s'
account_prefix = 'evmos'
key_name = 'alice_key'
store_prefix = 'ibc'
default_gas = 100000
max_gas = 3000000
gas_price = { price = 0.0001, denom = 'aevmos' }
clock_drift = '15s'
max_block_time = '10s'
trusting_period = '14days'
trust_threshold = { numerator = '1', denominator = '3' }
address_type = { derivation = 'ethermint', proto_type = { pk_type = '/ethermint.crypto.v1.ethsecp256k1.PubKey' } }


[[chains]]
id = 'evmos_9000-2'
rpc_addr = 'http://192.168.0.107:26657'
grpc_addr = 'http://192.168.0.107:9090'
websocket_addr = 'ws://192.168.0.107:26657/websocket'
rpc_timeout = '10s'
account_prefix = 'evmos'
key_name = 'alice_key'
store_prefix = 'ibc'
default_gas = 100000
max_gas = 3000000
gas_price = { price = 0.0001, denom = 'aevmos' }
clock_drift = '15s'
max_block_time = '10s'
trusting_period = '14days'
trust_threshold = { numerator = '1', denominator = '3' }
address_type = { derivation = 'ethermint', proto_type = { pk_type = '/ethermint.crypto.v1.ethsecp256k1.PubKey' } }



```
