# SFSailfish
This is the repository for the Artifact Evaluation of CCS'25 "Optimistic, Signature-Free Reliable Broadcast and Its Applications".

For all questions about the artifact, including troubleshooting, please e-mail Qianyu Yu qyu100@connect.hkust-gz.edu.cn

# Artifact Overview

The artifact consists of a docker image containing code allowing to reproduce the experiments described in the paper "Optimistic, Signature-Free Reliable Broadcast and Its Applications", CCS 2025.

For the DAG consensus experiments, the git repository contains one branch per system:
- Branch OptSFSailfish represents Sailfish++ using optimistic RBC under failure-free case.
- Branch SFSailfish represents Sailfish++ using Bracha RBC under failure-free case.
- Branch SFBullshark represents signature-free SFBullshark under failure-free case.
- Branch Sailfish represents valinla Sailfish.
- Branch SFSailfishFault represents Sailfish++ under failure case.
- Branch SailfishFault represents Sailfish  under failure case.
- Branch SFBullsharkFault represents Bullshark  under failure case.

For convenience, each branch has been checked out in its own directory.
However, the GCP scripts require access to a git repository with this exact branch structure.
The scripts use "https://github.com/qyu100/SFSailfish.git".
If this GitHub repository is not available, you can host the git repository (which you can find in the image under `/home/user/CCS2025-artifact/` at a location of your choice and modify `benchmark/settings.json` accordingly.

Finally, formal specifications and model-checking configuration can be found in the [formal-specifications](./formal-specifications) directory, including a README file containing instructions.

# Detailed instructions for DAG consensus experiments

## Running the Docker image and copying the credentials

To run the docker image:
If using Linux, first unzip the image with `gunzip ccs-25.tar.gz`, then `docker load -i ccs-25.tar`.
Verify the image is loaded using `docker images`.
Finally, run the image with `docker run --rm -it ccs-25`.
 
Next, you will be given 3 files (`sf-dag`, `sf-dag.pub`, and `key.json`) that you need to copy to the image. If you do not have them, please request them from the program chairs. Once you have the 3 files, uploaded them to the running image as follows:


```bash
docker cp sf-dag image_name:/home/user/.ssh/sf-dag
docker cp sf-dag.pub image_name:/home/user/.ssh/sf-dag.pub
docker cp key.json image_name:/home/user/
```

The process should be similar on other platforms that support Docker.

## Testing Locally

The core protocols are written in Rust, but all benchmarking scripts are written in Python and run with [Fabric](https://www.fabfile.org/). To deploy and benchmark a testbed of 10 nodes on your local machine: 
```bash
cd CCS2025-artifact/benchmark
fab local
```

Testing locally should work even if you do not have credentials.

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

## Running Experiments on GCP

For this you need to have copied the credentials into the image as explained before.

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

