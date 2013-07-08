Chain.js
=====

Sequencing function calls in JavaScript. 

## Examples

### #1 (simple)

	Chain.run(
		function(res, next) {
			console.log("A"); next(10);
		},
		function(res, next) {
			console.log("B", res); next(res+1);
		},
		function(res, next) {
			console.log("C", res); next();
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
	var B = function(str, callback) {
		console.log("How are " + str + "?");
		callback();
	}
	Chain.run(
		[A, "world"],
		[B, "you"],
		function(res, next) {
			next();
		}
	);

Output:

	Hello world!
	How are you?

### #3 (#1 + #2 + listening for chain ending)

	var chainFinished = function(res) {
		console.log("End", res);
	}
	var customFunc = function(arg1, arg2, callback) {
		console.log("customFunc", arg1, arg2);
		callback("result(customFunc)");
	}

	Chain.on("done", chainFinished).run(
		function(res, next) {
			console.log("A", res);
			setTimeout(function() {
				next("result(A)");
			}, 2000)		
		},
		[customFunc, "Hello", "World"],
		function(res, next) {
			console.log("B", res);
			next("result(B)");
		}
	);

Output:

	A null
	customFunc Hello World 
	B result(customFunc)
	End result(B)