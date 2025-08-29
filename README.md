# SFSailfish
This is the repository for the Artifact Evaluation of CCS'25 "Optimistic, Signature-Free Reliable Broadcast and Its Applications".

For all questions about the artifact, including troubleshooting, please e-mail Qianyu Yu qyu100@connect.hkust-gz.edu.cn

# Artifact Overview
This artifact contains, and allows to reproduce, experiments for all figures included in the paper "Optimistic, Signature-Free Reliable Broadcast and Its Applications".

Branch OptSFSailfish represents Sailfish++ using optimistic RBC under failure-free case.

Branch SFSailfish represents Sailfish++ using Bracha RBC under failure-free case.

Branch SFBullshark represents signature-free SFBullshark under failure-free case.

Branch Sailfish represents valinla Sailfish.

Branch SFSailfishFault represents Sailfish++ under failure case.

Branch SailfishFault represents Sailfish  under failure case.

Branch SFBullsharkFault represents Bullshark  under failure case.

# Testing Locally
The core protocols are written in Rust, but all benchmarking scripts are written in Python and run with [Fabric](https://www.fabfile.org/). To deploy and benchmark a testbed of 10 nodes on your local machine, clone the repo and install the python dependencies:
```bash
$ git clone https://github.com/qyu100/SFSailfish.git
$ git checkout SFSailfish
$ cd benchmark
$ pip install -r requirements.txt
```
You also need to install Clang (required by rocksdb). 
After commenting out lines 12–20 in benchmark/instance.py, run a local benchmark using Fabric:
```bash
$ fab local
```

This command may take a long time the first time you run it (compiling rust code in ```release``` mode may be slow) and you can customize a number of benchmark parameters in ```fabfile.py```. When the benchmark terminates, it displays a summary of the execution similarly to the one below.

```
-----------------------------------------
 SUMMARY:
-----------------------------------------
 + CONFIG:
 Faults: 0 node(s)
 Committee size: 10 node(s)
 Worker(s) per node: 1 worker
 Collocate primary and workers: True
 Execution time: 20 s

 Header size: 512 B
 Max header delay: 1,000 ms
 GC depth: 50 round(s)
 Sync retry delay: 10,000 ms
 Sync retry nodes: 3 node(s)
 batch size: 512 B
 Max batch delay: 200 ms

 + RESULTS:
 Consensus BLPS: 2,680 Block/s
 Consensus TPS: 2,680 tx/s
 Consensus latency: 7 ms
 Consensus leader latency: 3 ms
 Consensus non leader latency: 7 ms
-----------------------------------------
```
