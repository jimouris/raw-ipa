# IPA End-2-End Prototype in MP-SPDZ

This is a prototype of IPA, written in the research MPC system, [MP-SPDZ](https://github.com/data61/MP-SPDZ).


## Installation

To run the prototype, you will need to clone the MP-SPDZ repo so that it neighbors the `raw-ipa` repo.

From the current directory (i.e. `raw-ipa/research-prototype`)

```
cd ../..
gh repo clone data61/MP-SPDZ
cd MP-SPDZ

```

Then, to setup MP-SPDZ, run:

```
make -j 8 tldr
```

On a Mac, this requires brew to be installed, and on Linux it requires other certain packages to be installed. See the [MP-SPDZ README](https://github.com/data61/MP-SPDZ#tldr-source-distribution) for more details.

On a Mac, if your homebrew is installed in a non-traditional location, see [this issue.](https://github.com/data61/MP-SPDZ/pull/628).

This prototype uses the `replicated-ring-party.x` MP-SPDZ virtual machine. To compile, make sure you're still in the `MP-SPDZ` directory. First, we need to compile a 32 bit ring, so we need to add one more config to `CONFIG.mine` by running:

```
echo "MOD = -DRING_SIZE=32" >> CONFIG.mine
```

Then, to compile, run:
```
make clean -B -j 8 replicated-ring-party.x
```

There is a small bug with running this from another directory (see [this issue]()). To patch this, cd into the `raw-ipa/research-prototype` directory and create a symbolic link the generated `libSPDZ.so`:

```
cd ../raw-ipa/research-prototype
ln -s ../../MP-SPDZ/libSPDZ.so libSPDZ.so
```

Finally, you'll need to setup SSL for the parties. From the `raw-ipa/research-prototype` directory, run:

```
../../MP-SPDZ/Scripts/setup-ssl.sh 3
```

## Running the Prototype

Make sure you are now back in this directory, `raw-ipa/research-prototype`.


### Generate random input data

To generate 2^10 random input data points, run:

```
python3 ipainput.py 10
```

If you'd like to generate more, replace 10 with N to generate 2^N data points.

### Compiling the IPA MPC
To run the compiler:

```
../../MP-SPDZ/compile.py -C -R 32 ipae2e
```

There are two options (specific to the IPA prototype) that you can provide as environmental variables: `IPA_VERBOSE` and `IPA_NUMROWS_POWER`. (The MP-SPDZ compile script consumes command line args, hence the need for environmental variables.) An example of providing these for a single compile step:

```
IPA_VERBOSE=True IPA_NUMROWS_POWER=5 ../../MP-SPDZ/compile.py -C -R 32 ipae2e
```

Note that you should also generate random data accordingly.


(If you run into an error for `input sorting`, see [this issue](https://github.com/data61/MP-SPDZ/pull/629). You may need to make two small changes to `MP-SPDZ/Compiler/types.py`.)

### Running the MPC locally

To simulate the MPC locally, run:

```
../../MP-SPDZ/Scripts/ring.sh -R 32 ipae2e
```