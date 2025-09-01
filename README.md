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
After commenting out lines 12–20 in ```benchmark/instance.py```, run a local benchmark using Fabric:
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

# Running Experiments on GCP
The GCP configuration is specified in ```benchmark/settings.json```.
Please fill in the name and path of the SSH key used to connect to the GCP machines: 
```  
"key": {
        "name": "",
        "path": ""
    },
```
For example:
```  
"key": {
        "name": "sf-dag",
        "path": "/home/user/.ssh/sf-dag"
    },
```

You also need to place the GCP Service Account Key JSON file (downloaded from the Google Cloud Console) somewhere on your filesystem and indicate its location in ```benchmark/benchmark/instance.py``` line 13.
For example:
```
GCP_KEY_PATH = '../benchmark/benchmark/key.json'

```

Finally, you also need to indicate the location of your SSH public key in ```benchmark/benchmark/instance.py``` line 14.
For example:
```
SSH_PUB_KEY_PATH = '/home/user/.ssh/sf-dag.pub'
```

Then you can run 
```bash
$ fab create --nodes=2
```
This will create 10 instances in total: 2 for each of the 5 regions defined in ```settings.json```.
If you change this number, you must also change `'nodes': 10` in fabfile.py, line 102.

Then run: 
```bash
$ fab install
```
to install Rust and clone the repository on all created instances (may take 10 minutes or more).

Finally, run:
```bash
$ fab remote
```
to run and collect the experiment results from the remote instances.
You can find the results in the ```results``` directory.

You can adjust the parameters in fabfile.py to explore different settings.

	•	Set nodes = 50 to run with 50 nodes.

	•	Set header_size = 128_000 to use a header size (i.e. block size) of 128 KB.

In OptSFSailfish, you also need to configure the parameter ```f_num``` based on the selected value of ```nodes``` (so that ```3*f_num < n```, e.g. ```3``` for ```n=10```).

For n=10 and n=25, by tuning ```header_size``` over [128_000, 512_000, 1024_000, 1500_000, 2048_000, 3072_000, 4096_000, 5120_000], you will get the figures.

For n= 50, by tuning ```header_size``` over [128_000, 256_000, 512_000, 768_000, 1024_000, 1536_000, 2048_000, 3072_000, 4096_000], you will get the figures.

After completing all experiments, run:
```bash
$ fab destroy
```
to shut down all the machines.

# Formal Specifications

See the [formal-specifications](./formal-specifications) directory.
