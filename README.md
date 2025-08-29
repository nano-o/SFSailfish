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
Finally, run a local benchmark using fabric:
```bash
$ fab local
```

This command may take a long time the first time you run it (compiling rust code in ```release``` mode may be slow) and you can customize a number of benchmark parameters in ```fabfile.py```. When the benchmark terminates, it displays a summary of the execution similarly to the one below.