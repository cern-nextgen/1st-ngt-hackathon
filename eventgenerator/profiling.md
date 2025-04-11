---
title: Profiling
layout: main
section: eventgenerators 
---

# Finding bottlenecks in the code
## Profiling and `perf`
During the development of the hardware-accelerated version of MadGraph, the choice of the parts to be changed, in order to be better parallelized was taken on the basis of code profiling.
There are several profiling tools, used to find bottlenecks in the code.
One of the most famous is [`perf`](https://perfwiki.github.io/main/), installed in any Linux distribution, which is able to obtain several information on the CPU usage, cache, memory usage of a program.

## Adaptyst
At CERN, IT-FTI, thanks to the work of Maksymilian Graczyk, the tool [Adaptyst](https://adaptyst.web.cern.ch/) has been developed.
Adaptyst is:
> A comprehensive and architecture-agnostic performance analysis tool addressing your software, hardware, and system needs.

### Features
- **Comprehensive Profiling**: Provides detailed code analyses (e.g., flame graphs) for both on-CPU and off-CPU activities, including full stack traces and all threads/processes.
- **Cross-Platform Compatibility**: Works on a wide range of architectures and vendors (Intel, AMD, ARM, RISC-V) as long as Linux and `perf` are supported.
- **Open-Source**: Developed at CERN as part of the EU-funded SYCLOPS initiative, Adaptyst is free and open-source, with regular updates on GitHub.
- **Tailored to Various Use Cases**: Supports diverse applications from microcontrollers to large-scale computations, with plans to accommodate even more specialized setups in the future.
- **Holistic Compute Solutions**: Goes beyond code optimization by proposing best compute solutions, including hardware (CPUs, GPUs, FPGAs) and system-level customizations based on specific needs.
- **Modular and Future-Proof**: Designed with a modular structure and API to stay current with rapid technological advancements and evolving hardware/software landscapes.

### References
Notice that AdaptivePerf is the older and deprecated name of Adaptyst.
- Graczyk, M., Roiser, S. [Enhancing software-hardware co-design for HEP by low-overhead profiling of single-and multi-threaded programs on diverse architectures with Adaptyst (AdaptivePerf)](https://arxiv.org/abs/2502.20947). ArXiv:[2502.20947](https://arxiv.org/abs/2502.20947). Talk presented at CHEP 2024.
- Graczyk, M. [Enhancing software-hardware co-design for HEP by low-overhead profiling of single-and multi-threaded programs on diverse architectures with Adaptyst (AdaptivePerf)](https://indico.cern.ch/event/1338689/contributions/6010664). Talk presented at CHEP 2024.
- Graczyk, M. [Platform-agnostic performance profiling with AdaptivePerf](https://www.vitamin-v.eu/OpenSourceWorkshop). Talk presented at the 1st Open-Source RISC-V Software Workshop co-located with RISC-V Summit Europe 2024 on 28 June 2024.
- Graczyk, M. [AdaptivePerf: a portable, low-overhead, and comprehensive code profiler for single- and multi-threaded applications](https://indico.cern.ch/event/1329689). Talk presented at the Compute & Accelerator Forum at CERN on 8 May 2024.
- Graczyk, M. [AdaptivePerf: a portable, low-overhead, and comprehensive code profiler for single- and multi-threaded applications](https://indico.cern.ch/event/1330797/contributions/5796595/). Poster presented at ACAT24.

## Exercises
In the following, we will learn how to install and use Adaptyst to profile MadGraph, generating flame graphs and observing the real speedup introduced with the CUDACPP plugin.

### Adaptyst

#### Installing Adaptyst
Following the [Adaptyst installation instructions on the website](https://adaptyst.web.cern.ch/install/), we need to install both:
- Adaptyst, available at this [GitHub repository](https://github.com/adaptyst/adaptyst);
- Adaptyst Analyser (the tool to analyze the results produced by Adaptyst), python package installable with `pip`, and available at this [GitHub repository](https://github.com/adaptyst/adaptyst-analyser).

##### Requirements
- Linux 5.8 or newer compiled with:
    - `CONFIG_DEBUG_INFO_BTF=y` (check this by seeing if `/sys/kernel/btf` exists in your system);
    - `CONFIG_FTRACE_SYSCALLS=y` (check this by seeing if `/sys/kernel/tracing/events/syscalls` exists in your system and is not empty, but you may need to mount `/sys/kernel/tracing` first)
    - If you want complete kernel debug symbols, `CONFIG_KALLSYMS=y` and `CONFIG_KALLSYMS_ALL=y` (or equivalent) should also be set.
    - Kernel recompilation may **not** be needed! If you have `/sys/kernel/btf` and `/sys/kernel/tracing/events/syscalls` as explained above and you don’t care about having kernel debug symbols, you’re already good to go here!
- Python 3.6 or newer.
- `addr2line` (part of binutils, tested with 2.35.2 and 2.42.0)
- CMake 3.20 or newer (if building from source)
- GCC (if building from source, Clang will not compile Adaptyst except for the patched “perf”!)
- `libnuma` (if a machine with your profiled application has NUMA)
- `CLI11` (if building from source)
- `nlohmann-json` (if building from source)
- PocoNet + PocoFoundation
- Boost (header-only libraries and the program_options module)
- `libarchive` (tested with 3.5.3 and 3.7.7)
- The patched `perf` dependencies:
    - Clang (if building from source, can be removed after installing Adaptyst)
    - `libtraceevent`
    - `libelf`
    - `flex`
    - Bison
    - `libpython` (corresponding to Python 3.6 or newer)

##### Install manually from source
Clone the [GitHub repository](https://github.com/adaptyst/adaptyst) and run, in order:
- `./build.sh` (as either non-root or root, non-root recommended);
- `./install.sh` (as root unless you run the installation for a non-system prefix) after `build.sh` finishes.

By default, Adaptyst is installed in `/usr/local`, its support files along with the bundled patched `perf` are installed in `/opt/adaptyst`, and the configuration file is installed in `/etc/adaptyst.conf`.
Refer to the [main documentation]( to understand the various compilation flags and settings that allows to change the paths.

#### Installing Adaptyst Analyser

##### Requirements
Python 3.6 or newer.
Also, you must have `npm` and Node.js 18 or newer during the installation process (you will not need these later).
All other dependencies are installed automatically when setting up Adaptyst Analyser.

##### Install with `pip`
This is a Python package, so you can install it with pip:
```bash
pip install git+https://github.com/adaptyst/adaptyst-analyser
```
#### Quick start
Follow the following steps to profile your program:
1. compile it with `-fno-omit-frame-pointer` and `-mno-omit-leaf-frame-pointer`;
1. run the following command (it changes the kernel settings for an entire machine, so be careful if you use a shared computer!): `sysctl kernel.perf_event_max_stack=1024`;
1. profile a command of your choice with `adaptyst -- <command>`;
1. When Adaptyst finishes its job, run Adaptyst Analyser passing as argument the path to the produced results, usually `./results`: `adaptyst-analyser <path_to_results>`.
1. Open the web page in your browser and explore!

> [!TODO]
> If you want, try running Adaptyst against a simple shell command, like `ls`.

### Profiling MadGraph

#### Generate the process
Use the CUDACPP plugin to generate the following process:
```
import model sm
generate p p > t t~ j
output madevent_gpu MY_PROC
```

#### What do we need to profile exactly?
In order to profile only the MadEvent generator, we do not need to profile the full `./bin/generate_events` script, that would do many more operations.
Additionally, during the run, MadGraph orchestrates several instances of MadEvent, one for each subprocess, and in general, parallelising them across the CPU cores.
In our case, we are just interested in assessing the performance for one single MadEvent for one SubProcess.

So, we can profile straighaway one `madevent` executable.
Let's pick one subprocess directory and `cd` into it.
Before profiling, we need to modify some compilation options.

#### Compilation options for profiling
We will add the debug options `-g` and we will compile everything with **frame pointers**, for the following files:
- `Source/make_opts`
  ```make
  GLOBAL_FLAG=-O2 -g -fno-omit-frame-pointer -mno-omit-leaf-frame-pointer
  ```
- `SubProcesses/makefile`
  ```make
  CXXFLAGS = -g -fno-omit-frame-pointer -mno-omit-leaf-frame-pointer -O2 -Wall -Wshadow -Wextra
  ```
- `SubProcesses/cudacpp.mk`
  ```make
  OPTFLAGS = -O2 -g -fno-omit-frame-pointer -mno-omit-leaf-frame-pointer
  ```

And then we can compile with the following chain of commands:
```bash
make BACKEND=cuda OMPFLAGS= -f cudacpp.mk -j4
make -C ../../Source -j4
make BACKEND=cuda -j4
```
Where the backend has been chosen accordingly.

Clean with:
```bash
make cleanall -f cudacpp.mk
make clean
make -C ../../Source clean
```

#### Profile with Adaptyst
After installing Adaptyst, the executable is in `/usr/local/bin/adaptyst`, and it should be used with `sudo` (there are ways to avoid doing that, but for now I am going to skip them), in this case we also preserve the environment:
```bash
sudo env PATH=$PATH LD_LIBRARY_PATH=$LD_LIBRARY_PATH adaptyst -- sh -c "./madevent_cuda < G1/input_app.txt"
```
This will produce the output in the folder `results`.
That folder should be shipped on a system where Adaptyst Analyser has been installed, and the results can be inspected by using it as:
```bash
adaptyst-analyser <path_to_results>
```

See also the [following page on the official documentation](https://adaptyst.web.cern.ch/docs/adaptyst/running-adaptyst/).

#### Profile with `perf`
Run with input file `input.txt`:
```
16384     2     2  !Number of events and max and min iterations
0.1     !Accuracy
0       !Grid Adjustment 0=none, 2=adjust
0       !Suppress Amplitude 1=yes
0       !Helicity Sum/event 0=exact
1
```
It will generate about 50k events, the first number must be a multiple of 32.
The last `1` indicated that it will run the first phase space parametrization.

Now profile with the command:
```bash
perf record --call-graph=fp ./madevent_cuda<input.txt
```

> [!NOTE] The `--call-graph` option
> It refers to the way of collecting the call graphs / call chains (basically the function stack) in a profiling session, see [here](https://stackoverflow.com/a/57432063).
> It can have three values:
> - `fp` (default): uses frame pointers, it is very efficient but can be unrealiable (particularly for optimized code);
> - `dwarf`: `perf` collects and stores a part of the stack memory itself and unwinds it with post-processing; this can be very resource consuming and may have limited stack depth, and the default stack memory chunk is 8 kiB, but can be configured;
> - `lbr` stands for *last branch records*: this is a hardware mechanism support by Intel CPUs; this will probably offer the best performance at the cost of portability.

Flamegraphs can be generated with:
```bash
perf script report flamegraph -o <output_file>.html
```
the `<output_file>.html` is an interactive flamegraph that can be opened in a browser.

> [!HINT] Better flamegraphs with an hack
> Flamegraphs are not normally in chronological order, and the functions that are called in the same way are merged together in a single block.
> However, there may be functions that are not correctly recognized during the stack unwinding, and they are shown as `unknown` in the flamegraph.
> These functions, even if different, are merged together in the same block (given their name would always be `unknown` and the algorithm creating the block thinks they are the same).
> This is, most of the time, unwanted.
> To avoid that, one could modify the code of the flamegraph generating script inside the `perf` folder, `/usr/libexec/perf-core/scripts/python/flamegraph.py`, applying [the following patch](./assets/perf_unknown_blocks.patch), with `patch -u flamegraph.py -b -i perf_unknown_blocks.patch`:
> ```diff
> --- flamegraph.py
> +++ flamegraph-revision.py
> @@ -107,7 +107,9 @@
>  
>          if "callchain" in event:
>              for entry in reversed(event["callchain"]):
> -                name = entry.get("sym", {}).get("name", "[unknown]")
> +                name = entry.get("sym", {}).get("name")
> +                if not name:
> +                    name = "[{}.{}]".format(event.get("symbol", "unknown"), entry.get("ip"))
>                  libtype = self.get_libtype_from_dso(entry.get("dso"))
>                  node = self.find_or_create_node(node, name, libtype)
>          else:
> ```
> So that now each `unknown` function is shown as `[<symbol_name>.<pointer>]` in the flamegraph.
> Now, each unknown function would be different and there would not be a single big unknown block.

### Profile with LHAPDF library

#### Installing of LHAPDF

> [!ATTENTION]
> LHAPDF can be installed also through MadGraph, by typing:
> ```
> mg5> install phapdf6
> ```
> However, in this case we want to profile it, so it would be nice to have debug symbols and to compile with frame pointer, so we will install it externally and tell MadGraph its path.

Python and Cython are required to build LHAPDF, if the python API is required.
Clone the repository:
```bash
git clone --depth 1 --branch lhapdf-6.5.5 https://gitlab.com/hepcedar/lhapdf
```

Install by running the following commands (notice, `python` is required), remember to define `LHAPDF_INSTALL_PATH` on the basis of what you want:
```bash
autoreconf -i
./configure --prefix=<installation_prefix> --enable-static CXXFLAGS="-O2 -g -fno-omit-frame-pointer -mno-omit-leaf-frame-pointer"
make -j4
make install
```

Then, one needs to set the paths to the libraries and includes files:
```bash
prefix=<installation_prefix>
export PATH="$prefix/bin:$PATH"
export PYTHONPATH="$prefix/lib64/python3.9/site-packages:$PYTHONPATH"
export LD_LIBRARY_PATH="${prefix}/lib:$LD_LIBRARY_PATH"
export LHAPDF_DATA_PATH="/cvmfs/sft.cern.ch/lcg/external/lhapdfsets/current/" # this folder is full of pre-downloaded pdf sets
```
Notice that there is a pre-made file `lhapdf/lhapdfenv.sh` containing the above settings already, that one can source.

#### Compile `madevent` with LHAPDF library
We need to make a few changes before compiling the code:
- source the LHAPDF environment variables;
- change the parameter `lhapdf_py3` in the `$MADGRAPH_DIR/input/mg5_configuration.txt` file, so that, when generating a new folder, LHAPDF is taken into account (remember to uncomment it):
  ```
  #! lhapdf-config --can be specify differently depending of your python version
  #!  If None: try to find one available on the system
  # lhapdf_py2 = lhapdf-config
  lhapdf_py3 = <installation_prefix>/bin/lhapdf-config
  ```

You can check this is the case, because the file `Source/make_opts` will show the following value of the option `lhapdf-config`, so that MadGraph knows where to pick the PDF:
```make
lhapdf=<installation_prefix>/bin/lhapdf-config
```
Additionally, when generating the process, modify the run card so by setting `pdlabel=lhapdf`. 
After the directory has been created you can also verify that `Source/run_card.inc` will have `PDLABEL = 'lhapdf'`.
With this changes, the code compiles correctly with the same compilation commands as before.

> [!Caution] What if you don't want to regenerate the process folder?
> If you don't want to regenerate the folder, you will need to do these changes yourself:
> - modify `lhapdf-config` in `Source/make_opts`;
> - modify `pdlabel` in `Cards/run_card.dat` and `Source/run_card.inc`.

#### Profile
Run the same commands in [Profile with Adaptyst](#Profile with Adaptyst), and [Profile with `perf`](#Profile with `perf`).
```bash
sudo env PATH=$PATH LD_LIBRARY_PATH=$LD_LIBRARY_PATH PYTHONPATH=$PYTHONPATH LHAPDF_DATA_PATH=$LHAPDF_DATA_PATH adaptyst -- sh -c "./madevent_cuda < G1/input_app.txt"
```
