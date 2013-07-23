## doctest.js

For a more complete description please [read the main
page](http://doctestjs.org).

`doctest.js` is a test runner for Javascript, organized around *examples* and *expected result*.  Tests look like this:

```javascript
// Simple stuff:
print(3 * 4);
// => 12

// Or complicated stuff:
var complete = false;
var savedResult = null;
$.ajax({
  url: "/test",
  dataType: "json",
  success: function (result) {
    complete = true;
    savedResult = result;
  }
});
wait(function () {return complete;});
print(savedResult);
// => {value1: "something", value2: true}
```

And a bunch more features: check out the [tutorial](http://doctestjs.org/tutorial.html) to get started, or read the [reference](http://doctestjs.org/reference.html) for more detail.

## Fork

The purpose of this fork is to experiment with making the framework a bit more
customizable. In particular, I wanted to incorporate [test fixtures](http://en.wikipedia.org/wiki/Test_fixture) and be able to invoke them
declaratively within the test suite comments. This is practical need that arose
from an ongoing project and I'm sure I'll have others. I haven't contacted the original author about this, but I probably will at some point.

To handle this situation, I've implemented the ability to add custom hooks (or callback) before and after each test:

```javascript
  doctest.addRunHook(beforeTest, afterTest);

  // the beforeTest and afterTest function could be like the "setup" and
  // "teardown" of unit test frameworks:
  function beforeTest(E) {
    // Here E is the full test script expression, comments and all, so you could
    // parse it and do whatever. 
    //
    // 'this' is the same contextual object the test will be invoked with, so
    // if you want to pass some variable to the test you could use 'this', e.g.:
    this.repeatCount = 100;
  }
```
The change is really small, and the whole thing dealing with fixtures is a separate library that I'll publish once it matures. But roughly it lets you define fixtures like this:

```javascript
  T.fixt("TestUser").do(function () {
    this.user = server.postObject('/user/register', 
                 {email:'user@test.org',
                  firstName:'Testing', 
                  lastName:'Guru', 
                  password:'password'});        
  }).undo(function () {
    server.del('/data/user/user@test.org');
  });
```

The above defines a test fixtures called "TestUser" that registers a new test user with the server and then deletes it to cleanup the fixture. So now a fixture is a named component that can be attached to various tests. The way this integrates with doctestjs is by specifying which fixtures the following test needs in a comment:

```javascript
// @TestUser

print(user.email);

// => user@test.org

```

If you stumble upon this fork and really want this fixture code now, drop me a note.

## License

Doctest.js is released under an MIT-style license.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
