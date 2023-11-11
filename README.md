# Ultimate EVM Tracing Reference

[![Telegram Chat](https://img.shields.io/badge/Telegram-join_chat-blue.svg)](https://t.me/paradigm_data)

This repo is a collection of trace-related information for easy reference.

A best effort is made to provide accurate information. Please submit corrections to the issue tracker.

## Contents
1. [Tracers](#tracers)
2. [Trace Methods](#trace-methods)
3. [Node Client Support](#node-client-support)
4. [RPC Provider Support](#rpc-provider-support)
5. [Ecosystem Tooling Support](#ecosystem-tooling-support)
6. [Hosted Data Platform Support](#hosted-data-platform-support)
7. [Example Tracer Data](#example-tracer-data)

## Tracers

A tracer gives a detailed view into what happened during a block or transaction.

Each tracer type provides a different set of information. There are two main categories of tracers, parity and geth. Each node client supports these tracer types to varying degrees.

**To see the specific information returned by each tracer, see the schemas and data samples in the [Example Tracer Data](#example-tracer-data) below.**

| tracer | description | parameters |
| --: | --- | --- |
| [`parity calls`<sup>1</sup>](https://openethereum.github.io/JSONRPC-trace-module) | calls in a flat list structure | `[ "trace" ]` |
| [`parity stateDiffs`<sup>2</sup>](https://openethereum.github.io/JSONRPC-trace-module) | all state changes for each tx | `[ "stateDiff" ]` |
| [`parity vmTraces`](https://openethereum.github.io/JSONRPC-trace-module) | opcode-level trace | `[ "vmTrace" ]` |
| [`geth opcodes`](https://geth.ethereum.org/docs/developers/evm-tracing/built-in-tracers#struct-opcode-logger) | opcode-level trace | `{ }` |
| [`geth calls`<sup>1</sup>](https://geth.ethereum.org/docs/developers/evm-tracing/built-in-tracers#call-tracer) | calls in a nested structure | `{ "tracer": "callTracer" }` |
| [`geth preState`<sup>2</sup>](https://geth.ethereum.org/docs/developers/evm-tracing/built-in-tracers#prestate-tracer) | data that was read before each tx | `{ "tracer": "prestateTracer" }` |
| [`geth stateDiffs`<sup>2</sup>](https://geth.ethereum.org/docs/developers/evm-tracing/built-in-tracers#prestate-tracer) | all state changes for each tx | `{ "tracer": "prestateTracer", "diffMode": true }` |
| [`geth 4byte`](https://geth.ethereum.org/docs/developers/evm-tracing/built-in-tracers#4byte-tracer) | 4byte prefixes of function calls | `{ "tracer": "4byteTracer" }` |
| [`geth javascript`<sup>3</sup>](https://geth.ethereum.org/docs/developers/evm-tracing/custom-tracer#custom-javascript-tracing) | custom javascript tracer functions | `{ "tracer": "{ fault: ..., result: ... }" }` |

*<sup>1</sup>: Geth call traces contain nearly identical information to Parity call traces. There are differences such as 1) they include precompile calls, 2) they used a nested schema instead of a list, 3) they do not include block rewards. See [here](https://github.com/blockchain-etl/ethereum-etl/blob/develop/docs/schema.md#differences-between-geth-and-parity-tracescsv) for additional differences.*

*<sup>2</sup>: There are four types of state changes: balances, codes, nonces, and storage. State-related traces include information about all four.*

*<sup>3</sup>: "tracer is interpreted as a JavaScript expression that is expected to evaluate to an object which must expose the result and fault methods. There exist 4 additional methods, namely: setup, step, enter, and exit. enter and exit must be present or omitted together."*

## Trace Methods

RPC methods are used to obtain trace data from RPC endpoints.

Each method applies one or more tracers to a particular scope of data, such as a block, transaction, or call data.

| rpc method                      | description                 | tracers |
| --: | --- | --- |
| `trace_block`                   | basic block trace           | parity calls
| `trace_transaction`             | basic transaction trace     | parity calls
| `trace_replayBlockTransactions` | advanced block trace        | all parity tracers
| `trace_replayTransaction`       | advanced transaction trace  | all parity tracers
| `trace_filter`                  | query a subset of traces    | all parity tracers
| `trace_call`                    | trace custom call_data      | all parity tracers
| `trace_callMany`                | trace sequence of call_data | all parity tracers
| `trace_rawTransaction`          | parity call_data trace      | all parity tracers
| `trace_get`                     | parity indexed trace        | parity calls
| `debug_traceBlock`              | advanced block trace        | all geth tracers
| `debug_traceTransaction`        | basic transaction trace     | all geth tracers
| `debug_traceCall`               | trace custom call_data      | all geth tracers
| `debug_traceBlockByNumber`      | advanced block trace        | all geth tracers
| `debug_traceBlockByHash`        | advanced block trace        | all geth tracers

## Node Client Support

Node clients track the state of the chain and can perform tracing on the chain's history.

Each node client supports a different set of tracers and trace methods.

| rpc method | [geth](https://geth.ethereum.org/docs/developers/evm-tracing/built-in-tracers) | [reth](https://paradigmxyz.github.io/reth/jsonrpc/intro.html) | [erigon](https://github.com/ledgerwatch/erigon/blob/devel/cmd/rpcdaemon/README.md) | [besu](https://besu.hyperledger.org/public-networks/reference/api) | [nethermind](https://docs.nethermind.io/interacting/json-rpc-ns/trace) |
| --: | :-: | :-: | :-: | :-: | :-: |
| `trace_block`                   | ❌ | ✅ | ✅ | ✅ | ✅ |
| `trace_transaction`             | ❌ | ✅ | ✅ | ✅ | ✅ |
| `trace_replayBlockTransactions` | ❌ | ✅ | ✅ | ✅ | ✅ |
| `trace_replayTransaction`       | ❌ | ✅ | ✅ | ❌ | ✅ |
| `trace_filter`                  | ❌ | ✅ | ✅ | ✅ | ✅ |
| `trace_call`                    | ❌ | ✅ | ✅ | ✅ | ✅ |
| `trace_callMany`                | ❌ | ✅ | ✅ | ✅ | ❌ |
| `trace_rawTransaction`          | ❌ | ✅ | ✅ | ✅ | ✅ |
| `trace_get`                     | ❌ | ✅ | ✅ | ✅ | ❌ |
| `debug_traceBlock`              | ✅ | ✅ | ✅ | ✅ | ✅ |
| `debug_traceTransaction`        | ✅ | ✅ | ✅ | ❌ | ✅ |
| `debug_traceCall`               | ✅ | ✅ | ✅ | ❌ | ✅ |
| `debug_traceBlockByNumber`      | ✅ | ✅ | ✅ | ❌ | ✅ |
| `debug_traceBlockByHash`        | ✅ | ✅ | ✅ | ✅ | ✅ |

The set of traces that can be obtained for a chain is determined by the clients that support that chain:

| rpc method | geth | reth | [erigon](https://github.com/ledgerwatch/erigon#system-requirements) | [besu](https://besu.hyperledger.org/public-networks/get-started/connect/mainnet) | [nethermind](https://www.quicknode.com/guides/infrastructure/node-setup/how-to-run-nethermind-node) | geth fork |
| --: | :-: | :-: | :-: | :-: | :-: | :-: |
| ethereum | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| goerli   | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| [arbitrum](https://docs.arbitrum.io/node-running/how-tos/running-a-full-node) | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| [optimism](https://community.optimism.io/docs/developers/bedrock/node-operator-guide/) | ❌ | ✅ | ❌ | ❌ | ❌ | ✅ |
| zora     | ❌ | ✅ | ❌ | ❌ | ❌ | ✅ |
| [base](https://docs.base.org/guides/run-a-base-node/)     | ❌ | ✅ | ❌ | ❌ | ❌ | ✅ |
| [polygon](https://wiki.polygon.technology/docs/category/operate-a-node/)  | ❌ | ❌ | ✅ | ❌ | ❌ | ✅ |
| [gnosis](https://docs.gnosischain.com/node/manual/execution)   | ✅ | ❌ | ✅ | ✅ | ✅ | ❌ |
| [bnb](https://github.com/bnb-chain/bsc)      | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |


## RPC Provider Support

RPC providers create endpoints where customers can access RPC data without having to run their own nodes.

Every node provider supports different tracers and trace methods. 

| rpc method | [infura](https://docs.infura.io/networks/ethereum/json-rpc-methods/trace-methods) | [alchemy](https://docs.alchemy.com/reference/trace-api)<br>([pricing](https://docs.alchemy.com/reference/compute-unit-costs#debug-api)) | [quicknode](https://www.quicknode.com/docs/ethereum)<br>([pricing](https://www.quicknode.com/api-credits)) | [llamanodes](https://llamanodes.notion.site/Ethereum-Request-Method-Pricing-288b72d56067497c88deddef36dbb19e)<br>([pricing](https://llamanodes.notion.site/Ethereum-Request-Method-Pricing-288b72d56067497c88deddef36dbb19e)) |
| --: | :-: | :-: | :-: | :-: |
| `trace_block`                   | ✅ | ✅ | ✅ | ✅ |
| `trace_transaction`             | ✅ | ✅ | ✅ | ✅ |
| `trace_replayBlockTransactions` | ❌ | ✅ | ✅ | ✅ |
| `trace_replayTransaction`       | ❌ | ✅ | ✅ | ✅ |
| `trace_filter`                  | ✅ | ✅ | ✅ | ✅ |
| `trace_call`                    | ✅ | ✅ | ✅ | ✅ |
| `trace_callMany`                | ✅ | ❌ | ✅ | ✅ |
| `trace_rawTransaction`          | ❌ | ✅ | ✅ | ❌ |
| `trace_get`                     | ❌ | ✅ | ❌ | ✅ |
| `debug_traceBlock`              | ❌ | ❌ | ✅ | ❌ |
| `debug_traceTransaction`        | ❌ | ✅ | ✅ | ✅ |
| `debug_traceCall`               | ❌ | ✅ | ✅ | ✅ |
| `debug_traceBlockByNumber`      | ❌ | ✅ | ✅ | ✅ |
| `debug_traceBlockByHash`        | ❌ | ✅ | ✅ | ✅ |

## Ecosystem Tooling Support

Many different tools exist for obtaining and analyzing traces.

Each tool supports a different set of tracers and trace methods. The libraries in the javascript ecosystem generally do not support tracing.

📟 = can use from command line<br>
🐍 = can use as a python library<br>
🦀 = can use as a rust library

| tracer | [cryo](https://github.com/paradigmxyz/cryo)<br> 📟🐍🦀 | [ethereum<br>etl](https://github.com/blockchain-etl/ethereum-etl) 📟 | [ethers.rs](https://github.com/gakonst/ethers-rs)<br>🦀 | [ctc](https://github.com/checkthechain/checkthechain)<br>🐍 | [ape](https://github.com/apeworx/evm-trace)<br>🐍 | [web3py](https://github.com/ethereum/web3.py)<br>🐍 |
| --: | :-: | :-: | :-: | :-: | :-: | :-: |
| `parity calls`      | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `parity stateDiffs` | ✅ | ❌ | ✅ | ✅ | ✅ | ✅ |
| `parity vmTraces`   | ✅ | ❌ | ✅ | ✅ | ✅ | ✅ |
| `geth opcodes`      | ✅ | ❌ | ✅ | ❌ | ✅ | ❌ |
| `geth calls`        | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| `geth preState`     | ✅ | ❌ | ✅ | ✅ | ✅ | ❌ |
| `geth stateDiffs`   | ✅ | ❌ | ✅ | ✅ | ✅ | ❌ |
| `geth 4byte counts` | ✅ | ❌ | ✅ | ❌ | ❌ | ❌ |
| `geth javascript`   | ✅ | ❌ | ✅ | ❌ | ❌ | ❌ |

## Hosted Data Platform Support

Hosted data platforms allow customers to interact with trace data directly without running their own infrastructure.

Most platforms only support call traces. 

| tracer | [Dune](https://dune.com/docs/data-tables/raw/evm/traces/) | [Flipside](https://flipsidecrypto.xyz/) | [Bigquery](https://cloud.google.com/blog/products/data-analytics/ethereum-bigquery-public-dataset-smart-contract-analytics) | [Allium](https://docs.allium.so/real-time-data/raw/traces) |
| --: | :-: | :-: | :-: | :-: |
| `parity calls`      | ✅ | ✅ | ✅ | ✅ |
| `parity stateDiffs` | ❌ | ❌ | ❌ | ❌ |
| `parity vmTraces`   | ❌ | ❌ | ❌ | ❌ |
| `geth opcodes`      | ❌ | ❌ | ❌ | ❌ |
| `geth calls`        | ✅ | ✅ | ✅ | ✅ |
| `geth preState`     | ❌ | ❌ | ❌ | ❌ |
| `geth state diffs`  | ❌ | ❌ | ❌ | ❌ |
| `geth 4byte counts` | ❌ | ❌ | ❌ | ❌ |
| `geth javascript`   | ❌ | ❌ | ❌ | ❌ |

## Example Tracer Data

A 100 block sample of data is provided for each tracer (block range 10,000,000 through 10,000,099)

<table>
<thead>
  <tr>
    <th>tracer</th>
    <th>schema</th>
    <th>data</th>
    <th>collection command</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td align="right"><code>parity calls</code></td>
    <td><details><summary>schema</summary><pre>- action_from: binary<br>- action_to: binary<br>- action_value: string<br>- action_gas: uint32<br>- action_input: binary<br>- action_call_type: string<br>- action_init: binary<br>- action_reward_type: string<br>- action_type: string<br>- result_gas_used: uint32<br>- result_output: binary<br>- result_code: binary<br>- result_address: binary<br>- trace_address: string<br>- subtraces: uint32<br>- transaction_index: uint32<br>- transaction_hash: binary<br>- block_number: uint32<br>- block_hash: binary<br>- error: string<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__traces__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo traces -b 10M:+100</code></td>
  </tr>

  <tr>
    <td align="right"><code>parity stateDiffs balances</code></td>
    <td><details><summary>schema</summary><pre>- transaction_hash: binary<br>- block_number: uint32<br>- address: binary<br>- from_value_string: string<br>- from_value_binary: binary<br>- from_value_f64: float64<br>- to_value_string: string<br>- to_value_binary: binary<br>- to_value_f64: float64<br>- chain_id: uint64<br>- transaction_index: uint32</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__balance_diffs__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo balance_diffs -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>parity stateDiffs codes</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint32<br>- transaction_hash: binary<br>- address: binary<br>- from_value: binary<br>- to_value: binary<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__code_diffs__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo code_diffs -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>parity stateDiffs nonces</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint32<br>- transaction_hash: binary<br>- address: binary<br>- from_value: uint64<br>- to_value: uint64<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__nonce_diffs__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo nonce_diffs -b 10M:+100</code></td>
  </tr>
  
  <tr>
    <td align="right"><code>parity stateDiffs storage</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint32<br>- transaction_hash: binary<br>- address: binary<br>- slot: binary<br>- from_value: binary<br>- to_value: binary<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__storage_diffs__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo storage_diffs -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>parity vmTraces</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint32<br>- pc: uint64<br>- cost: uint64<br>- used: uint64<br>- op: string<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__vm_traces__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo vm_traces -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth opcodes</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_hash: binary<br>- transaction_index: uint32<br>- trace_address: string<br>- depth: uint64<br>- error: string<br>- gas: uint64<br>- gas_cost: uint64<br>- op: string<br>- pc: uint64<br>- refund_counter: uint64<br>- return_data: binary<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__geth_opcodes__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo geth_opcodes -b 10M:+100 \</code><br><code>--include-columns stack storage</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth calls</code></td>
    <td><details><summary>schema</summary><pre>- typ: string<br>- from_address: binary<br>- to_address: binary<br>- value_string: string<br>- value_binary: binary<br>- value_f64: float64<br>- gas_string: string<br>- gas_binary: binary<br>- gas_f64: float64<br>- gas_used_string: string<br>- gas_used_binary: binary<br>- gas_used_f64: float64<br>- input: binary<br>- output: binary<br>- error: string<br>- block_number: uint32<br>- transaction_hash: binary<br>- transaction_index: uint32<br>- trace_address: string<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__geth_calls__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo geth_calls -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth prestate balances</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint32<br>- transaction_hash: binary<br>- address: binary<br>- balance_binary: binary<br>- balance_string: string<br>- balance_f64: float64<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__balance_reads__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo balance_reads -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth prestate codes</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint32<br>- transaction_hash: binary<br>- contract_address: binary<br>- code: binary<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__code_reads__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo code_reads -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth prestate nonces</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint32<br>- transaction_hash: binary<br>- address: binary<br>- nonce: uint64<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__nonce_reads__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo nonce_reads -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth prestate storages</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint32<br>- transaction_hash: binary<br>- contract_address: binary<br>- slot: binary<br>- value: binary<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__storage_reads__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo storage_reads -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth stateDiffs balances</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint64<br>- transaction_hash: binary<br>- address: binary<br>- from_value_f64: float64<br>- from_value_binary: binary<br>- from_value_string: string<br>- to_value_f64: float64<br>- to_value_binary: binary<br>- to_value_string: string<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__geth_balance_diffs__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo geth_balance_diffs -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth stateDiffs codes</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint64<br>- transaction_hash: binary<br>- address: binary<br>- from_value: binary<br>- to_value: binary<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__geth_code_diffs__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo geth_code_diffs -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth stateDiffs nonces</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint64<br>- transaction_hash: binary<br>- address: binary<br>- from_value_f64: float64<br>- from_value_binary: binary<br>- from_value_string: string<br>- to_value_f64: float64<br>- to_value_binary: binary<br>- to_value_string: string<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__geth_nonce_diffs__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo geth_nonce_diffs -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth stateDiffs storages</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint64<br>- transaction_hash: binary<br>- address: binary<br>- slot: binary<br>- from_value: binary<br>- to_value: binary<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__geth_storage_diffs__10000000_to_10000099.parquet">parquet</a></td>
    <td><code>cryo geth_storage_diffs -b 10M:+100</code></td>
  </tr>
  <tr>
    <td align="right"><code>geth 4byte counts</code></td>
    <td><details><summary>schema</summary><pre>- block_number: uint32<br>- transaction_index: uint32<br>- transaction_hash: binary<br>- signature: binary<br>- size: uint64<br>- count: uint64<br>- chain_id: uint64</pre></details></td>
    <td><a href="https://datasets.paradigm.xyz/ultimate_tracing_evm_reference/ethereum__four_byte_counts__18000000_to_18000099.parquet">parquet</a></td>
    <td><code>cryo 4byte_counts -b 10M:+100</code></td>
  </tr>
</tbody>
</table>
