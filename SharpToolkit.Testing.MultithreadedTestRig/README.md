# Multithreaded Test Rig

Allow to easily define the conditions for tests that require concurrency. It helps to coupe with test runner not handling assertions, exceptions and contracts outside the main thread.

The usage is pretty simple:

```CSharp
// The TestContext is the type of class that would be used to
// interact with the threads.
var rig = new MultithreadedTestRig<TestContext>();

// Any number of threads can be created.
rig.CreateThread(
    // The code that the thread will run.
    ctx =>
    {
        while (true)
        {
            Thread.Sleep(100);
            if (ctx.CancellationRequested)
                break;
        }
    },
    // The code that will execute when the rig will ask
    // to abort the execution.
    ctx =>
    {
        ctx.CancellationRequested = true;
    },
    // The context
    new TestContext(),
    // Optional thread name.
    "Thread name"
);

rig.StartThreads();
rig.WaitForThreads();

// Success will false if an exception is thrown
// a Contract has failed.
rig.Success.Should().BeTrue();
rig.CaughtException.Should().BeNull();
```