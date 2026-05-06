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

[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 

**Why they need protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #2: Execution Log

**What resource**: 

**Why it needs protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 

**Number of permits and why**: 

**Where implemented**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```

**Results**: 
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 

**Results**: 

**What this proves**: 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
