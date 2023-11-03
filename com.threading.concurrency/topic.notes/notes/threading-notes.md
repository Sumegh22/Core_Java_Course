# Notes - Multithreading and Concurrency

### True Parallelism vs Logical Parallelism**

* True Parallelism vs Logical Parallelism
* True parallelism is achieved by assigning tasks to individual CPUs or Processors. This is possible through multicore processors or executing the tasks using multiple CPUs.

* If there is one CPU and you want to perform multiple tasks in parallel, then CPU will be shared across those tasks for some stipulated time interval, which stands for interleaved execution, and this way of executing the tasks is known as logical parallelism or psuedo parallelism.

* In case of logical parallelism, lets assume there are two tasks T1 and T2, when executed in parallel and using one CPU, CPU is switched between T1 and T2 i.e. CPU executed T1 for some stipulated time interval and then switched to T2. Once T2's time slice is completed then it is switched back to T1 and starts from where it stops.



**Example -**
* In the below example Task is a Thread(explained later), and run is the entry point of the thread execution where it starts printing 1500 T's.

* main() runs in Thread i.e. the Main thread which is started by the JVM.

      Note In the main method we are not calling doTask directly, instead we are using the start() method of the Thread class, which runs the Task using a separate Thread.



class Task extends Thread {

    // Thread execution begins here.
    public void run() {
        // performs the task i.e. prints 1500 T's
	doTask(); 
    }
	
    public void doTask() {
	for(int i=1; i <= 1500; i++) {
	    System.out.print("T");
	}
    }
}

public class Main {

    // Runs with in the Main thread started by JVM.
    public static void main(String[] args) {
		
	Task t1 = new Task();
        // Starts a separate Thread using the 
        // the start method of the Thread class.
	t1.start(); 
 
	// runs in the Main thread and prints 1500 M's
	for(int i=1; i <= 1500; i++) {
	    System.out.print("M");
	}	
    }
}


Here main() and Task are run using two separate threads, which means they are executed in parallel (logical parallelism in case of single CPU) and hence you will see output like MMMTTTMMMTTT...

=============================================================================================================================

## Designing Threads

**Thread -**
A thread is a light weight process, it is given its own context and stack etc. for preserving the state. Thread state enables the CPU to switch back and continue from where it stopped.

**Creating Threads in Java -**
When you launch Java application, JVM internally creates few threads, e.g. Main thread for getting started with main() and GarbageCollector for performing garbage collection and few other threads which are need for execution of a Java application.

You can create a thread and execute the tasks with in the application, this enables you to perform parallel activities with in the application.

There are two approaches,

1) Extending the Thread class and performing the task. This is not a preferred approach because you are not extending the Thread functionality, instead you are using the Thread to execute a task, hence you should prefer the second approach.

2) Implementing the Runnable interface and then submitting this task for execution. Similarly there is a Callable interface(explained later) as well.



run() method -

Once you choose your approach, you can consider the run() method as the entry point for thread execution. To simplify just think like main() for a program, run() for a thread.

start() method -

Execution of the thread should be initiated using the start() method of the Thread class. This submits the thread for execution. This takes the associated thread to ready state, this doesn't mean it is started immediately. i.e. in simple terms, when you call the start() method, it marks the thread as ready for execution and waits for the CPU turn.



**1. Extending the Thread class.**
class MyThread extends Thread {

	// Thread execution begins here.
	public void run() {
		// DO THAT TASK HERE.
		for(int i=0; i <= 1000; i++) {
			System.out.print("T");
		}
	}

}

**2. Implementing the Runnable interface,**
// This marks this class as Runnable and
// assures that this class contains the run()
// method. Because any class implementing the
// interface should define the abstract method
// of the interface, otherwise it becomes abstract.

    class MyTask implements Runnable {

    // Thread execution begins here.
    @Override
    public void run() {
	// DO THAT TASK HERE.
	for(int i=0; i <= 1000; i++) {
	    System.out.print("-");
	}
    }
    }
-----------------------------------------------------------------------------------------------------

    public class Main {
    // Will run with in the main thread.
    public static void main(String[] args) {
		
	// Because MyThread extends the Thread
	// class, you can call the start() method
	// directly, as it is also a member of this
	// class, courtesy inheritance relation.
	MyThread thr = new MyThread();
	thr.start();
		
	// MyTask is a Runnable task and not a 
	// Thread and hence we need to create a 
	// Thread object and assign it the task 
	// Note here we are calling the start 
	// method over Thread object and not on 
	// task object.
	MyTask task = new MyTask();
	Thread thr2 = new Thread(task);
	thr2.start();
		
	for(int i=0; i <= 1000; i++) {
	    System.out.print("M");
	}	
    }
    }


There are three threads in the above program (system threads ignored). One the Main thread that prints "M" and thr that prints "T" and thr2 that prints "-". Because they are executed in parallel, you will see the output like MMMTTT---MMMTTT--- ...

