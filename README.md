# README: Java Threading Concepts

## Table of Contents
- [Introduction to Threads in Java](#introduction-to-threads-in-java)
- [Making a Class a Thread in Java](#making-a-class-a-thread-in-java)
- [Using the Sleep Keyword in Threading](#using-the-sleep-keyword-in-threading)
- [Synchronization in Threading](#synchronization-in-threading)
- [Understanding Reentrant Lock in Threading](#understanding-reentrant-lock-in-threading)
- [Understanding wait, notify, and notifyAll in Threading](#understanding-wait-notify-and-notifyall-in-threading)
- [Understanding Executor Service in Threading](#understanding-executor-service-in-threading)

## Introduction to Threads in Java
Welcome to the README file for understanding threads in Java! In this document, we'll explore the concept of threads in Java programming using a simple language.

Threads are like little workers in a big factory (your Java program). They help your program do multiple things at once, making it faster and more efficient. Imagine you're cooking dinner in your kitchen. You might be chopping vegetables while the water is boiling on the stove. These tasks happen simultaneously, just like threads in Java.

In this README, we'll cover what threads are, why they're useful, and how to use them in your Java programs. Whether you're a beginner or an experienced developer, understanding threads is crucial for building efficient and responsive applications.

## Making a Class a Thread in Java
In Java, you can turn a class into a `thread` by extending the Thread class or implementing the `Runnable` interface. Here's how you can do it:

### Extending the Thread Class
```java
public class MyThread extends Thread {
    @override
    public void run() {
        // Code to be executed by the thread
        System.out.println("MyThread is running");
    }

    public static void main(String[] args) {
        MyThread thread = new MyThread();
        thread.start(); // Start the thread
    }
}
```
In this example, `MyThread` extends the Thread class and overrides the `run()` method. Inside the `run()` method, you write the code that you want the thread to execute. To start the thread, you create an instance of `MyThread` and call the `start()` method.

### Implementing the Runnable Interface
```java
public class MyRunnable implements Runnable {
    @override
    public void run() {
        // Code to be executed by the thread
        System.out.println("MyRunnable is running");
    }

    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread thread = new Thread(myRunnable); // Create a thread
        thread.start(); // Start the thread
    }
}
```
In this example, `MyRunnable` implements the Runnable interface and provides an implementation for the `run()` method. Then, you create a Thread object and pass an instance of `MyRunnable` to its constructor. Finally, you start the thread using the `start()` method.

Both approaches achieve the same goal of creating a thread. Extending the Thread class is simpler but limits your class to be a thread only, while implementing the Runnable interface allows your class to extend other classes as well, providing more flexibility in your program's design.

## Using the Sleep Keyword in Threading
In Java threading, the `sleep()` method is used to pause the execution of a thread for a specified amount of time. This can be useful for various purposes such as timing operations, controlling the rate of execution, or introducing delays in a program.

Here's how you can use the `sleep()` method:
```java
public class MyThread extends Thread {
    @override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println("Thread is running");
            try {
                Thread.sleep(1000); // Sleep for 1 second (1000 milliseconds)
            } catch (InterruptedException e) {
                System.out.println(e.getMessage());
            }
        }
    }

    public static void main(String[] args) {
        MyThread thread = new MyThread();
        thread.start(); // Start the thread
    }
}
```
In this example, the `run()` method of the `MyThread `class runs a loop for five iterations. Inside the loop, the thread prints a message and then calls Thread.`sleep(1000)` to pause execution for one second (1000 milliseconds). The `sleep()` method may throw an `InterruptedException`, so it needs to be handled properly.

## Synchronization in Threading
In Java threading, synchronization is a technique used to control access to shared resources by multiple threads. When multiple threads access shared data concurrently, it can lead to data corruption or inconsistency if not synchronized properly. The `synchronized` keyword helps ensure that only one thread can access a block of code or an object's critical section at a time.

Here's how you can use the `synchronized` keyword:

### Synchronizing Methods

```java
public class Counter {
    private int count = 0;

    // Method to increment the count
    public synchronized void increment(int count) {
        try {
        for(int i=0;i<=count;i++){
            count*=i;
        }
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Incremented by Thread " + Thread.currentThread().getName() + ": " + count);
    }

    // Method to get the current count
    public synchronized int getCount() {
        return count;
    }
}
```
In this example, the `increment()` and `getCount()` methods of the Counter class are `synchronized`. This means that only one thread can execute these methods at a time. It prevents multiple threads from simultaneously modifying or reading the count variable, ensuring data consistency.

### Synchronizing Blocks

```java
public class MyThread extends Thread {
    private Counter counter;

    public MyThread(Counter counter) {
        this.counter = counter;
    }
    @override
    public void run() {
        synchronized (counter) {
            counter.increment(10);
        }
    }
}

public class MyThread2 extends Thread {
    private Counter counter;

    public MyThread2(Counter counter) {
        this.counter = counter;
    }
    @override
    public void run() {
        synchronized (counter) {
            counter.increment(5);
        }
    }
}
```
In this example, the `run()` method of the `MyThread` class increments the `Counter` object's count. Instead of synchronizing the entire method, a synchronized block is used to synchronize only the critical section where the `increment()` method is called. This allows other threads to access non-critical sections of the code simultaneously, improving performance.

Finally, let's create a `Main` class to demonstrate the usage of the `Counter` and `MyThread` classes.
```java
public class Main {
    public static void main(String[] args) {
        Counter counter = new Counter();
        MyThread thread1 = new MyThread(counter);
        MyThread2 thread2 = new MyThread2(counter);

        thread1.start();
        thread2.start();
    }
}
```
In the `Main` class, we create an instance of the `Counter` class and pass it to two `MyThread` and `MyThread2` instances. Both threads share the same `Counter` object. When the threads start, they concurrently execute their `run()` methods, calling the `increment()` method on the shared Counter object.

## Understanding Reentrant Lock in Threading
In Java threading, a reentrant lock is a synchronization mechanism that allows a thread to acquire the same lock multiple times without causing a deadlock. This concept is particularly useful when dealing with complex synchronization scenarios where a thread might need to enter the same synchronized block or method recursively.

Let's delve deeper into the concept of a reentrant lock:

### What is a Reentrant Lock?
A reentrant lock, also known as a recursive lock, is a synchronization primitive that provides exclusive access to a shared resource. Unlike synchronized blocks or methods, which are reentrant by default, reentrant locks offer explicit control over locking and unlocking operations.

### How Does a Reentrant Lock Work?
A reentrant lock allows a thread to acquire the lock multiple times. Each time a thread acquires the lock, it must release it the same number of times to fully unlock the resource. This behavior prevents deadlock situations where a thread holds a lock indefinitely, blocking other threads from accessing the resource.

### Example Using Reentrant Lock

```java
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockExample {
    private static ReentrantLock lock = new ReentrantLock();

    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            lock.lock();
            try {
                System.out.println("Thread 1 acquired the lock");
                // Do some work
                lock.lock(); // Acquiring the lock again
                try {
                    System.out.println("Thread 1 reacquired the lock");
                    // Do some more work
                } finally {
                    lock.unlock(); // Release the lock
                    System.out.println("Thread 1 released the lock");
                }
            } finally {
                lock.unlock(); // Release the lock
            }
        });

        Thread thread2 = new Thread(() -> {
            lock.lock();
            try {
                System.out.println("Thread 2 acquired the lock");
                // Do some work
            } finally {
                lock.unlock(); // Release the lock
                System.out.println("Thread 2 released the lock");
            }
        });

        thread1.start();
        thread2.start();
    }
}
```
In this example, we create two threads (`thread1` and `thread2`) that attempt to acquire the reentrant lock. `thread1` acquires the lock twice (once in the outer scope and once in the inner scope), demonstrating the reentrant nature of the lock. Meanwhile, `thread2` acquires and releases the lock once.

## Understanding wait, notify, and notifyAll in Threading
In Java threading, `wait`, `notify`, and `notifyAll` are methods provided by the `Object` class for inter-thread communication and synchronization. These methods are used in conjunction with the `synchronized` keyword to coordinate the execution of multiple threads and avoid race conditions.

Let's explore these concepts in more detail:

### 1. wait()
The `wait()` method causes the current thread to wait until another thread invokes the `notify()` or `notifyAll()` method for the same object. When a thread calls `wait()`, it releases the lock it holds, allowing other threads to acquire it and make progress.

### 2. notify()
The `notify()` method wakes up a single thread that is waiting on the object's monitor. If multiple threads are waiting, it's arbitrary which one gets awakened. The awakened thread must reacquire the lock before it can continue execution.

### 3. notifyAll()
The `notifyAll()` method wakes up all threads that are waiting on the object's monitor. After `notifyAll()` is called, the awakened threads compete for the lock.

### Example Using wait, notify, and notifyAll
```java
public class WaitNotifyExample {
    private static final Object lock = new Object();
    private static boolean flag = false;

    public static void main(String[] args) {
        Thread waiterThread = new Thread(() -> {
            synchronized (lock) {
                while (!flag) {
                    try {
                        System.out.println("Waiter thread is waiting...");
                        lock.wait(); // Wait until notified
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.println("Waiter thread is notified. Continuing execution...");
            }
        });

        Thread notifierThread = new Thread(() -> {
            synchronized (lock) {
                System.out.println("Notifier thread is performing some task...");
                // Simulating some task
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                flag = true;
                lock.notify(); // Notify one waiting thread
                // lock.notifyAll(); // Alternatively, you can use notifyAll() to notify all waiting threads
            }
        });

        waiterThread.start();
        notifierThread.start();
    }
}
```
In this example:

We have a shared object `lock` used for synchronization. The `waiterThread` waits until the flag becomes true, and it releases the lock by calling `wait() `on lock.The `notifierThread` performs some task, sets a flag to true, and notifies one waiting thread using `notify()` on lock.

You can also replace `lock.notify()` with `lock.notifyAll()` to notify all waiting threads instead of just one. However, in this case, since there's only one waiting thread, using either `notify()` or `notifyAll()` achieves the same result.

## Understanding Executor Service in Threading
In Java, the Executor framework provides a higher-level abstraction for managing and executing asynchronous tasks concurrently. The `ExecutorService` interface, part of the Executor framework, simplifies the task of executing tasks in parallel and provides features like thread pooling, task scheduling, and task submission.

Let's explore the concepts of Executor Service in more detail:

### 1. ExecutorService Interface
The `ExecutorService` interface represents an asynchronous execution service that manages a pool of threads and executes submitted `Runnable` tasks or `Callable` tasks.

#### Key Methods:
- `submit(Runnable task)`: Submits a Runnable task for execution and returns a Future representing the task's result.
- `shutdown()`: Initiates an orderly shutdown of the ExecutorService, allowing previously submitted tasks to execute before terminating.
- `shutdownNow()`: Attempts to stop all actively executing tasks, halts the processing of waiting tasks and returns a list of tasks that were awaiting execution.

### 2. ThreadPoolExecutor Class
The `ThreadPoolExecutor` class is a concrete implementation of the `ExecutorService` interface, providing a flexible and configurable thread pool for executing tasks.

#### Key Parameters:
- `Core Pool Size`: The number of threads to keep in the pool, even if they are idle.
- `Maximum Pool Size`: The maximum number of threads to allow in the pool.
- `Keep Alive Time`: The maximum time that excess idle threads will wait for new tasks before terminating.

### Example Using Executor Service
```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadListExample {
    public static void main(String[] args) {
        List<Thread> threads = new ArrayList<>();
        
        // Adding 5 threads to the list
        for (int i = 1; i <= 5; i++) {
            final int threadId = i;
            Thread thread = new Thread(() -> {
                System.out.println("Thread " + threadId + " started by " + Thread.currentThread().getName());
                // Simulate some thread execution time
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("Thread " + threadId + " completed by " + Thread.currentThread().getName());
            });
            threads.add(thread);
        }
        
        // Create an ExecutorService with a fixed-size thread pool of 2 threads
        ExecutorService executor = Executors.newFixedThreadPool(2);
        
        // Submit threads for execution
        for (Thread thread : threads) {
            executor.submit(thread);
        }
        
        // Shutdown the ExecutorService
        executor.shutdown();
    }
}
```
In this example:

We create a list of threads containing instances of the Thread class. Each thread in the list simulates some work by printing a start message, sleeping for 1 second, and then printing a completion message. We create an ExecutorService with a fixed-size thread pool of 2 threads using Executors.newFixedThreadPool(2). We submit all threads for execution using the executor. submit(thread).The ExecutorService ensures that at most two threads execute concurrently due to the fixed-size thread pool. Finally, we shut down the ExecutorService after all threads have been submitted.
