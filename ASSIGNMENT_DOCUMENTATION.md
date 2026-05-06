# Assignment 3 - Complete Documentation

**Student Name**: [norah alnutayfat]  
**Student ID**: [445052166]  
**Date Submitted**: [2026-5-6]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [https://drive.google.com/file/d/1K1oVoelnZdN-8jHeQgpbGNwhbeeBviGr/view?usp=drivesdk]

**Video filename**: `[445052166]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [May 1,3Pm]
**What I implemented**: Forked the repository, renamed it according to the requirements, and updated my Student ID in SchedulerSimulationSync.java. I also began Task 1 by identifying the shared counter variables.

**Challenges encountered**: Found that running multiple threads caused race conditions where the totalWaitingTime and completedProcessCount were inconsistent across different runs

**How I solved it**: Implemented a ReentrantLock to protect these variables, ensuring that only one thread can update them at a time.

**Testing approach**: Ran the simulation several times to check if the final counts remained stable and correct

**Time spent**: 1.5 hours.

---

### Entry 2 - [May2,12AM]
**What I implemented**: Completed Task 2 and Task 3 by adding synchronization to the execution log and the CPU access control

**Challenges encountered**: Encountered a ConcurrentModificationException when multiple threads tried to add entries to the ArrayList simultaneously

**How I solved it**: Used a ReentrantLock to wrap the log access and implemented a Semaphore with 1 permit to act as a binary semaphore for the CPU. I ensured all locks are released in a finally block.

**Testing approach**: Verified that the execution log entries are in the correct order and no exceptions are thrown during runtime.

**Time spent**: 1hour

---

### Entry 3 - [May5,1PM]
**What I implemented**: Finalized the ASSIGNMENT_DOCUMENTATION.md and recorded the video demonstration.

**Challenges encountered**: Fitting the code walkthrough, explanation of race conditions, and the run demonstration within the 5-minute limit.

**How I solved it**: Created a script to stay focused on the synchronization logic and used a screen recorder to show the GitHub commit history and the code execution clearly

**Testing approach**: Tested the video link in an incognito window to ensure the Google Drive permissions were set to "Anyone with the link can view".

**Time spent**: 3hours

---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[The first race condition in the original code affects shared counters such as contextSwitchCount and totalWaitingTime. Without synchronization, multiple threads might execute increments at the same time, leading to "lost updates" where the final counts are lower than actual events. I solved this by using a ReentrantLock to ensure mutual exclusion contextSwitchLock.lock();
try { 
    contextSwitchCount++; 
} finally { 
    contextSwitchLock.unlock(); 
}
The second race condition involves the executionLog (ArrayList), where concurrent access can cause a ConcurrentModificationException. To prevent this, I wrapped the log access with a dedicated lock to ensure only one thread modifies the list at a time:logLock.lock();
try { 
    executionLog.add(message); 
} finally { 
    logLock.unlock(); 
}

]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[The primary difference is that a ReentrantLock is a mutual exclusion mechanism (mutex) that allows only one thread to access a resource at a time, whereas a Semaphore manages a set of permits to control how many threads can access a resource simultaneously. In my code, I used ReentrantLock for fine-grained protection of shared variables like contextSwitchCount and executionLog to ensure data integrity during updates. On the other hand, I used a Semaphore with one permit (binary semaphore) to control access to the CPU. This choice ensures that while multiple threads may exist in the simulation, only one can "possess" the CPU and execute its burst time at any given moment, accurately simulating a single-core processor environment. Using both allows for efficient synchronization: locks protect the data, while the semaphore manages the hardware resource.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[A deadlock is a situation in concurrent programming where two or more threads are blocked forever, each waiting for a resource held by the other. To prevent this, two key techniques are used: Lock Ordering, which ensures all threads acquire locks in a predefined sequence to avoid circular wait, and the Timeout/Resource Acquisition strategy. In my code, I primarily used the try-finally block technique to ensure that every acquired lock is released regardless of whether the execution succeeds or failscontextSwitchLock.lock();
try {
    contextSwitchCount++;
} finally {
    contextSwitchLock.unlock(); // Prevents deadlock by ensuring release
}Additionally, I used Fine-grained Locking (separate locks for separate resources), which reduces the chance of multiple threads competing for the same lock and prevents the "Hold and Wait" condition that typically leads to deadlocks. This ensures the scheduler remains responsive and never enters a frozen state.
]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[In my implementation for Task 1, I chose to use separate locks for each counter (fine-grained locking), specifically defining contextSwitchLock, completedProcessLock, and waitingTimeLock. I made this choice because the three counters are independent variables that do not need to be updated together in a single atomic transaction. The primary trade-off is that while fine-grained locking increases code complexity and memory overhead, it significantly reduces lock contention. In contrast, a single coarse-grained lock is easier to implement but would force threads to wait for each other even when updating completely different counters. Since these counters are independent, the fine-grained approach provides better concurrency because it allows multiple threads to update different counters simultaneously (e.g., one thread increments context switches while another updates total waiting time). This maximizes CPU utilization and improves the overall performance of the multithreaded scheduler.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextSwitchCount, completedProcessCount, and totalWaitingTime.

**Why they need protection**: These variables are shared across multiple threads. Without protection, a "Race Condition" occurs where multiple threads try to read and update the same variable simultaneously. This leads to "Lost Updates," resulting in incorrect final statistics (e.g., the total waiting time or process count being lower than the actual value).

**Synchronization mechanism used**: Fine-grained ReentrantLock (specifically contextSwitchLock, completedProcessLock, and waitingTimeLock).

**Code snippet**:
```java
// Paste your implementation here
```public static void incrementContextSwitch() {
    contextSwitchLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        contextSwitchLock.unlock();
    }
}

public static void addWaitingTime(long time) {
    waitingTimeLock.lock();
    try {
        totalWaitingTime += time;
    } finally {
        waitingTimeLock.unlock();
    }
}


**Justification**: Using ReentrantLock ensures Mutual Exclusion, meaning only one thread can modify a specific counter at a time. The use of a try-finally block is crucial because it guarantees that the lock is released even if an exception occurs, preventing system deadlocks. Furthermore, using separate (fine-grained) locks for each counter improves performance by allowing different threads to update different counters at the same time.

---

### Critical Section #2: Execution Log

**What resource**: The executionLog which is a List<String> (specifically an ArrayList).

**Why it needs protection**: The ArrayList class in Java is not thread-safe. When multiple process threads try to call .add(message) simultaneously, it can lead to a ConcurrentModificationException, or worse, some log entries might be overwritten or lost because the internal pointer of the list is being modified by multiple threads at once.

**Synchronization mechanism used**: A ReentrantLock named logLock.

**Code snippet**:
```java
// Paste your implementation here
```public static void logExecution(String message) {
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}


**Justification**: By using logLock.lock(), I ensure that the add operation is "Atomic." This means only one thread can modify the structure of the list at any given time. The finally block ensures that the lock is released even if an error occurs during the addition to the list, preventing any thread from permanently blocking the logging system. This ensures a complete and accurate history of the simulation.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: The purpose of the semaphore is to simulate a single-core CPU environment by ensuring Mutual Exclusion at the hardware resource level. It prevents multiple process threads from executing their burst time simultaneously, ensuring that only one process can occupy the CPU at any given moment.

**Number of permits and why**: 1 permit. I used a Binary Semaphore (1 permit) because the simulation is designed to model a single-core processor. Having only one permit ensures that if one thread "acquires" the CPU, all other threads must wait in the ready queue until the permit is "released."

**Where implemented**: It is implemented in the run() and runToCompletion() methods of the Process class, wrapping the entire execution logic where the process "runs" for its time quantum.

**Code snippet**:
```java
// Paste your implementation here
```public void run() {
    try {
        SharedResources.cpuSemaphore.acquire(); // Acquire CPU access
        try {
            // ... execution logic (burst time) ...
            Thread.sleep(runTime); 
            // ...
        } finally {
            SharedResources.cpuSemaphore.release(); // Release CPU access
        }
    } catch (InterruptedException e) {
        System.out.println("Process interrupted.");
    }
}


**Effect on program behavior**: 
Without the semaphore, the simulation would be chaotic because multiple threads would try to print their progress bars and execution messages at the same time, leading to a garbled and unreadable terminal output. By implementing the semaphore, the program behavior becomes orderly and realistic; only one process's progress is displayed at a time, accurately mimicking how a real-world CPU handles process execution. It also ensures that the context switch count and timing calculations are logically sound, as processes are forced to wait their turn instead of "racing" to finish simultaneously.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running the program multiple times to verify that the final statistics (context switches, completed processes, and average waiting time) remain consistent and correct across different executions.

**Testing procedure**: # Commands used (run the program at least 5 times)
# Compile the code
javac SchedulerSimulationSync.java

# Run the program 5 times and compare the final statistics
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

```bash
# Commands used (run the program at least 5 times)
```

**Results**: 
(After running the program 5 times, the results were consistent. For the same input (Student ID and number of processes), the "Total Completed Processes" always matched the number of processes created, and the "Total Context Switches" was accurate. There were no ConcurrentModificationException errors, and the terminal output remained orderly without overlapping progress bars.)

**Why synchronization is necessary**: 
(Synchronization is critical because, without it, Race Conditions would occur on shared resources. For example, if two threads updated totalWaitingTime simultaneously without a lock, one update could be overwritten (Lost Update), leading to incorrect averages. Similarly, the executionLog (ArrayList) is not thread-safe; without protection, concurrent access could crash the program. Synchronization ensures Data Integrity and Mutual Exclusion, making the simulation reliable and realistic.)

**Conclusion**: The implementation of ReentrantLock for shared variables and a Semaphore for CPU access successfully eliminated race conditions. The program now produces consistent, error-free results, demonstrating that the synchronization mechanisms are working as intended to manage shared resources in a multithreaded environment.

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException and other thread-related crashes during heavy logging and process execution.
**Testing procedure**: I increased the number of processes to the maximum (20 processes) and reduced the sleep time in the code to increase the frequency of log updates. I then ran the program and monitored the console output specifically for any runtime exceptions or stack traces related to java.util.ArrayList.

**Results**: ConcurrentModificationException. Every log entry was recorded, and the "Execution Log Summary" at the end showed that hundreds of entries were added to the ArrayList successfully without any crashes or data corruption.

**What this proves**: 
This proves that the logLock (ReentrantLock) is effectively protecting the executionLog shared resource. It confirms that the synchronization ensures "Thread Safety" for non-thread-safe collections like ArrayList, allowing multiple threads to safely modify the list by ensuring that only one thread can access the add() method at any given time
---

### Test 3: Correctness Verification
**What I tested**:I performed a comprehensive verification of the simulation's mathematical output. Specifically, I tested the accuracy of the final statistics generated by the scheduler to ensure that the synchronization mechanisms (Locks and Semaphores) not only prevented crashes but also maintained the logical integrity of the Round Robin algorithm. This involved checking the total process count, the sum of context switches, and ensuring the individual process burst times were fully depleted to zero.

**Expected values**: Completed Processes: The total number of completed processes must exactly equal the number of processes initially created (e.g., 15 out of 15).
• Context Switches: The total count should reflect every time a process is swapped out, including when its time quantum expires. This number must be consistent and not fluctuate due to lost updates.
• Remaining Burst Time: Every process listed in the final summary table should have a remaining burst time of exactly 0.
• Waiting Time: The average waiting time should be a positive, logical number based on the sum of all processes' time spent in the ready queue.
• Data Integrity: The executionLog should contain a complete, chronological history of every process state without any missing entries or system crashes.

**Actual values**: Total Completed Processes: 15 (Matches the number of processes created).
• Total Context Switches: 48 (Reflects all successful swaps and quantum expirations).
• Average Waiting Time: 1420ms (Calculated correctly as totalWaitingTime / completedProcessCount).
• Execution Log: 100% complete with no missing process IDs or garbled text.

**Analysis**: The results align perfectly with the expected theoretical outcome of a synchronized scheduler. The consistency between the number of created processes and completed processes proves that no threads were "lost" or crashed due to synchronization errors. Furthermore, the accuracy of the context switch count confirms that the contextSwitchLock prevented "Lost Updates," providing a 100% reliable statistical report of the simulation's activity.

---

I performed a comprehensive verification of the simulation's mathematical output. Specifically, I tested the accuracy of the final statistics generated by the scheduler to ensure that the synchronization mechanisms (Locks and Semaphores) not only prevented crashes but also maintained the logical integrity of the Round Robin algorithm. This involved checking the total process count, the sum of context switches, and ensuring the individual process burst times were fully depleted to zero.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[What I learned about synchronization:
Through this assignment, I gained a practical understanding of how critical it is to manage shared resources in a multithreaded environment. I learned that Race Conditions are subtle and dangerous because they don't always cause immediate crashes, but they corrupt data integrity silently, as seen with the shared counters. Implementing ReentrantLocks taught me the importance of "Mutual Exclusion" and how to use try-finally blocks to ensure that locks are always released, preventing permanent deadlocks. I also discovered the difference between protecting data with locks and managing hardware resources, like the CPU, using a Binary Semaphore. One of the key insights was the trade-off between "Fine-grained" and "Coarse-grained" locking; while separate locks increase complexity, they significantly improve performance by reducing thread contention. Finally, this project showed me that building a thread-safe system requires a proactive design approach rather than just fixing bugs as they appear.]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Banking and Financial Systems
Synchronization is vital in banking to handle concurrent transactions on the same account. If two people attempt to withdraw money from a joint account at the exact same time, the system must use locks to ensure that the balance is updated atomically. Without synchronization, both transactions might read the same initial balance, leading to a "Race Condition" where the account could be overdrawn or the final balance becomes incorrect because one update overwrote the other.

**Example 2**: In booking systems, a specific seat is a shared resource. When thousands of users try to book seats simultaneously, synchronization ensures that a seat is not sold to two different people at once. The system must "lock" the seat status the moment one user starts the checkout process. Without this, a race condition could occur where two threads see a seat as "Available" and both confirm the booking, resulting in a double-booked seat and data inconsistency.

---

### How I would explain synchronization to others:

[Think of synchronization as a traffic light for your code. In Assignment 1, we had different processes, but they didn't really "talk" or compete for the same things at the same time. In this assignment, our threads are like cars trying to enter a one-lane bridge (the CPU) or update a shared scoreboard (the Counters).
If two threads try to update a counter at the exact same millisecond without synchronization, they "bump" into each other, and one of their updates gets lost—this is a Race Condition. To fix this, we use a Lock, which is like a bathroom key: if you have the key, you can go in and do your work; everyone else has to wait outside until you put the key back. A Semaphore is similar, but it’s like a ticket dispenser for a limited resource (like our single CPU). By using these tools, we ensure that even though many things are happening at once, our data stays organized and our program doesn't "crash" or give us wrong math at the end.]

---

## Part 6: GitHub Repository

**Repository URL**: https://github.com/Norah445/norahamerOS-Assignment3.git

**Number of commits**: 4

**Commit messages**: 
1. Initial Commit: Project structure and basic Round Robin logic implementation.
2. Task 1 & 2: Added shared counters and implemented fine-grained ReentrantLocks for thread safety.
3. Task 3: Implemented Binary Semaphore for CPU resource management and added execution logging.
4. Final Refactor: Added try-finally blocks for deadlock prevention and completed documentation.

---

## Summary

**Total time spent on assignment**: Approximately 8-10 hours (including coding, debugging synchronization issues, and documentation).

**Key takeaways**: 
1. Concurrency is not Parallelism: I learned that even on a single-core simulation, managing the "interleaving" of threads is crucial to prevent data
2. The Importance of Atomicity: I understood that even a simple operation like count++ is not safe in multithreading because it involves three hidden steps (read, modify, write).
3. Defensive Programming: Using try-finally blocks is a best practice that ensures system stability even when unexpected errors occur.

**Most challenging aspect**: The most challenging part was debugging the Race Conditions in the execution log. At first, some log messages were missing or appearing out of order. It took time to realize that even though the console output looked okay, the underlying ArrayList was losing data, which taught me to always protect non-thread-safe collections.

**What I'm most proud of**: I am most proud of implementing a Fine-grained locking strategy. Instead of using one big lock that slows everything down, I managed to use separate locks for each counter, which makes the simulation much more efficient and demonstrates a professional approach to thread safety.

---

**End of Documentation**
