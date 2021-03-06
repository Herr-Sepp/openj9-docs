<!--
* Copyright (c) 2017, 2020 IBM Corp. and others
*
* This program and the accompanying materials are made
* available under the terms of the Eclipse Public License 2.0
* which accompanies this distribution and is available at
* https://www.eclipse.org/legal/epl-2.0/ or the Apache
* License, Version 2.0 which accompanies this distribution and
* is available at https://www.apache.org/licenses/LICENSE-2.0.
*
* This Source Code may also be made available under the
* following Secondary Licenses when the conditions for such
* availability set forth in the Eclipse Public License, v. 2.0
* are satisfied: GNU General Public License, version 2 with
* the GNU Classpath Exception [1] and GNU General Public
* License, version 2 with the OpenJDK Assembly Exception [2].
*
* [1] https://www.gnu.org/software/classpath/license.html
* [2] http://openjdk.java.net/legal/assembly-exception.html
*
* SPDX-License-Identifier: EPL-2.0 OR Apache-2.0 OR GPL-2.0 WITH
* Classpath-exception-2.0 OR LicenseRef-GPL-2.0 WITH Assembly-exception
-->

# -Xgcpolicy


Controls the behavior of the garbage collector by specifying different garbage collection policies.

## Syntax

        -Xgcpolicy:<parameter>

## Parameters

| Parameter                                                                 | Default  |
|---------------------------------------------------------------------------|----------|
| [`balanced`](#balanced)                                                   |          |
| [`gencon`](#gencon)                                                       | <i class="fa fa-check" aria-hidden="true"></i><span class="sr-only">yes</span> |
| [`metronome`](#metronome-aix-linux-x86-only) (AIX&reg;, Linux&reg; x86 only)  |          |
| [`nogc`](#nogc)                                                           |          |
| [`optavgpause`](#optavgpause)                                             |          |
| [`optthruput`](#optthruput)                                               |          |


Specify the garbage collection policy that you want the OpenJ9 VM to use:

### `balanced`

        -Xgcpolicy:balanced

: The balanced policy policy uses mark, sweep, compact and generational style garbage collection. The concurrent mark phase is disabled; concurrent garbage collection technology is used, but not in the way that concurrent mark is implemented for other policies. The `balanced` policy uses a region-based layout for the Java&trade; heap. These regions are individually managed to reduce the maximum pause time on large heaps and increase the efficiency of garbage collection. The policy tries to avoid global collections by matching object allocation and survival rates.

: If you have problems with application pause times that are caused by global garbage collections, particularly compactions, this policy might improve application performance. If you are using large systems that have Non-Uniform Memory Architecture (NUMA) characteristics (x86 and POWER&trade; platforms only), the balanced policy might further improve application throughput.

    <i class="fa fa-pencil-square-o" aria-hidden="true"></i> **Note:** If you are using the balanced GC policy in a Docker container that uses the default `seccomp` Docker profile, you must start the container with `--security-opt seccomp=unconfined` to exploit NUMA characteristics. These options are not required if you are running in Kubernetes, because `unconfined` is set by default (see [Seccomp]( https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp)).

    For more information about this policy, including when to use it, see [Balanced Garbage Collection policy](https://www.ibm.com/support/knowledgecenter/SSYKE2_8.0.0/com.ibm.java.vm.80.doc/docs/mm_gc_balanced.html).

#### Defaults and options

The initial heap size is *Xmx/1024*, rounded down to the nearest power of 2, where *Xmx* is the maximum heap size available. You can override this value by specifying the `-Xms` option on the command line.

The following options can also be specified on the command line with `-Xgcpolicy:balanced`:

- `-Xalwaysclassgc`
- `-Xclassgc`
- `-Xcompactexplicitgc`
- `-Xdisableexcessivegc`
- `-Xdisableexplicitgc`
- `-Xenableexcessivegc`
- `-Xgcthreads<number>`
- `-Xgcworkpackets<number>`
- `-Xmaxe<size>`
- `-Xmaxf<percentage>`
- `-Xmaxt<percentage>`
- `-Xmca<size>`
- `-Xmco<size>`
- `-Xmine<size>`
- `-Xminf<percentage>`
- `-Xmint<percentage>`
- `-Xmn<size>`
- `-Xmns<size>`
- `-Xmnx<size>`
- `-Xms<size>`
- `-Xmx<size>`
- `-Xnoclassgc`
- `-Xnocompactexplicitgc`
- `-Xnuma:none`
- `-Xsoftmx<size>`
- `-Xsoftrefthreshold<number>`
- `-Xverbosegclog[:<file> [, <X>,<Y>]]`

The behavior of the following options is different when specified with `-Xgcpolicy:balanced`:

`-Xcompactgc`
: Compaction occurs when a System.gc() call is received (default). Compaction always occurs on all other collection types.

`-Xnocompactgc`
: Compaction does not occur when a System.gc() call is received. Compaction always occurs on all other collection types.

The following options are ignored when specified with `-Xgcpolicy:balanced`:

- `-Xconcurrentbackground<number>`
- `-Xconcurrentlevel<number>`
- `-Xconcurrentslack<size>`
- `-Xconmeter:<soa | loa | dynamic>`
- `-Xdisablestringconstantgc`
- `-Xenablestringconstantgc`
- `-Xloa`
- `-Xloainitial<percentage>`
- `-Xloamaximum<percentage>`
- `-Xloaminimum<percentage>`
- `-Xmo<size>`
- `-Xmoi<size>`
- `-Xmos<size>`
- `-Xmr<size>`
- `-Xmrx<size>`
- `-Xnoloa`
- `-Xnopartialcompactgc` (deprecated)
- `-Xpartialcompactgc` (deprecated)

### `gencon`

        -Xgcpolicy:gencon

: The generational concurrent policy (default) uses a concurrent mark phase combined with generational garbage collection to help minimize the time that is spent in any garbage collection pause. This policy is particularly useful for applications with many short-lived objects, such as transactional applications. Pause times can be significantly shorter than with the `optthruput` policy, while still producing good throughput. Heap fragmentation is also reduced.

### `metronome` (AIX, Linux x86 only)

        -Xgcpolicy:metronome

: The metronome policy is an incremental, deterministic garbage collector with short pause times. Applications that are dependent on precise response times can take advantage of this technology by avoiding potentially long delays from garbage collection activity. The metronome policy is supported on specific hardware and operating system configurations.

    For more information, see [Using the Metronome Garbage Collector](https://www.ibm.com/support/knowledgecenter/SSYKE2_8.0.0/com.ibm.java.vm.80.doc/docs/mm_gc_mgc.html).

#### Defaults and options

`-Xgc:synchronousGCOnOOM | -Xgc:nosynchronousGCOnOOM`
: One occasion when garbage collection occurs is when the heap runs out of memory. If there is no more free space in the heap, using `-Xgc:synchronousGCOnOOM` stops your application while garbage collection removes unused objects. If free space runs out again, consider decreasing the target utilization to allow garbage collection more time to complete. Setting `-Xgc:nosynchronousGCOnOOM` implies that when heap memory is full your application stops and issues an out-of-memory message. The default is `-Xgc:synchronousGCOnOOM`.

`-Xnoclassgc`
: Disables class garbage collection. This option switches off garbage collection of storage associated with Java classes that are no longer being used by the OpenJ9 VM. The default behavior is -Xnoclassgc.

`-Xgc:targetPauseTime=N`
: Sets the garbage collection pause time, where N is the time in milliseconds. When this option is specified, the GC operates with pauses that do not exceed the value specified. If this option is not specified the default pause time is set to 3 milliseconds. For example, running with `-Xgc:targetPauseTime=20` causes the GC to pause for no longer than 20 milliseconds during GC operations.

`-Xgc:targetUtilization=N`
: Sets the application utilization to `N%`; the Garbage Collector attempts to use at most (100-N)% of each time interval. Reasonable values are in the range of 50-80%. Applications with low allocation rates might be able to run at 90%. The default is 70%.

    This example shows the maximum size of the heap memory is 30 MB. The garbage collector attempts to use 25% of each time interval because the target utilization for the application is 75%.


      java -Xgcpolicy:metronome -Xmx30m -Xgc:targetUtilization=75 Test


`-Xgc:threads=N`
: Specifies the number of GC threads to run. The default is the number of processor cores available to the process. The maximum value you can specify is the number of processors available to the operating system.

`-Xgc:verboseGCCycleTime=N`
: N is the time in milliseconds that the summary information should be dumped.

    <i class="fa fa-pencil-square-o" aria-hidden="true"></i> **Note:** The cycle time does not mean that the summary information is dumped precisely at that time, but when the last garbage collection event that meets this time criterion passes.

`-Xmx<size>`
: Specifies the Java heap size. Unlike other garbage collection strategies, the real-time Metronome GC does not support heap expansion. There is not an initial or maximum heap size option. You can specify only the maximum heap size.

### `nogc`

        -Xgcpolicy:nogc

: This policy handles only memory allocation and heap expansion, but doesn't reclaim any memory. If the available Java heap becomes exhausted, an `OutOfMemoryError` exception is triggered and the VM stops.

    Because there is no GC pause and most overheads on allocations are eliminated, the impact on runtime performance is minimized. This policy therefore provides benfits for "garbage-free" applications. See the following section, "When to use nogc", for some possible use cases.

    You should be especially careful when using any of the following techniques with `nogc` because memory is never released under this policy:  
    - Finalization  
    - Direct memory access  
    - Weak, soft, and phantom references

    This policy can also be enabled with the [`-XX:+UseNoGC`](xxusenogc.md) option.

    Further details are available at [JEP 318: Epsilon: A No-Op Garbage Collector](http://openjdk.java.net/jeps/318).

####When to use nogc

: For most Java applications, you should _not_ use `nogc`. However, there are some particular situations where it can be appropriate:

    Testing during development

    - GC performance. Use `nogc` as a baseline when testing the performance of other GC policies, including the provision of a low-latency baseline.

    - Application memory. Use `nogc` to test your settings for allocated memory. If you use [`-Xmx`](xms.md) to set the heap size that should not be exceeded, then your application will crash with a heap dump if it tries to exceed your memory limit.

    Running applications with minimal or no GC requrements

    - You might use `nogc` when an application is so short lived that allocated memory is never exhausted and running a full GC cycle is therefore a waste of resources.

    - Similarly, when memory application is well understood or where there is rarely memory to be reclaimed, you might prefer to avoid unnecessary GC cycles and rely on a failover mechanism to occasionally restart the VM as necessary.


### `optavgpause`

        -Xgcpolicy:optavgpause

: The "optimize for pause time" policy uses concurrent mark and concurrent sweep phases. Pause times are shorter than with `optthruput`, but application throughput is reduced because some garbage collection work is taking place while the application is running. Consider using this policy if you have a large heap size (available on 64-bit platforms), because this policy limits the effect of increasing heap size on the length of the garbage collection pause. However, if your application uses many short-lived objects, the `gencon` policy might produce better performance.

### `optthruput`

        -Xgcpolicy:optthruput

: The "optimize for throughput" policy disables the concurrent mark phase. The application stops during global garbage collection, so long pauses can occur. This configuration is typically used for large-heap applications when high application throughput, rather than short garbage collection pauses, is the main performance goal. If your application cannot tolerate long garbage collection pauses, consider using another policy, such as `gencon`.







<!-- ==== END OF TOPIC ==== xgcpolicy.md ==== -->
