Chain.js
=====

Sequencing function calls in JavaScript. 

## Examples

### #1 (simple)

	Chain()(
        function(res, chain) {
            console.log("A"); chain.next(10);
        },
        function(res, chain) {
            console.log("B", res); chain.next(res+1);
        },
        function(res, chain) {
            console.log("C", res); chain.next();
        }
    );

Output:

	A
	B 10
	C 11

### #2 (passing function as array)

	var A = function(str, callback) {
        console.log("Hello " + str + "!");
        callback();
    }
    var B = function(str1, str2, callback) {
        console.log("How " + str1 + " " + str2 + "?");
        callback();
    }
    Chain()(
        [A, "world"],
        [B, "are", "you"],
        function(res, chain) {
            chain.next();
        }
    );

Output:

	Hello world!
	How are you?

### #3 (#1 + #2 + listening for chain ending)

	var chainFinished = function(res, chain) {
        console.log("End", res);
    }
    var customFunc = function(arg1, arg2, callback) {
        console.log("customFunc", arg1, arg2);
        callback("result(customFunc)");
    }

    Chain()("done", chainFinished)(
        function(res, chain) {
            console.log("A", res);
            chain.next("result(A)");
        },
        [customFunc, "Hello", "World"],
        function(res, chain) {
            console.log("B", res);
            chain.next("result(B)");
        }
    );

Output:

	A null
	customFunc Hello World 
	B result(customFunc)
	End result(B)

### #4 (error reporting)

	var operationA = function(res, chain) {
        chain.error({message: "Error message from operation A"}).next();
    }
    var operationB = function(res, chain) {
        chain.error({message: "Error message from operation B"}).next();   
    }
    Chain()("done", function(res, chain) {
        if(errors = chain.error()) {
            console.log("Yes, there are some errors", errors);
        }
    })(
        operationA,
        operationB
    );

Output:

	Yes, there are some errors
	[Object, Object]