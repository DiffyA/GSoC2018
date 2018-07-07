---
layout: post
title:  "The intricacies of yield"
date:   2018-07-06
---
Today, we'll be taking a look into a pretty handy mechanism present in the Twisted framework. I say handy now, but the reality is that the thought process behind this mechanism had been wrecking my head for the last couple of weeks. Hurray for perseverance?

Anyways, the mechanism we will talk about is that of the `inlineCallbacks` decorator that can be found readily throughout Twisted apps. However, before getting into that, we should quickly review the core concepts used in this mechanism in order to understand it from the bottom up. This means taking a look at Python's yield keyword and generator objects.

### Yields and Generators

Let's take a look at generator functions, generator objects and yields. First of all, a generator function is a function that returns a generator iterator object. In order for a function to return a generator iterator function, it must have a `yield` keyword somewhere in its body. Evidently, these concepts go hand in hand: we can't have a generator function without having a yield, and a generator function implies returning a generator object or generator iterator. 

It's probably easier to understand with some simple code. Take a look at the following snippet:

```
def func():
    return 0

def generator_func():
    yield 0

if __name__ == '__main__':

    value = func()

    print(type(value))

    value = generator_func()

    print(type(value))
```

We have defined two functions: `func()` which simply returns `0`, and `generator_func()` which yields `0`. If you run this piece of code, we will see that their types are indeed different. On the one hand, `func()` will return an `int` type, but on the other hand, `generator_func()` will return a `generator` type. We can see that in the following output:

```
<type 'int'>
<type 'generator'>
```

The implication of having a `generator` type returned from `generator_func` is interesting, too, because it means that we don't really have the value `0` assigned to the `value` variable just yet. What we have is a generator object, and in reality, `generator_func()` has not even been executed yet.

To prove this, we can take a look at the following code sample:

```
def generator_func():

    print("Ha! This won't run until you try something different!")
    yield 0

if __name__ == '__main__':

    value = generator_func()
    print(value)
```

If `generator_func()` was NOT a generator function, it's pretty clear that we would see the output of that print statement when we ran this script. However, the output of this script is the following:

```
<generator object generator_func at 0x7f2e2490e0a0>
```

Hmm... fun. We get the object reference. Well, that's not very helpful if we want to access the `0`, but we are on the right track. We can use the `.next()` method of the generator object in order to start the execution. Now take a look at the following script:

```
def generator_func():

    print("That's more like it!")
    yield 0

if __name__ == '__main__':

    gen = generator_func()
    value = gen.next()
    print(value)
``` 

Now this starts to make more sense. The output of this script is as follows:

```
That's more like it!
0
```

The first line is due to the `print()` statement inside `generator_func()`, and the second line is due to the `print(value)` in the main function.

Now that we know how to get generator functions running and get some value from them, let's recap:
+ A generator function is a function that has a `yield` instead of a `return`
+ Calling a generator function will return an object of type `generator`, also called generator iterator
+ Once we have the `generator`, we can iterate through it using `.next()` to obtain its values.

Having those basic definitions out of the way, we can start digging into the good stuff. A generator function can be, in a sense, "paused and resumed". In other words, the state of the local variables inside of a generator function are saved from one execution to another, as well as the point of execution. The "pausing points" inside of a generator function are the yield instructions. Once execution reaches a yield, the value is returned to the caller function (if the value even exists), and the control flow is also returned to the caller function. To resume execution of the generator function, we simply call it again using `.next()` on its associated `generator` object, and we can marvel in the wonders of programming as execution resumes from the last yield instruction. 

As with most programming concepts, this is probably better explained with an example. Take a lookt at the following snippet:

```
def generator_func():

    print("Hello! First stop here we go.")
    yield

    print("We just resumed execution from the last yield. Nice.")
    yield

    print("Ok. If you call me again, I'm going to freak out and throw an exception.")
    yield

if __name__ == '__main__':

    gen = generator_func()

    gen.next()
    gen.next()
    gen.next()
```

In this example, we demonstrate the concepts explained earlier along with the "pause/unpause" functionality of generators. First, we obtain the `generator` object in the `gen` variable, and then we simpy proceed to execute the function by calling `.next()` repeatedly. The first call to `.next()` will execute up to the first yield in the function, printing `Hello! First stop here we go.` to the standard output. The second call will print `We just resumed execution from the last yield. Nice.` to the standard output. Finally, and predictably, another execution will print `Ok. If you call me again, I'm going to freak out and throw an exception.` to the standard output. But what's that about? Is that last print message a threat or good-willed advice? Well, a bit of both. See, if we were to call `gen.next()` again, there would be no more function to run! This would raise a `StopIteration` exception. Of course, we could catch these exceptions and continue with our logic, but that falls outside of the scope of this post.

To keep adding to this interesting concept of generators and yields, we can extend the functionality presented to also include returning actual values in the yield statements. If we do so, the yields would work as "pausing points" as well as return a value to the calling function. Let's take a look at the following interesting use of generators:

```
def generator_powers():
    n = 0
    while True:
        yield 2 ** n
        n += 1

if __name__ == '__main__':

    gen = generator_powers()

    for i in range(200):
        print(gen.next())
```

In this case, the generator function `generator_powers()` simply keeps generating the value of `2^n` starting from `n=0`. Every time it is called, `n`is increased by 1 and the result of the operation is returned. In the main function, we obtain the generator object and call the function repeatedly in a for loop. This behavior basically generates the values of a list on-the-fly and is generally more efficient.

### Enter Twisted's inline callbacks

Now that we have refreshed on some of the functionality of generators and yields, let's get into Twisted's mechanisms. We'll look into the inline callbacks functionality, which can be useful but also very convoluted when first starting to use it. This was my case, at least, and I still have to take out the good old paper and pencil to work things out when using this in Syn.

Basically, we can apply the inline callback functionality to a generator function by prepending the signature of the function with the @inlineCallbacks decorator. In doing so, we drastically change the behavior of the generator function. This is meant to be used with Twisted's Deferred objects and asynchronous philosophy, so in the end it makes sense. Some of the biggest changes include:

1. Yields will no longer pause the execution of the generator function UNLESS we are yielding a `deferred`. This is because a deferred object returns a value once it is resolved, but it does not necessarily have to be resolved right away. The fact that yielding a deferred means we have to wait for that same deferred to be resolved converts the original generator function into a `deferred` itself, because it must wait for internal promises to be resolved. You can try this for yourself! Try running the upcoming code sample without the `@defer.inlineCallbacks` decorator to see how it gets stuck (it is waiting for a `.next()` that never comes).
2. There is no need to call `.next()`. The generator will attempt to run the function until the end, although it does not guarantee it will finish because it might have internal deferreds which must also be resolved.
3. Piggybacking from point 1, calling an inline callback function that yields a deferred WILL RETURN A DEFERRED.

Again, let's take a look at some code:

```
from twisted.internet import reactor, defer
from twisted.internet.defer import Deferred


@defer.inlineCallbacks
def inline_callback():
    print ("Running the function. Notice that it will not pause even though there is a yield right here!")
    result = yield 1  # yields that are not deferred types will not pause the execution

    print("See? No pause. Also, the previously yielded value is: {}".format(result))

    print("Let's create a deferred, tell the reactor to fire it in 5 seconds and yield it. We will be forced to pause!")
    d = Deferred()
    reactor.callLater(5, d.callback, 2)  # Fire the deferred in 5 seconds with an argument of 2

    print("Attempting to yield the deferred. Will keep flowing once it has been resolved.")
    result = yield d  # yielded deferreds pause the generator. Result will obtain the result of the resolved deferred.

    print ("With the deferred resolved, we have its value: {}".format(result))  # the result of the deferred


if __name__ == '__main__':

    d = inline_callback()  # This is actually a deferred as well!

    print("Check that deferred object reference! {}".format(d))  # Proof that calling an inlineCallback returns a deferred

    d.addCallback(lambda _: reactor.stop())

    reactor.run()
```

Let's take a look at the main function and go step by step. The first thng we do is call `inline_callback()`. This automatically starts the `inline_callback()` function (remember, this is not a generator function anymore, there is no need for a `.next()`). We can see how the first print statement is executed, as well as the second one with the `1` value that was previously yielded. This means, as stated above, that yields no longer pause execution, unless they are deferreds. 

Afterwards, the third print statement is executed, a deferred object is created, and the reactor is told to execute that deferred's callback function in `5` seconds and pass it a value of `2`. Now things get interesting. After another print statement, we attempt to yield the deferred object that was just created. However, it has not been resolved yet! The `5` seconds have not passed and as such, execution in this function pauses. When execution pauses, the main function regains control and we can see that its print statement is executed. The output contains the object reference and we can confirm, in fact, that we obtained a deferred from executing `inline_callback()`. Afterwards, we add a callback to that deferred indicating it to shut down the reactor once it has been resolved. However, it will not be resolved until the yield that paused the execution of `inline_callback()` is also resolved, which will happen once the '5' seconds are up. Once this happens, the last print of the `inline_callback()` function is executed and we print its result. Having no more blocking code in this funtion, the execution of `inline_callback()` finishes and the code in the main function is also unblocked as everything has been resolved. The only thing that happens next is that the reactor is stopped thanks to the last callback that was added.

I might be making things more complicated than they really are, but that is just my understanding of inline callbacks in Twisted. They get even nastier, too! For example, if we want to chain callbacks and not lose the data along the way, we have to use `defer.returnValue(value)` inside of the `inline_callback()` function to allow callbacks added to the deferred generated from executing `inline_callback()` to have access to the results it obtained. This is extremely useful and has been used numerous times in Syn's codebase, as it allows to add any other callback function to the deferred's callback chain, allowing other programs to build on top of Syn's API.

Just for completeness' sake, I will paste a code snippet with this functionality, but will not go into very great detail as it is quite similar to the previous:

```
from twisted.internet import reactor, defer
from twisted.internet.defer import Deferred



@defer.inlineCallbacks
def inline_callback():
    print ("Running the function. Notice that it will not pause even though there is a yield right here!")
    result = yield 1  # yields that are not deferred types will not pause the execution

    print("See? No pause. Also, the previously yielded value is: {}".format(result))

    print("Let's create a deferred, tell the reactor to fire it in 5 seconds and yield it. We will be forced to pause!")
    d = Deferred()
    reactor.callLater(5, d.callback, 2)  # Fire the deferred in 5 seconds with an argument of 2

    print("Attempting to yield the deferred. Will keep flowing once it has been resolved.")
    result = yield d  # yielded deferreds pause the generator. Result will obtain the result of the resolved deferred.

    print ("With the deferred resolved, we have its value: {}".format(result))  # the result of the deferred

    # Returns the value so we can chain callbacks
    defer.returnValue(result)

if __name__ == '__main__':

    def cb(data):
        print("The data returned from the deferred is: {}".format(data))

    d = inline_callback()  # This is actually a deferred as well!

    print("Check that deferred object reference! {}".format(d))  # Proof that calling an inlineCallback returns a deferred

    d.addCallback(cb)
    d.addCallback(lambda _: reactor.stop())

    reactor.run()
```

To see the difference, simply comment the line that says `defer.returnValue(result)` so you can see how the `cb()` callback added to the deferred does not have access to the result of the inner deferred.

That's all for now regarding yields, generators, and Twisted's inline callbacks, but trust me, I will be dealing with them for a long long time!

### That's great, but, why use them?

Generally, generators are more memory efficient when we want to retrieve a certain item in very large lists. For example, let's say we want to find a single specific element in a huge list and we have a good-old-fashioned function that obtains this list and returns it. If we call this function, besides the overhead of creating the list in the first place, we will have a lot of memory taken up by the variable that holds the list. Afterwards, we would still have to operate on the list in order to find the element we are looking for. On the other hand, if we use a generator function, we can simply create the list on-the-fly, element by element, and return each element separately. Once we find the element we want, we can stop calling the generator function and save resources.

Another use case is when writing test cases using the Pytest framework. A common operation when testing an application is to previously set up an enclosed test environment, and later clean it up once the tests have finished in order the leave the application in a consistent state. The granularity of the setup and teardown operations can be adjusted (setup and teardown after every test? Once for every group of tests?), but what makes yields useful in this case is that we can define the setup and teardown portions in the same function, and allow them to execute in order. The flow looks something like this:

```
@pytest.fixture(scope='module')
def setup_and_clean():
    # setup
    ...
    
    yield
    
    # teardown
    ...

def test_01(setup_and_clean):
    ...

def test_02(setup_and_clean):
    ...
```

I won't go into too much detail given that testing falls way outside of the scope of this post, specifically testing associated to a specific framework, but its helpful to know that by using yields, the flow of this test script would be as follows:

1. Execute the setup code once before all the tests. Pause `setup_and_clean()` once the yield is reached. It is even possible to return interesting objects here such as those allowing connections to databases.
2. Execute all the tests in a random order. Once done, `setup_and_clean()` will resume from the yield.
3. Execute the teardown code after all tests have executed.

More information of this cleanup method can be found (here)[https://docs.pytest.org/en/latest/fixture.html].

### Thank you for sticking through my longest post so far! I hope it made sense!

-V.
