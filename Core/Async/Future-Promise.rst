Future-Promise
==============

The asynchronous execution implemented in UE4 is based on Future-Promise pattern. This is very similar to that in C++11. It is worthwhile to learn threading in c++11 first and then it will help understand the implementation of Future-Promise pattern in UE4. 

You can find the implementation of Future here.
Path: <Engine Root>/Engine/Source/Runtime/Core/Public/Async/Future.h
There are several necessary classes implemented in this source file.
They are listed as follows.

* FFutureState 
* TFutureBase
* TFuture 
* TSharedFuture
* TPromiseBase
* TPromise

**FFutureState** is a shared state associated to a Future object and a Promise object.FFutureState is a base class of derived future states. FFutureState has two members. One is a flag **Complete**, which indicates whether or not the asynchronous execution has completed. The other one is an event **CompletionEvent**, which will be fired when the async execution is completed. When the asycn execution is completed, or in other words the CompletionEvent is fired, the result stored in this state is available to access.

UE4 defined a template class named **TFutureState** which is a subclass of FFutureState.It receives a template parameter **InternalResultType** indicating the type of stored result. And it also introduces two interfaces for getting or setting result.

The code of **GetResult**::


    /**
     * Gets the result (will block the calling thread until the result is available).
     *
     * @return The result value.
     * @see SetResult
     */
    const InternalResultType& GetResult() const
    {
        while (!IsComplete())
        {
            WaitFor(FTimespan::MaxValue());
        }

        return Result;
    }

This function returns the result if the execution is completed, otherwise it will wait until it is done. When the result is available, this function will be invoked by future objects to retrieve the result.


The code of **SetResult**::

    /**
     * Sets the result and notifies any waiting threads.
     *
     * @param InResult The result to set.
     * @see GetResult
     */
    void SetResult(const InternalResultType& InResult)
    {
        check(!IsComplete());

        Result = InResult;
        MarkComplete();
    }

    /**
     * Sets the result and notifies any waiting threads (from rvalue).
     *
     * @param InResult The result to set.
     * @see GetResult
     */
    void SetResult(InternalResultType&& InResult)
    {
        check(!IsComplete());

        Result = InResult;
        MarkComplete();
    }

These two functions are invoked by promise objects to set the value of result and marks the status as complete. Note that the second overload function receives a parameter of rvalue reference which is a new future of c++11. Rvalue reference is introduced to avoid value copy in order to get better performance. Refer to standard of C++11 for details. This is not in the scope of this document.


**Future** A future is an object that can retrieve a value from some provider object or function,properly synchronizing this access if in different threads.


**TFutureBase** is a base template class for futures and shared futures.It holds a future state as a member, which is a shared pointer::

    typedef TSharedPtr<TFutureState<InternalResultType>, ESPMode::ThreadSafe> StateType;

**TFuture** is a subclass of TFutureBase for unshared future object, which provides two public interfaces. 

One is Get()::

    /**
     * Gets the future's result.
     *
     * @return The result.
     */

    ResultType Get() const
    {
        return MoveTemp(this->GetState()->GetResult());
    }

Function MoveTemp is an equvalent of std::move,which convert a reference to an rvalue reference.

The other one is Share()::

    /**
     * Moves this future's state into a shared future.
     *
     * @return The shared future object.
     */
    TSharedFuture<ResultType> Share()
    {
        return TSharedFuture<ResultType>(MoveTemp(*this));
    }

It is a interface to convert a unshared future object into shared future object.

UE4 also provides some specialization of TFuture to adapt differernt usage.


**TSharedFuture** is a shared version of future object.

**Promise** A Promise is an object that can store a value for a Future object to retrieve.The Future object can also be in another thread.

**TPromiseBase** is the base class of promise object. It holds just one member field, a share pointer to TFutureState of a specific type. It is a none copyable class which is derived from FNoncopyable,which means copy construct and assign operator are all private.

**TPromise** is the template concrete class of TPromiseBase. It provides interfaces of getting associated future object and setting result value. 


This is an article about rvalue reference and move sementics of c++11.
http://www.ibm.com/developerworks/cn/aix/library/1307_lisl_c11/









