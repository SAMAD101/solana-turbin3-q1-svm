## prereq

***Based on your review of SolanaHowitWorks and any other documentation, describe what is happening in the area you plan to focus on- this can be something very specific- please avoid being too high level.***

RPCs are the gateways to the network. I learned about "Stake-Weighted Quality of Service" (SWQoS). It serves as a mechanism in the SVM that directs the transaction messages, introduced in early 2024. It enables the leader node in to prioritize transaction messages that are proxied through other staked validators. The validators with a higher stake are granted a proportionally higher capacity to transmit transaction message packets to the leader. This approach also counters possibility of Sybil attacks from non-staked nodes across the network. 80% of the leader's capacity is reserved for SWQoS, and the remaining 20% for non-staked nodes. This design also enables high-traffic application to vertically integrate their operations by running their own staked-nodes, which can ensure privileged access to the leader enhancing their transaction processing capabilities.

***Go to the Anza Agave Client Repository - copy 20-30 lines of code from the area you are focusing on - annotate the code by noting:*** 
1. ***What is the rust concept***
2. ***What is it doing?*** 
3. ***How can it be made better?***
```rust
#[derive(Debug, Clone)]
pub struct JsonRpcConfig {
    pub enable_rpc_transaction_history: bool,
    pub enable_extended_tx_metadata_storage: bool,
    pub faucet_addr: Option<SocketAddr>,
    pub health_check_slot_distance: u64,
    pub skip_preflight_health_check: bool,
    pub rpc_bigtable_config: Option<RpcBigtableConfig>,
    pub max_multiple_accounts: Option<usize>,
    pub account_indexes: AccountSecondaryIndexes,
    pub rpc_threads: usize,
    pub rpc_blocking_threads: usize,
    pub rpc_niceness_adj: i8,
    pub full_api: bool,
    pub rpc_scan_and_fix_roots: bool,
    pub max_request_body_size: Option<usize>,
    /// Disable the health check, used for tests and TestValidator
    pub disable_health_check: bool,
}

impl Default for JsonRpcConfig {
    fn default() -> Self {
        Self {
            enable_rpc_transaction_history: Default::default(),
            enable_extended_tx_metadata_storage: Default::default(),
            faucet_addr: Option::default(),
            health_check_slot_distance: Default::default(),
            skip_preflight_health_check: bool::default(),
            rpc_bigtable_config: Option::default(),
            max_multiple_accounts: Option::default(),
            account_indexes: AccountSecondaryIndexes::default(),
            rpc_threads: 1,
            rpc_blocking_threads: 1,
            rpc_niceness_adj: Default::default(),
            full_api: Default::default(),
            rpc_scan_and_fix_roots: Default::default(),
            max_request_body_size: Option::default(),
            disable_health_check: Default::default(),
        }
    }
}

impl JsonRpcConfig {
    pub fn default_for_test() -> Self {
        Self {
            full_api: true,
            disable_health_check: true,
            ..Self::default()
        }
    }
}
```

This code is from 'rpc/rpc.rs` file in Anza Agave client.

Rust Concept:
Struct definition with derive macros. Rust provides #[derive] to automatically and conveniently implement traits. Which is used here to implement 'Debug' and 'Clone' traits for the 'JsonRpcConfig' struct.
'Default' trait implementation for 'JsonRpcConfig' struct. It provides a way to create instances of the struct with default values.
There is also a custom constructor implemented (default_for_test) which could be used for tests.

What it does:
This part of code defines a struct for centralizing RPC configuration for the node. Also does special configuration for testing: enable full api access, and disable health checks.

Potential improvements:
Some more builder pattern implementation for creating instances of the 'JsonRpcConfig' struct with some different settings.
```rust
impl JsonRpcConfig {
    pub fn builder() -> JsonRpcConfigBuilder {
        JsonRpcConfigBuilder::default()
    }
}

pub struct JsonRpcConfigBuilder {
    config: JsonRpcConfig;
}

impl JsonRpcConfigBuilder {
    pub fn with_threads(mut self, threads: usize) -> Self {
        self.config.rpc_threads = threads;
        self
    }
    
    pub fn build(self) -> JsonRpcConfig {
        self.config
    }
}
```
This is just an exmaple, some builder functions like these could potentially provide a more flexible way to create instances of 'JsonRpcConfig' struct.

***Are you familiar with various benchmark testing practices? If so, what have you done? If not, do some research and note what you want to learn more about.***

I'm not very much familiar with benchmark testing practices. So far, I've only read some benchmark reports. And unfortunately my PC is not even powerful enough to compile the whole agave client software (cargo build), let alone run the benchmarks, I'll figure something out about that problem. As for my goals for leaning, I want to explore part of benchmark tests that tests the RPC and gossip part of the SVM.

***Are you currently working on a project for which you plan to use what you are doing in this program (this is a positive) - if so, please describe.***

The capstone project that I built in the builder's cohort, HiveMind, a decentralized voting platform for validating ideas, which I plan to continue to work on. Learning more about the SVM internals, I might be able to make it optimize its design.