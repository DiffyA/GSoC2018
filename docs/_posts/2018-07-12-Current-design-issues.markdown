---
layout: post
title:  "Current design issues"
date:   2018-07-12
---

Hello everyone! In the last post regarding the release of Syn, I mentioned a couple of issues about the current state of things under the hood. The most notable being the way the reactor is set up within the code. Let's dive a bit deeper into this.

As Syn stands, whenever some functionality is executed that depends on the underlying Soledad engine, we are indirectly using Twisted natives such as the Deferred object and the Reactor object. This can be traced back to the Soledad API which was also built around the Twisted Framework, which implies that the methods and functions of the API will return Deferred objects, making the functionality be non-blocking and allowing everything to run asynchronously. Once we obtain the Deferred object from the Soledad API, it is up to Syn to fire these Deferreds by running a reactor, and this is where the main issue is.

Let's exemplify this and take a look at one of Syn's simpler functions, the `list_entries()` function:

```
    def list_entries(self, cb=None):
        """
        Obtains the list of all entries stored in the database
        :param cb: optional callback function which receives the a tuple comprised of the amount of documents found
        as well as the list of documents itself
        """

        def default_cb(data):
            """
            Callback function for the deferred returned from Soledad.get_all_docs().
            Prints the total amount of entries to stdout as well as the names of the entries
            """
            # num_docs = data[0]
            list_docs = data[1]
            num_docs = len(list_docs)

            print ("Found a total of %s entries:" % num_docs)
            for doc in list_docs:
                print(doc.doc_id)

        d = self._list_entries()

        if cb:
            d.addCallback(cb)
        else:
            d.addCallback(default_cb)

        d.addCallback(lambda _: reactor.stop())

        reactor.run()


    @defer.inlineCallbacks
    def _list_entries(self):
        """
        Wrapper function for list_entries() method
        :param cb:
        :return:
        """
        data = yield self.soledad_client.get_all_docs()
        defer.returnValue(data)
```

Let's go step by step and see what this function is doing. If we call `list_entries()`, for example from the CLI script, the behavior is as follows:

1. `_list_entries()` is called
2. `_list_entries()` calls the `get_all_docs()` method of the Soledad object which returns a Deferred. This Deferred object will return a tuple of the form (generation, [Document]) when fired
3. The `defer.returnValue(data)` line allows us to pass the value of this deferred back to the caller
4. Back in the `list_entries()` function, `d` now contains the deferred that was returned from `_list_entries()`. 
5. The following if/else block simply checks if a custom callback function was passed along. This simply allows whoever uses the Syn API to change the behavior of this method. If no custom callbacks are provided, the `default_cb()` will be called, which prints the total number of entries found as well as the entry names. 
6. After adding the custom or default callback to the deferred's callback chain, we add yet another callback with a lambda function which stops the reactor.
7. We run the reactor.
8. Once the reactor runs, it will resolve the Deferred with the callbacks in the order they were specified. So first, it will resolve the `default_cb` (or a custom one if it was passed) by printing out the number of entries it found and their names, and afterwards it will resolve the callback with the `reactor.stop()` instruction, which, as its name implies, will stop the reactor.

The problem lies in step 8. When the `reactor.stop()` line is called, the Twisted reactor is killed, and we are not allowed to re-run this same reactor. The thing is, this reactor is a singleton, which means there can only be a single instance of this object throughout the whole process. So if we kill that reactor once we are done executing `list_entries()`, what does that mean? Well, simply put, we are not allowed to execute any other functionalities that call `reactor.run()` because the reactor instance has been killed. In fact, Twisted is so nice that it'll throw a `ReactorNotRestartable` exception so you know for a fact that no, what you are doing is very wrong!

Let's take a look at the following snippet:

```
import data
import syn_utils


soledad = syn_utils.create_client_from_file()

dm = data.DataManager(soledad)

dm.list_entries()
```

Here, we import the necessary modules, create an instance of the `Soledad` object, then we create an instance of the `DataManager` which is the class that contains the `list_entries()`, and then we call that function. The output of this snippet is the following:

```
Found a total of 2 entries:
StumbleUpon
Test
```

As we can see, the method works as expected. But what if we do some work after getting this list, and then decide we want to call the list again? Take a look at this snippet:

```
import data
import syn_utils


soledad = syn_utils.create_client_from_file()

dm = data.DataManager(soledad)

dm.list_entries()

# ... do some work ...

dm.list_entries()
```

If the problem I explained earlier made any sense at all, it is clear to see that this will result in a catastrophic exception. Let's take a look at the output of this snippet:

```
Found a total of 2 entries:
StumbleUpon
Test
Traceback (most recent call last):
  File "reactor-test.py", line 13, in <module>
    dm.list_entries()
  File "/home/victor/leap/syn/data.py", line 92, in list_entries
    reactor.run()
  File "/home/victor/leap/test-syn/local/lib/python2.7/site-packages/twisted/internet/base.py", line 1260, in run
    self.startRunning(installSignalHandlers=installSignalHandlers)
  File "/home/victor/leap/test-syn/local/lib/python2.7/site-packages/twisted/internet/base.py", line 1240, in startRunning
    ReactorBase.startRunning(self)
  File "/home/victor/leap/test-syn/local/lib/python2.7/site-packages/twisted/internet/base.py", line 748, in startRunning
    raise error.ReactorNotRestartable()
twisted.internet.error.ReactorNotRestartable

```

We can see that first, we get the entries as expected, but afterwards, we get a stack trace leading to the `ReactorNotRestartable` exception. It makes sense, because each call of the `list_entries()` method is calling `reactor.run()` and `reactor.stop()`, thus **restarting** the reactor which is not allowed in Twisted.

This might seem a bit trivial at first because why would we really want to call `list_entries()` two times in a row? Can't we store the values somewhere? Well, the problem comes when integrating with other functions such as those that allow deleting and creating entries, which all call the `reactor.run()` instruction. So for instance, if we want a program that will create more than one entry, or create an entry and then delete one, or any other combination of two or more atomic functions, we will have a glorious and catastrophic failure on our hands.

This, unfortunately, also affects other aspects of Syn...

### The second victim: Automated Testing

Yes, as the title implies, even automated testing suffers from this design drawback. Obviously, the test cases that are affected are those that face the same issue as before: the ones that start and stop the reactor. I can't point out a single point of failure where the error occurs, given the nature of the tests. Tests are executed at random, and thus can fail at different points. However, it's clear that the problem is the `ReactorNotRestartable` exception originating from this faulty design. To prove this, we can see in the following image that executing the tests all together will yield unfavorable results:

![Failure when testing automatically](https://raw.githubusercontent.com/DiffyA/GSoC2018/master/docs/_assets/Failure%20when%20testing%20automatically.png)

However, if we run the tests manually, we can see that all the tests pass:

![Successful tests](https://raw.githubusercontent.com/DiffyA/GSoC2018/master/docs/_assets/Successful%20tests.png)

Presumably, this can be fixed by addressing the reactor issue, and to do this, we will be separating the reactor calls from the already implemented Syn codebase and decoupling it into its own daemon process. This daemon process will hold the Reactor object and possibly the Soledad object, to allow for a greater decoupling of the parts. The daemon will function as a local HTTP server and the Syn app will be able to communicate with it using HTTP methods (PUT, GET, UPDATE...). 

That's all for now, thanks for reading!

-V.

