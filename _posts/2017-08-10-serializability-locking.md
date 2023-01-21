---
title: "Concurrency control"
date: "2017-08-10"
categories:
  - "software-development"
layout: post
subclass: post
navigation: "true"
cover: assets/images_1/
---

# **Concurrency control**

Imagine two users trying to access an employee table in the company's database. One is requesting the total salary of the employees in order to transfer the money into their bank accounts. The other, upon receiving an email from the boss saying that he was impressed by John and decides to give him a raise, goes on modifying John's salary cell in the database. No one knows how it goes, but suppose the transfer occured after the update. John ends up not getting the extra money that he earned this month, the guy who were told to give John more money probably is going to be fired. No one is happy.  This is also known as **the incorrect summary problem**.

Then maybe the boss rethinks his decision and finds himself being too hasty. The boss calls the other guy again and asks him to revert John's salary to normal. Suppose the money-guy saw the previous update and went on transfering money based on the database table that he's seen.  This leads to inconsistency in the realities of people involved. The boss thinks he's smart , John is happy and thinks that the boss is dumb. A transaction writes a value that later gets aborted, but another transaction accessing the same element is not aware of this change and reads the aborted value. This is known as **the dirty read problem**.

If we allow two transactions to access a database element at the same time, God knows what will happen and we can only hope that everyone gets what they want. But hoping is never economical, so we have to come up with a solution to specify what each transaction can do and to which scope.

**The solution is Concurrency Control**.  The concept of concurrency control provides rules, methods and methodologies to maintain consistency of database components.

**Concurrency control ensures that database transactions are performed concurrently without violating data integrity of the respective databases. Maror goals of concurrency control are: serializability and recoverability.** 

# **Database transactions & Schedule**

A database transaction is a unit of work that encapsulates a number of operations with defined boundaries in terms of which code are executed within that transaction. A transaction is designed to obey the **ACID** properties:

- **Atomicity**: Either everything is executed, or nothing is executed.
- **Consistency**: Every transaction must take the database from one consistent state to another consistent state.
- **Isolation**: Transactions cannot interfere with each other, and the effect of a transaction is only visible to other transactions after it's sucessfully executed.
- **Durability**: All sucessful transactions must have effects that persist even after crashes.

Database transaction, at any given moment, is in one of the following states:

![cc_1.png](images/cc_1.png)

After rollback, a transaction can be restarted at an appropate time if no internal logic occurs.

Database transactions are arranged within a **schedule**.

# **Serializability**

Before we discuss the details, first there's an obvious question: **Why would a schedule is serializable while it can just be serial ?**

If we execute transactions serially, then no problem would arise because the output of a transaction is the input of another. No inconsistencies, no abnormalities, no anomalies.

However, there are times when one transaction wants to read from the disk, and one transaction wants to use CPU to compute some value. If we only allow them to serially, then one transaction must remain idle although it does not interfere with the other transaction in any sense so there could not be any consistency. This leads to the problem of low disk utilization and low transactions througput.

That's why we would **prefer the schedule to be serializable rather than serial**. Serializability ensures that the **outcome** is **equivalent to** transactions executed **serially**. **How** the transactions are arranged **internally** **can be** **done differently** and more effectively.

There are **two major types of serializabilit**y:

- View-serializability.
- **Conflict-serializability**.

A schedule that is conflict-serializable if it's **conflict-equivalent** to a serial schedule, i.e. **there exists some swappings of non-conflicting pair of opeartions that make the schedule serial.** 

Any schedule that is conflict-serializable is view-serializable, but not necessary the opposite. Therefore we just need to ensure conflict-serializability in general.

# **Precedence graph**

We need to be able to detect conflict-serializability before we can do anything. One of the tests is **precedence graph**.

Precedenge graph for a schedule S contains:

- A **node** **for each** committed **transaction** in S.
- **An arc from T1 to T2** if an operation in **T1 precedes** and is **in conflict with** an operation in **T2**.

An operation is either a **_read_** or **_write._** Therefore it follows naturally that a pair of conflicting operations is a pair of read-write or write-read or write-write. Each of these three pair of operations, if executed in reverse order, would produce a different result. That's the whole idea of conflict.

![cc_2.png](images/cc_2.png)

How exactly does the precedence graph helps us with detecting conflict-serializability.

We need to prove the following:

- If a precedence graph **has no cycles**, then the schedule is **conflict-serializabile**.

Proof by induction:

![cc_3.png](images/cc_3.png)

- If a schedule **has a cycle**, then the schedule is **not** **conflict-serializable**.

Proof by contradiction.

![cc_4.png](images/cc_4.png)

Therefore we have proved that the precedence graph is cyclic if and only if serializability is violated.

# **Locking**

**Locking is a mechanism used to prevent inconsistencies or data corruption caused by simultaneous accessing of transactions**. A database system should be engineered in such a way that each lock is held as short as possible.

**From database's perspective**, there are **three types of lock** that is used in locking:

- **Read lock ( Share lock )**: A bunch of locks can bind to a database element. This is the kind of lock that is requested immediately before a transaction needs to read an element. Read lock is immediately released as soon as the reading is done. However, the element that is share locked cannot be exclusively locked.

- **Write lock ( Exclusive lock )**: Only one lock can bind to a database element. Write lock does not share its object with any other kind of lock. This kind of lock is requested immediately before a transaction needs to write to an element, but this lock can be released as late as the transaction's life and not necessarily immediate.

- **Update lock**: Update lock is a hybrid of read lock and write lock. A transaction requests an update lock on a database element when it **predicts** that it would want to exclusively lock the element, but it does not have to do so in the mean time.

So perhaps you're wondering: Okay if you want to read an element, use read lock on it, if you want to write to an element, use write lock on it. When you're done using the element then unlock it. No problem ! But wait, **why is update lock needed** ?

To understand this, you have to know that **most of the Database Management Systems support** **upgrade mode**: The idea is useful when you request a **SELECT** query, meaning you read the database elements without needing to modify it yet, then afterwards when you're finished calculating something you request an **UPDAE** query.  If we go with the usual types of locks ( read and write lock ), then you would read lock the object, then unlock the object, then write lock the object again.

**But things turn really nasty between the miliseconds after you release the read lock and before you start the write lock**, what if another transaction somehow gets scheduled and requests a write lock on the element. This means that we just got our needed element stolen before the our query is done. If we wait until the thief transaction finishes, then maybe the change that transaction made happened before the change we want in the UPDATE query but we actually want them executed in reverse.

There lies **the motiation** for upgrade mode: When a transaction read locks an element, and later it wants to modify that element, it waits for read locks of other transactions to be released and then it can throw in a write lock without having to release its own read lock. This way the problem we mentioned is solved.

Back to the question we were asking: **why is update lock needed** ?  There is still an issue. Suppose a schedule containing of just two transactions that have read locked an element. Now T1 wants to modify ( upgrade lock ) the element, but cannot since T2 already read locked it. So we wait until T2 finishes. T2 wants to modify ( upgrade lock ) the element , too, but cannot, since T1 already read locked it. There is no transaction left in the schedule so everything is delayed indefinitely. This is called a **deadlock**.

Here's a fine picture describing deadlock.

![cc_5.png](images/cc_5.png)

Source: [Levent Divilioglu's answer](https://stackoverflow.com/questions/34512/what-is-a-deadlock)

To resolve this issue, we need a new type of lock. But what exactly are the conditions that this new type of lock needs to meet in order to prevent deadlock. Because deadlock arises whenever two upgradable locks want to throw in a write lock on attending element, the new type of lock needs to forbid this behavior .i.e. forbid write locks. Of course write lock can resolve the issue, but it also takes away the advantage of upgradable locks ( which is allowing others to read an element until the transaction wants to perform modification ), so we want a new type of lock that both is upgradable and forbids write locks from other transactions. That is exactly what the **update lock** is**.**

**Update lock** forbids write locks from other transactions while allowing read locks, it waits for other read locks to be released before throwing out a write lock without realeasing its own read lock. Update lock helps solve deadlock.

# **Two-phase locking**

Remember the example we mentioned earlier when a transaction realeases a read lock and then in that split second before it throws a write lock another transaction goes in and write locks the element, causing undesirable result ?

Yeah well, that sort of **problem arises** whenver **a transaction unlocks an element and another transaction immediately locks the element inappropiately**. Therefore two-phase locking solves this problem by separating the locking process into two phases:

1. **Growing**: A transaction locking elements, the number of locks only increases and never decreases.
2. **Shrinking**: A transactions releasing elements, the number of locks only decreases and never increases.

It means that once a transaction releases an element, its relationship with that element ends and hence it is safe for other transactions to take control of the element.

There are 3 types of two-phase locking.

![cc_6.png](images/cc_61.png)

Most Database Management System implements Rigorous type. Therefore we would assume Rigorous 2-phase locking from now on.

Read more on 2-phase locking [here](http://scanftree.com/dbms/2-phase-locking-protocol).

# **Logging**

Concurrency control has something to do with recoverability too. Suppose a transaction has written some data to the disk and later that transaction aborts, the written data will be undone to reflect the abort state of the transaction. But what happens if a crash occurs right after that transaction has written the data. If we don't know what the transaction were doing before the crash, we have no way to tell if that transaction were going to be aborted had everything gone fine. If we don't know, then there's no reason to undo the data unwritten, this means this data, which belongs to an incorrect database state, now is part of our system. This will lead to many inconsistencies as time proceed.

This calls for a method to record exactly what the transactions are doing in real time. And **logging** is such a method.

**A log is a sequence of records about what transactions have done**. Log is stored in the main memory and will be written to disk as soon as possible, so we don't have to worry abouot log getting lost in the crash.

![cc_7](images/cc_7.png)

A **transaction log** contains four statements:

- **< start T >** 
- **< commit T >** Transaction T has completed successfully and will make no attempt to modify database elements.
- **< abort T >** Transaction T could not complete successfully.
- **< T , X , v > or < T , X , v , w >** Transaction T has changed database element X from value v to value w.

A transaction has the following **primitive operations**:

![cc_8.png](images/cc_8.png)

**Log only records when W(X,t) occur not when O(X) occur**. This means that l**og does not necessarily reflect the actual values on the disk**. When a crash happens, we cannot tell if a transaction has written a value to the disk or not from the log alone.

# **1) Undo logging**

Order of execution in undo logging:

**< T , x , val >** _**read transaction T changes x from val.**_ 

![cc_9.png](images/cc_91.png)

**Recovery with undo logging**: Classify the transactions in the log as either committed or uncomitted.

1. **If we see < commit T >** then we know from the image above that everything before < commit T > has been written to disk, so **ignore** it.
2. **If we see < start T > without a < committ T >** then transactions might have written something into disk, so we **undo it by replace X to v by looking at < T , x , val >**.
3. Then after we're done with that uncommitted transaction, we **w****rite an < abort T > at the end**.
4. **Repeat until no** transaction is **uncommitted** ( either committed or aborted ).

 

# **2) Redo logging**

Undo logging has a disadvantage which is it requires to write all the changes to disk before a transaction can commit. This means we have to access the disk everytime before transactions commit, which is costly. To avoid this, we have a different mode of logging called redo logging.

Order of execution in redo logging ( circles indicate things that differ from undo )

**< T , x , val > _read transaction T changes x to val. ( as opposed to from in undo )_**

![cc_10.png](images/cc_10.png)

**Recovery with redo logging:**

1. **Identify committed transactions**.
2. **Scan** the log from the beginning **forward**. **For each < T , X , v > encountered**:
    1. **If T is not committed, ignore**.
    2. **If T is committed, write value v to X**.
3. **For** each **incompleted transaction**, **write < abort T > and flush the log.**

# **3) Nonquescient checkpoint and recovery**

![cc_11.png](images/cc_11.png)

**In short, elements affected by T1...Tk got written into disk after the start ckpt for undo. And the elements affected by transactions before the start ckpt is written into disk for redo.** 

![cc_12.png](images/cc_121.png)

Undo logging ignores committed transactions and undo uncommitted transactions.

Redo logging ignores uncommited transactions and redo committed transactions.
