# StreamChain Evaluation of BIDL

## General preparation

	-- Run BEFORE you modify cluster configuration in config files
	./reconfigure.sh

	-- Inspect to modify cluster configuration
	vi config.sh

## General throughput benchmark with read_insert workload and no pipelining.

	-- Copies cluster configuration, crypto material, and binaries (including streamchain binaries) to cluster machines
	-- and runs the benchmark
	./run_main.sh

	-- Run if your benchmark run experienced errors or had to be terminated early
	./teardown.sh

	-- Results
	cd logs/processed

## General throughput benchmark with read_insert workload AND pipelining.	
	-- Inspect to modify node configuration
	-- run_pipeline/config_str.sh is what we're interested in. This configures streamchain to use ramdisk in both orderers and peers.
	vi run_pipeline

	-- IMPORTANT: run_pipeline.sh can run with configurable ramdisk utilization. Selectively comment out the last three code blocks
	-- (the ones with run 'a', run 'b', and run 'c'). Make sure only one of the three is active.
	-- Copies cluster configuration, crypto material, and binaries.
	./run_pipeline.sh

	-- If your run experienced errors or had to be terminated early
	./teardown.sh

	-- Results
	cd logs/processed
	
## General throughput benchmark with read_insert workload, pipelining, AND batchwrite optimization

	-- Inspect to modify node configuration
	vi run_batchwrite/config_orderer.sh

	-- Copies cluster configuration, crypto material, and binaries
	./run_main.sh

	-- Run if your benchmark experienced errors or had to be terminated early
	./teardown.sh
	
	-- Results
	cd logs/processed

# Running Benchmarks

## General prerequisites

- **Golang** installed and inside path, `GOPATH` set
- **Docker** installed and current user inside `docker` group in system

## Benchmark Preparations

The different roles on the network have to be defined in `config.sh`. Some of the parameters there are further explained here.

- **add_events** stands for additional event peers next to the main one (separation is done because the main one is doing tasks like instantiating the channel)
- **goexec** has to be set to the parent folder of the go compiler executable in the system of the event machines
- **mode** defines which set of Fabric peer and orderer binaries are used for the benchmark execution. The set with the corresponding name is found in the ./bin folder. In order to run tests with a custom Fabric version, the two binaries have to be put in a subfolder with the name of the mode. Currently this can be one of
  - *fabric*: "Vanilla" Fabric binaries
  - *streamchain*: Fabric with streamchain extension
  - *streamchain_ff*: Fabric with streamchain and block caching optimization of FastFabric

## Running YCSB Benchmarks

All the benchmarks are run by executing `run.sh`. They currently include:

- `run_main.sh`: General throughput benchmark with varying parallel threads and block sizes (optionally with Streamchain)
- `run_pipeline.sh`: Benchmark corresponding to Figure 6 in Streamchain paper, measuring throughput with varying pipeline parallelism in validation phase and RAM disk utilizations
- `run_batchwrite.sh`:  Measurements with different sizes for the batching optimizations for disk writes

The scope of each of these benchmarks has to be defined inside the corresponding benchmark executable (e.g. Streamchain enabled or not, different block sizes and client numbers).

## Running Supply Chain Benchmarks

All of the workloads have to be located in the workloads/supplychain/ folder. Afterwards, they are run by executing `run_all_scmp.sh`. The amount of clients and workloads to be included, can be specified in there as well. Depending on the size of the workload and the system in use (e.g. StreamChain or Fabric), the sleeping times inside `run_main_scmp.sh` have to be adjusted, both for the loading phase and the actual benchmark. As a coarse orientation, the size of the data set that is, e.g., loaded, can be divided by the expected throughput.
