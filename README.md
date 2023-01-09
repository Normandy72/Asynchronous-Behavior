### Before Promises - Callbacks
```
asyncFunction(function(){
    // do when asyncFunction is done
});
```
No easy and straighforward way to pass the results of asyncFunction back to its caller, especially, if the real recipient of the result ia s few layers away.

In addition, what would happen if you needed to chain a few asynchronous pieces together? For example, when asyncFunction1 would complete, instead of returning the value, you would still have to call asyncFunction2. When that would complete, you would finally move asyncFunction3, and only then deal with all the results combined together.

```
asyncFunction1(function(){
    asyncFunction2(function(){
        asyncFunction(function(){
            // do when asyncFunction is done
        });
    });
});
```
It's very hard to read this code, and that's not even showing the error handling. 


### AngularJS and New ES6 API
#### Promise
Object which can be passed around or returned that holds references to the outcome of asynchronous behavior.

In Angular, promises are created through the the `$q` service.
#### AngularJS $q Service Promise
```
function asyncFunction(){
    var deferred = $q.defer();
    if(...){ deferred.resolve(result); }
    else{deferred.reject(error); }
    return deferrer.promise;
}
```
* `var deferred = $q.defer();` - creates async environment with all the hooks into it, including the promise object.
* `deferred.resolve(result);` - marks _successful_ completion, wraps data for the promise.
* `deferred.reject(error);` - marks _unseccessful_ completion, wraps data for the promise.
* `return deferrer.promise;` - returns promise to caller (a hook back to this entire process)

This code can be done asynchronously: 
```
...
    if(...){ deferred.resolve(result); }
    else{deferred.reject(error); }
...
```
The next step is for the caller to call our asynchronous function and to capture a reference to the promise object. 
```
var promise = asyncFunction();
promise.then(function(result){
    // do something with result
},
function(error){
    // do something with error
}).then(...);
```
Note that the then function takes two arguments. Both of which are functions themselves. The argument that the promise API will send into these functions is what we resolve the rejected, the deferred object with accordingly. Result object and the error object could be just a simple string.

The `$q` service also has the capabilities to resolve multiple promises asynchronously, so no promise has to wait for another to complete in order to even start running. The `$q.all` method accomplishes that, plus the ability to have a central place to handle all the results or to handle any errors that come from any of the promises.
```
$q.all([promise1, promise2])
.then(function(result){
    // do something with result
})
.catch(function(error){
    // handle error
})
```

***
#### _Summary_
* Promises give us a lot of flexibility when dealing asynchronous behavior.
* The `$q` service is the Angular implementation of Promise API.
* Promises either get resolved or rejected.
* The `.then` method takes 2 arguments (both function values):
    * 1st - function to handle success or 'resolve' outcome;
    * 2nd - function to handle error or 'reject' outcome;
    * `.then` itself returns a Promise, so it chainable.
* `$q.all` method allows us to execute multiole promises in parallel, handling success/failure in one central place.
***

