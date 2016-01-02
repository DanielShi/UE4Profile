Async
=====

There are 3 kinds of Asynchronous execution methods in UE4, which handles different situations.

Find the source in Path: <Engine Root>/Engine/Source/Runtime/Core/Public/Async/Async.h

* **Task Graph**. This is for short running tasks.
* **Thread**. It executes in sperate thread for long running tasks.
* **ThreadPool**. It executes in queued thread pool. 

To create a asychronous task, there is an unified interface for it::

    template<typename ResultType>
    TFuture<ResultType> Async(EAsyncExecution Execution, TFunction<ResultType()> Function)

It returns a future object which contains the result. It receives two arguments. The first one determines which way to use, task graph, thread or thread pool.The second argument is an execution function that does the real work. 

It works pretty much like std::async function in c++11. 

There is an another option to create asynchronous task named **FAsyncTask**. It is built on thread pool. It is a template class and receives a template parameter **TTask**. TTask has a function named DoWork() that does the real work.

Next, we will go deeper of each of the methods and see how they are implemented in UE4.



