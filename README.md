# Hash Hash Hash
TODO introduction
In this project, we delve into handling hash table entries using pthread mutexes, where we create two versions of the hash table implementaion, one which only takes into account the correctness (using a single mutex), and the other takes into account performance and correctness using multiple mutexes for different entries. We test for correctness in hash table v1, and for correctness and perfomance in hash table v2.


## Building
```shell
TODO
To build the file, we first navigate to the directory using the cd command. Once we are in the directory, we run the command 
make
This would generate an executable hast-table-tester, which we would use to test our implementations.
```

## Running
```shell
TODO To run the hash table implementations, we run the following command in the directory where our executable is located:
./hash-table-tester -t [number_of_threads] -s [number_of_entries]
where we replace [number_of_threads] [number_of_entries] with the number of threads and the number of entries respectively.
for instance: ./hash-table-tester -t 8 -s 50000
This runs the hastable with 8 threads and with 50,000 entries. This would generate an output in the following format:
 
./hash-table-tester -t 8 -s 50000
Generation: 78,170 usec
Hash table base: 497,843 usec
  - 0 missing
Hash table v1: 1,799,294 usec
  - 0 missing
Hash table v2: 203,676 usec
  - 0 missing
Which gives the execution time for the base hash table, the v1 version, and the v2 version
```

## First Implementation
In the `hash_table_v1_add_entry` function,  I added a mutex to the entire hash table to ensure thread safety during the addition of new entries. This mutex locks the entire hash table, thus preventing concurrent modifications but allowing concurrent reads. This would have a slow implementation as an add would stop all other threads from modyfying the table.

### Performance
```shell
To test the performance of the first implementation, run:
./hash-table-tester -t 8 -s 50000
The outputs of the command above were the following in this test run:

Generation: 79,726 usec
Hash table base: 618,915 usec
  - 0 missing
Hash table v1: 1,216,053 usec
  - 0 missing
Hash table v2: 115,769 usec
  - 0 missing.

Version 1 is slower than the base version due to the overhead introduced by the mutex, which serializes the entry additions. This serialization is necessary to prevent race conditions that would produce incorrect results(missing entries) but significantly reduces the potential for concurrent processing, especially evident in environments with a high degree of parallelism.


## Second Implementation
In the `hash_table_v2_add_entry` function, I implemented a fine-grained locking mechanism, where each hash table entry has its own mutex, allowing multiple threads to concurrently modify different parts of the hash table. This reduces contention compared to the first version, where a single mutex locked the entire table. However, the perfomance may not be optimal, i.e. basetime/(num_cores-1)

### Performance
Using the values from above we fiund that compared to the base, Version 2 shows a substantial decrease in execution time, indicating that threads are able to change and update the hash table simultaneously imprving perfomance.
Performance Analysis: The use of individual mutexes for each hash table entry in V2 allows for higher degrees of parallelism. This is particularly beneficial when the workload involves a large number of threads performing many independent insertions into the hash table.
Mathematically, this is close to basetime/numcores-1, which is 88416 usec. The reduced lock region in V2, as compared to V1, contributes significantly to its performance. In scenarios where hash table entries are evenly distributed, the chance of two threads needing the same lock is lower, hence improving the overall throughput.
V2 scales better with greater amount of threads

## Cleaning up
```shell
To clean the files generated after we are done with the program, we run the following command:
make clean
this would remove the executable that was creating in the build process, and all other object files that were created as well
```