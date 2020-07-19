---
title: "benchmarking_database_there_and_back_again"
date: 2020-04-20T08:16:30+01:00
draft: false
type: muses
layout: single
---

# Benchmarking Database for "There and Back Again: The Practicality of GPU Accelerated Digital Audio"

This blog post is written to accompany the paper titled "There and Back Again: The Practicality of GPU Accelerated Digital Audio" presented at the conference of New Instruments for Musical Expression ([NIME 2020](https://nime2020.bcu.ac.uk/)).
The paper can be found [here](https://uwe-repository.worktribe.com/output/5951330/there-and-back-again-the-practicality-of-gpu-accelerated-digital-audio), though the page limit meant that many details had to be left out. This post gives the needed
space to cover the benchmarking methodology further, the details of all the tests and how to use the benchmarking suite. The main purpose of this post is to provide the full database of results collected using the benchmarking suite across several systems. 
The download for the database can be found at the end of this post.

The primary aim of this benchmarking suite is to demonstrate how viable it is to incorporate the GPU into the design of general and real-time audio applications. This is done by creating a collection 
of tests starting as simple as possible with purely data transfers, working up to complex synthesizers with bidrectional data transfers. One of the most infleuncial parameters exposed in digital audio
is the buffer size. This is the number of samples that can be batched and processed at a time within a program. Using a large buffer size is efficient, though there are other performance issues that arise
as a consequence which effect real-time scenarios. Latency is the time taken to process a buffer of audio samples. Jitter is the difference between two consecative buffer's latency. Latency and jitter
have been studied and shown to effect the perception of interative, real-time digital musical instruments. This work invovles the GPU which requires further datatransfers between devices in the 
system which further effects the latency and jitter. Therefore, the benchmarking suite scales the buffer size and records the overall execution time, latency and jitter of the buffers. The results
collected by the benchmarking suite can be used to review the practicality of using the GPU in different types of applications. Further, optimal buffer sizes which balance overall efficiency with 
latency and jitter will be highlighted. 

NOTE! The benchmarking suite only profiles the CPU-GPU interactions. Therefore, measurements of time, latency and jitter from other components which make up the
rest of the real-time environment is not included in the results. This means the results here need to be appended to the rest of a system's real-time round trip model. The reason for this choice is to 
isolate the profiling to a component that can be controlled and is consitent.


## Benchmarking Methodology

The benchmarking methodology is discussed here, describing how the suite was designed to achieve the target measurements for this work.

### Profiling Timer

CPU timers have been used to measure the elapsed time between the beginning and completion of the tests. There are several reasons why CPU timers have been used instead of specific GPU profiling tools.
Fundementally, CPU timers provide a realistic representation of the time to complete the processes. Ultimately, the time it takes to initiate and complete on the CPU side is what is important,
even though it executes on the GPU. The GPU profiling tools are extremely useful, and were considered closely for use in this benchmarking suite, however, there are a number of complications. Firstly,
the tools are vendor specific. This means there are tools provided by NVIDIA for CUDA, while AMD provide tools for OpenCL. This raises an issue with consitency between software tools. The way they record
and measure at points may differ and result in an unfair comparison. Secondly, the tools are programs in their own right, and therefore cannot be easily integrated into the benchmarking program, which is
written in C++.

The benchmarking profile timer has been written as a class, provided in the benchmarker.hpp file. The class allows a timer to be started, paused and ended. The timer works out the total time between
the timer start to the timer end. Additionally, the timer calculates an average time with respect to the number of pauses used between timer start and end and minimum and maximum times between pauses.
Another metric called jitter is calculated. The jitter is the difference between consecutive measurements when pausing the timer. The maximum jitter is also recorded. These are all the essential metrics
considered necessary for evaluating general and real-time tests in this benchmarking suite. When constucting the benchmarking objects, a string is passed and is used to record measurements to a file of
the string name.

### Benchmarking Flow

The benchmarking suite needs to run the benchmarking tests on different systems which include different sets of hardware. Systems can contain multiple supporting GPUs which should all be tested, this requires
querying CUDA and OpenCL support for all devices. To begin with, OpenCL support is checked. If OpenCL support is detected, then all supporting devices are listed. The list of OpenCL devices are then iterated
through and the "General" and "Real-time" benchmarks executed on them. The results are recorded in appropriatly labled files. With all OpenCL devices tested, CUDA support is queried. Like with OpenCL,
if CUDA is supported, all devices are listed and the tests executed on each. It is possible neither OpenCL or CUDA are supported on a system because the runtime or device
drivers are not installed. This will be communicated on the console and the tester can check if their system can support the technologies and download the necessary software.

```c
if(isOpenCLCompatible()) {
    clDevice = cudaListDevices();
    for (int i = 0; i != clDevices.size(); ++i) {
        runGeneralBenchmark(clDevice[i]);
        runRealTimeBenchmark(clDevice[i]);
    }
}

if(isCUDACompatible()) {
    cudaDevice = clListDevices();
    for (int i = 0; i != cudaDevice.size(); ++i) {
        runGeneralBenchmark(cudaDevice[i]);
        runRealTimeBenchmark(cudaDevice[i]);
    }
}
```

### Benchmarking Tests

A number of different benchmarking tests were designed to test different types of typical processes which can be offloaded to the GPU. These vary from simple tests where buffers of samples are transfered back and forth (bidrectional) to simple unidirectional synthesis methods. These will be described in more detail in the following sections.

The benchmarking suite is built up on a number of different benchmarking tests. These are classified into two types refered to the "General Benchmark" and "Real-time Benchmark" tests. The general 
benchmarking tests work by running a single test for a set number of repetitions. The total and average time for all the repetitions are measured and recorded. These tests include micro benchmarks which
isolate specific processes and measure them. These reflect less of the realistic use of the GPU in the real-world, but offers insight into specific processes and highlight limitations. The template for the general tests are outlined in the following code snippet:

```c
void test() {
    prepareTest(hostVariables, deviceVariables);
    
    if(isWarmup) {
        runTest();
    }
    for(int i = 0; i != numRepeats; ++i) {
        benchmarker.startTimer();
        runTest();
        benchmarker.endTimer();
    }
    benchmarker.elapsedTimer();
    checkTestResults(testResults);
    cleanup(hostVariables, deviceVariables);
}
```

Here, the runTest() function executes the test, either side the benchmarker object records the duration spent executing the test. These accumulate and finally after the test is repeated numRepeats times,
the benchmarker.elapsedTimer() function is called, which calculates all the averages, jitter, etc and records them to a file.


The other type of tests are the "Real-time Benchmark" tests, these reflect a more realsitic use of the GPU in digital audio. These work by measuring the time it takes to process a seconds worth of samples at a given sample rate
using a set buffer length. The real-time template is shown below:

```c
if (isWarmup) {
    runTest();
}
while (numSamplesComputed < aFrameRate) {
    benchmarker.startTimer();
    runTest();
    benchmarker.endTimer();

    numSamplesComputed += bufferLength;
    checkTestResults(testResults);
}
benchmarker.elapsedTimer();
cleanup(hostVariables, deviceVariables);
```

Here, instead of just executing for a ste number of times and working out time measurements, the test is executed for a second at the sample rate. The number of times the test needs to run is therefore
depedent on the bufferLength variable. For example, if measuring a test at 44.1Khz with a bufferLength of 128, the test is executed 3446 (Round up) times. If the total time measured to do this is above
1 second, then the test fails to operate in real-time.

All benchmarking tests have an option to run a warmup phase. This runs the test before recording measurements in order to allow the GPU and interfacing API to prepare all
resources and optimize the process. With this turned off, a disproprotionat amount of time is spent on running the first test. CUDA appeared to be effected by this considerably more than the OpenCL 
implementation (See file "OpenCL vs CUDA: Warmup" in "Database of benchmarking results"). This usually will not pose a problem, but in the rare case that it does, these results should be considered.

Many problems fundementally will not perform well in the GPU environment. If a problem inehrintly cannot be designed to maintain thread/data independence and requires threads to communicate and synchonize, 
the GPU is likely not suitable for the task. However, the kind of tests used in the benchmarking suite have purposefully been chosen as suitable processes for the GPU achtecture. 

Each test is defined in the format as follows: [implementation]_[test]_[type]. The [implementation] is either cl or cuda, and indicates the implementation of openCL or CUDA. The [test] indicate which
test is actually being used, for example cputogputocpu is the test which measure the time to transfer buffers from CPU to the GPU and back. Finally, the [type] indicates the type of test, these can be
"standard" and "mapped" for general tests, identifying which type of memory is used for the sample buffers. Real-time tests can either be "baseline" or "processing" indicating if the test is just
minimum data transfers or involves processing.

#### Baseline Tests

The first set of "general benchmark" tests aim to profile the minimum time implications involved when incorporating the GPU into a program. This means forming tests which measure the different
possible data transfers that are possible within a CPU-GPU environment, as this is the minimum overhead involved. 

These include the following tests:

* *nullkernel*
* *cputogpu*
* *gputocpu*
* *cputogputocpu*
* *unidirectional_baseline*
* *bidirectional_baseline*

#### Simple Processing

The simple buffer processing test is the first test which introduces GPU processing. It is referenced in the program as *simplebufferprocessing*. The test is naturally bidirectional, as the 
buffers are sent to the GPU, processed, then sent back to the CPU. The test simply applies attenuation at a constant rate across the whole audio signal. This works by just multiplying each sample with a constant
value, in this case 0.5. This halves all samples values, which requires no inter-thread communication or inherintly complex processing on the GPU. This makes it a small progression from purely data transfers,
to involving some very simple processing of the data.

[//]: # (Talk about some interesting results here?)

#### Complex processing

In *complexbufferprocessing*, the complex processing of buffers progresses the complexity of the processing further. Again, as buffers are processed this is a bidirectional test. This test involves more complex processing features than the
simple processing test. The test applies a triangular smoothing filter to the signal. Smoothing filters are methods that take neighbouring values
and average them to provides a signal which flows smoothly between each sample, reducing unwanted noise in the signal. 
Rectangular smoothing takes the previous, current and next sample, divides by 3 and writes that in place of the sample:

<div>$$S_{n} = \frac{S_{n-1} + S_{n} + S_{n+1}}{3}$$</div>

Triangular smoothing extends this by taking neigbours two steps in each direction and using a weighted average, like so:

<div>$$S_{n} = \frac{S_{n-2} + 2S_{n-1} + 3S_{n} + 2S_{n+1} + S_{n+1}}{9}$$</div>

([Link to more info](http://terpconnect.umd.edu/~toh/spectrum/Smoothing.html))

The more complex feature required for this smoothing filter is the accessing of neighbouring values in the buffer in order to smooth the signal. Accessing neighbours can effect performance significantly, especially if 
synchonization or inter-thread communication is involved. This test uses an input and output buffer and therefore can maintain read-only accesses, which avoids the challenges that would otherwise be imposed.

[//]: # (Talk about some interesting results here?)

#### Simple Synthesis

Synthesising audio is the generation of an audio signal. There are many different methods that can be used to do this, one of the most basic is components is a sine oscillator. This can generate an approximate
sine wave at a specific frequency. In test *simplebuffersynthesis* & *unidirectional_processing*, the sin function is used to generate a single sine wave across the buffers. Instead of generating each sample one after another,
the samples are generated for each element in the buffer in parallel. This typically is not an intensive process on its own, but it has the potential to scale for techniques such as additive synthesis 
where hundreds/thousands of sine waves need to be generated. The test maintains a undirectional case, as samples are only transfered back to the CPU from the GPU.

```c
float relativeFrequency = frequency / (float)sampleRate;
for(int i = 0; i != max_time; ++i)
    samples[i] = sin(2.0 * M_PI * relativeFrequency * i);
```

This can be mapped into the GPU environment by creating a thread for every sample in the buffer and using the thread ID as the sample position to generate the signal. A simple code snippet showing this:

```c
float relativeFrequency = frequency / (float)sampleRate;
int time = global_id;
float currentSample = sin(2.0 * M_PI * relativeFrequency * time);
```

[//]: # (Talk about some interesting results here?)

#### Complex Synthesis

The complex synthesis test referenced as *complexbuffersynthesis* & *bidirectional_processing* is the most intensive test in the benchmarking suite. This test builds a physical model based around the mathematics of 2D
wave propagation. In brief, this works by building a 2 dimensional grid of points approximating regions of space, the discretized wave equation is applied to each point to generate the next state of
the model. By time-stepping through this model at an audio rate, like 44.1KHz, the model can be sampled and generates audio simulating the real-world phenomena of sound waves propagating through a material.
More details for this synthesis technique can be found in the paper titled "OpenCL vs: Accelerated Finite-Difference Digital Synthesis" and the blog post [here](https://muses-dmi.github.io/pm_gpus/gpu_accelerated_2d_physical_model/)

This test is bidrectional as buffers of samples are input from CPU to GPU to excite the model and output back to the CPU. The model grid size for the test was set to 64x64. This results in a grid made up of
4096 grid points. For every time step, all grid points must be calculated. This results in 4096 * 44100 = 180633600 calculations per second. This is a great example of a process which would not operate
in real-time when executed on the same systems CPU, but is made possible by the massively processing potential of the GPU.

[//]: # (Talk about some interesting results here?)

## Conclusion

Benchmarking suite source code: [here](https://github.com/Harri-Renney/ThereAndBackAgain-NIME)

Database of benchmarking results: [download](/assets/physical_models&benchmarking/benchmarking_database_there_and_back_again.zip)

NIME2020 presentation: [download](/assets/physical_models&benchmarking/there_and_back_again_presentation.zip)