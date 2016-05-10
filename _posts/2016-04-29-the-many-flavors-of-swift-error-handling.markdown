---
layout: post
title:  "The Many Flavors of Swift's Error Handling Constructs"
date:   2016-04-29 17:00:00
categories: general
---

While reading tutorials and other forms of documentation about Swift's error handling mechanism, I noticed that most of them are superficial. Two notable exceptions are the [exploration](https://realm.io/news/testing-swift-error-type/) of the ``ErrorType`` protocol that [Marcus Rackwitz](https://twitter.com/mrackwitz) performed and the [document](https://github.com/apple/swift/blob/master/docs/ErrorHandlingRationale.rst) presenting the rationale and proposal for Swift error handling. Nevertheless, I couldn't find a single place that presents the numerous ways in which Swift programmers can define, signal, and capture errors. Therefore I decided to analyze this in more depth. Here I attempt to summarize this investigation. I assume that you have at least a passing familiarity with exception handling in some other language and with the basic building blocks of Swift (classes, structs, enums).

It is important to stress that this post received more than a few contributions from [Francisco Soares](https://twitter.com/frsoares).

### The Required Swift Error Handling Primer

If you've ever developed a program in a language that includes an exception handling mechanism, e.g., Java, C#, Ruby, C++, Python, among so many others, Swift error handling is straightforward. Errors are values of types that conform to the ``ErrorType`` protocol, which will become ``ErrorProtocol`` in Swift 3.0. The ``throw`` statement signals the occurrence of an error. A method that signals an error has two options: (i) either it ``catch``es the error; or (ii) it indicates in its signature that it ``throws`` an error. Most tutorials I've found on error handling in Swift start out by explaining that it is easy to define errors using an ``enum``, for example:

{% highlight swift %}
enum IOError : ErrorType {
  case FileNotFound(filePath: String)
  case ConnectionTimedOut
  case ExceededNumberOfRetries(number: Int)
}
{% endhighlight %}

``IOError`` is an enum with three enumeration cases (or constructors, if you come from a functional programming background). Each of these cases represents a different kind of error, as its name indicates. ``ExceededNumberOfRetries`` and ``FileNotFound`` are particularly interesting because they are errors that carry contextual information, the name of the file that was not found and the number of retries that were attempted before the error was signaled, respectively. This information can be employed to capture errors in more specific situations. Since each case is a value that represents an error, it can be ``throw``n:

{% highlight swift %}
func readFromFile(path: String) throws -> String {
  if (!NSFileManager.defaultManager().fileExistsAtPath(path)) {
      throw IOError.FileNotFound(filePath: path) // throws an error!
  } else {
     ... // do something useful here.
  }
}
{% endhighlight %}

The ``throws`` clause in the signature of ``readFromFile`` is required by the Swift compiler. Methods invoking ``readFromFile`` must either also have a ``throws`` clause in their signatures or explicitly ``catch`` the error. In addition, calls to ``readFromFile`` must be preceded by the ``try`` keyword to explicitly indicate that those statements may throw an error. The code snippet below presents an example of a method that invokes ``readFromFile`` and ``catch``es the  ``FileNotFound`` error (and others, as explained below).

{% highlight swift %}
func processText(filePath: String) {
  do {
    let fileContents = try readFromFile(path: filePath)
    ... // do something useful here
  } catch IOError.FileNotFound {
    print("File not found: \(filePath).")
  } catch { print(error) }
}
{% endhighlight %}

To catch an error, it is necessary to place the code that may throw errors within a ``do`` block. That block can have one or more associated ``catch`` clauses. The code snippet above has two such clauses. The first one catches ``FileNotFound`` errors, whereas the second one catches any other error. The latter is required by Swift to guarantee that any other errors are also caught. The first ``catch`` clause does not specify the name of the file that triggered the error. I come back to this topic later. In the second block, ``error`` is a predefined variable that represents the caught error. It is accessible within the body of the generic ``catch`` block.

Swift has additional constructs for error handling. It is possible to write ``try?`` before an expression that may throw an error to convert the error into an optional value.  
As pointed out in Apple's [tutorial](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html) on error handling in Swift, this construct is syntactic sugar for a common pattern of usage of ``do-catch`` blocks.
On the other hand, errors thrown by an expression preceded by ``try!`` are turned into a runtime error, more specifically, the same one that occurs when attempting to dereference ``nil``. These errors cannot be captured by Swift. Hereafter I do not cover the use of either construct.


### Many Flavors

There are many different approaches to define errors in Swift. Values of any type can be used as errors since even predefined types can be made to conform to ``ErrorType`` by means of extensions. Moreover, these approaches to define errors induce different methods to capture them. In this section I talk about both, definition and capture of errors.

#### Enumerations

The previous section covered only one approach to define errors, namely, to create an enumeration that conforms to the ``ErrorType`` protocol, and one to catch errors, using pattern matching on the enumeration cases. In the above example, to add another ``catch`` clause for the ``ExceededNumberOfRetries`` case, we could do so for a specific number of retries, as follows:

{% highlight swift %}
func processText(filePath: String) {
  do {
    ... // same as before, including first catch clause
  } catch IOError.ExceededNumberOfRetries(1) {
    ... // do something when number of retries is one  
  } catch IOError.ExceededNumberOfRetries {
    ... // do something when number of retries is something other than 1  
  } catch { print(error) }
}
{% endhighlight %}

In this example, the first ``catch`` clause will be selected when the ``ExceededNumberOfRetries`` error is constructed with argument 1. In the second one, an ``ExceededNumberOfRetries`` error will be caught independently of the argument. Since ``catch`` clauses are analyzed in textual order at runtime, there is no danger of the first one not being selected when it should be.

The possibility of describing very specific conditions for capturing an error (it is also possible to add a ``where`` clause to a ``catch`` clause to specify a boolean condition) highlights an important difference between Swift and some statically-typed languages that make extensive use of exception handling mechanisms: error capture in the examples we have seen so far is by value and not by type (it's pattern matching, after all). This is the reason why the last ``catch`` clause associated with a ``do`` block must always be generic. It is not possible to statically guarantee the absence of uncaught errors when capturing errors by pattern matching without such generic ``catch`` clause.

#### Classes

In this section I examine the use of classes to define errors. For this case, we will redefine ``ExceededNumberOfRetries`` and ``FileNotFound`` as classes that are subtypes of another class named ``IOError``.  The latter conforms to the ``ErrorType`` protocol. This structure is presented below.

{% highlight swift %}
class IOError : ErrorType {}
class ExceededNumberOfRetries : IOError {
  let numRetries : Int
  init(num: Int) { numRetries = num }
}
class FileNotFound : IOError {
  let path : String
  init(filePath: String) { path = filePath }
}
{% endhighlight %}

To use the class definitions above, the only change that needs to be applied to the ``readFromFile`` function is that the ``IOError.`` prefix must be removed from the line that throws the error. This is necessary because ``FileNotFound`` is now a full-fledged type, instead of just an enumeration case for ``IOError``. For function ``processText``, modifications are necessary because Swift does not know, by default, how to pattern match on values that are instances of classes. However, it is also possible to capture errors by type in Swift, more similarly to how it's done in Java and C#. Nevertheless, it is important to bear in mind that, behind the scenes, it's still pattern matching.
The following example is a modified version of the first version of ``processText``, adapted to work with this approach:

{% highlight swift %}
func processText(filePath: String) {
  do {
    ... // same as before
  } catch let fileError as FileNotFound {
    print("File not found: \(fileError.path).")
  } catch { print(error) }
}
{% endhighlight %}

In the code snippet, the first ``catch`` clause and its body were modified. The ``catch`` clause now captures errors whose type is ``FileNotFound`` and binds them to variable ``fileError``. The error value, an object, is then accessible within the ``catch`` block. If the captured error is not of that type, it is handled by the generic ``catch`` block. In case it is not necessary to access the error object within the ``catch`` blocks, one can use the ``is`` operator, which checks whether the value on the left has the type on its right, instead of as:

{% highlight swift %}
... // same as before
  } catch is FileNotFound {
    print("File not found.") // error is not accessible
  }
... // same as before
{% endhighlight %}

As usual for pattern matching, when capturing errors matching patterns by type it is important to keep ``catch`` clauses for more generic types after ones for more specific types. In the following example, the second ``catch`` clause will never be selected because the first one captures errors of type ``IOError``, a superclass of ``FileNotFound``.

{% highlight swift %}
func processText(filePath: String) {
  do {
    ... // same as before
  } catch let IOError as IOError {
    ... // handle the error
  } catch let fileError as FileNotFound {
    ... // Will never be reached.
  } catch { print(error) }
}
{% endhighlight %}


#### Structs

Structs defining errors are similar to classes, with the differences that ``IOError`` must be a protocol, since inheritance between structs is not possible, and that initializers become unnecessary:

{% highlight swift %}
protocol IOError2 : ErrorType {}
struct ExceededNumberOfRetries : IOError {
  let numRetries : Int
}
struct FileNotFound : IOError {
  let path : String
}
{% endhighlight %}

Using these definitions of the errors, function ``processText`` can be used as is, i.e., capturing errors by type. One relevant question is whether it is also possible to capture error structs based on their value. The short answer is yes (and the same applies to classes!), but with limitations.

[Marcus Rackwitz](https://twitter.com/mrackwitz), in his  [exploration](https://realm.io/news/testing-swift-error-type/) of the ``ErrorType`` protocol shows how one can pattern match with struct values to capture errors. Using the [LLDB](http://lldb.llvm.org/) debugger for the [LLVM](http://www.llvm.org) compiler infrastructure, he noticed that the ``ErrorType`` protocol is defined as follows, behind the scenes:  

{% highlight swift %}
protocol ErrorType {
  var _domain: Swift.String { get }
  var _code: Swift.Int { get }
}
{% endhighlight %}
<!---_ --->

It is interesting to note that types conforming to this protocol do not need to define these properties. In order to capture errors by pattern matching on the error values, one needs to first define the struct representing errors and then overload the pattern matching operator (``~=``). The only downside is that it is only possible to pattern match using the properties defined by ``ErrorType``, ``_domain`` and ``_code``. Nevertheless, it is useful to avoid having to define a new struct type for each error, in case one wants to use structs or classes to define errors.

{% highlight swift %}
struct Error : ErrorType {
    let domain: String
    let code: Int    
    var _domain: String {
        return domain
    }
    var _code: Int {
        return code
    }
}
func ~=(lhs: Error, rhs: ErrorType) -> Bool {
    return lhs._domain == rhs._domain
        && lhs._code   == rhs._code
}
{% endhighlight %}
<!---_ --->

With this scaffolding in place, it is possible to catch errors defined by structs based on the values of these two properties:

{% highlight swift %}
func processText(filePath: String) {
  do {
    ... // same as before
  } catch Error(domain: "FileNotFound", code: 404) {
    ... // handle the error
  } catch { print(error) }
}
{% endhighlight %}

The limitation of this approach is that it only employs the properties that exist in ``ErrorType`` for pattern matching, even if the struct has other properties. In the example below, the values of the new property ``additionalInfo`` of ``Error`` will not be taken into account by the ``catch`` clauses. The overload of ``~=`` cannot account for ``additionalInfo``, since this property is not defined in ``ErrorType``. Thus, the second ``catch`` clause will never be selected.

{% highlight swift %}
struct Error : ErrorType {
    ... // definitions of domain and code
    let additionalInfo: String
    ... // definitions of _domain and _code
}
func processText(filePath: String) {
  do {
    ... // same as before
  } catch Error(domain: "FileNotFound", code: 404, additionalInfo: "config.sys") {
    ... // handle the error
  } catch Error(domain: "FileNotFound", code: 404, additionalInfo: "autoexec.bat") {
    ... // handle the error
  } catch { print(error) }
}
{% endhighlight %}
<!---_ --->

It is possible to use Swift's optional casting operator (``as?``) or its forced variant (``as!``) to work around this limitation and  account for additional struct and class properties. For optional casting, all we need to do is use an `` if let ...`` block to cast to ``Error`` and add the struct type-specific code inside the block. To use forced casting, we need to check whether the second parameter of the pattern matching operator has the struct type (``Error``, in the examples) and, in case it does, force cast the argument from ``ErrorType`` to the struct type.

{% highlight swift %}
struct Error : ErrorType {
    let domain: String
    let code: Int    
    let additionalInfo : Int
    var _domain: String {
        return domain
    }
    var _code: Int {
        return code
    }
}
func ~=(lhs: Error, rhs: ErrorType) -> Bool {
    var result =  lhs._domain == newRHS._domain
               && lhs._code   == newRHS._code
    if let newRHS = rhs as? Error {
      result = result && (lhs.additionalInfo == newRHS.additionalInfo)
    }
    return result
}
{% endhighlight %}
<!---_ --->

With these definitions, any attribute from ``Error`` can be used for pattern matching when capturing errors. Thus, in the following example, the second ``catch`` clause will be selected normally.

{% highlight swift %}
func processText(filePath: String) {
  do {
    ... // same as before
  } catch Error(domain: "FileNotFound", code: 404, additionalInfo: "config.sys") {
    ... // handle the error
  } catch Error(domain: "FileNotFound", code: 404, additionalInfo: "autoexec.bat") {
    ... // handle the error
  } catch { print(error) }
}
{% endhighlight %}

Swift 2.1.1 behaves non-intuitively when overloading the pattern matching operator for structs and classes. The code snippet below defines a struct and a class and makes both of them conform to ``ErrorType``. It also overloads the pattern matching operator for both types (using just the ``code`` property). When executed, the program prints *"catch block for ErrorStruct"*, instead of *"catch block for ErrorClass"*, even though we are throwing ``ErrorClass(code: 10)``. I assume that, under the hood, Swift invokes the overloaded version of ``~=`` that best matches the type of the error object. Since this implementation disregards the object type (it only looks at the value of ``code``), the matching will depend only on the value of the ``code`` property.

{% highlight swift %}
struct ErrorStruct : ErrorType {
  let code : Int
  var _code : Int { return code }
}
class ErrorClass : ErrorType {
  let code : Int
  var _code : Int { return code }

  init(code :Int) { self.code = code }
}
func ~=(lhs: ErrorClass, rhs: ErrorType) -> Bool {
  return lhs._code == rhs._code
}
func ~=(lhs: ErrorStruct, rhs: ErrorType) -> Bool {
return lhs._code == rhs._code
}
func f() throws {
  throw ErrorClass(code: 10)
}
func g() {
  do {
    try f()
  } catch ErrorStruct(code: 10) {
    print("catch block for ErrorStruct")
  } catch ErrorClass(code: 10) {
    print("catch block for ErrorClass")
  } catch { ... }
}
g()
{% endhighlight %}
<!---_ --->

#### NSError and other predefined types

The traditional approach for Objective-C applications to signal errors is to return instances of the ``NSError`` class. This class seems to have a special status when it comes to throwing and capturing errors in Swift. It [adopts](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html) the ``ErrorType`` protocol, similarly to other types defining errors we have examined. However, a ``catch`` clause for ``NSError`` works as a generic ``catch`` clause. Thus, the following three ``catch`` clauses seem to be equivalent in the sense that any other two could be removed and the Swift compiler would still be satisfied. For the second case (``ErrorType``), Xcode emits a warning stating that *"as test is always true"*.

{% highlight swift %}
func processText(filePath: String) {
  do {
    ... // same as before
  } catch let error as NSError {
    print(error).")
  } catch let error as ErrorType {
    print(error).")
  } catch { print(error) }
}
{% endhighlight %}

It is interesting to note that ``NSError`` already defines properties ``code`` and ``domain``. Thus, to use pattern matching on value of type ``NSError``, one needs only to redefine the ``~=`` operator as we have done in the previous section.

One relevant question is whether it is possible to throw instances of ``NSException`` using Swift's ``throw`` statement. By default, ``NSException`` does not conform to ``ErrorType``. Thus, its instances cannot be thrown. However, by extending it to make it adopt ``ErrorType``, it becomes possible to both throw and capture errors of type ``NSException``.

{% highlight swift %}
// errors of type NSException can now be thrown and caught as we've seen before.
extension NSException: ErrorType { }
{% endhighlight %}

Making ``NSException`` an error is not the same, though, as dealing with Objective-C exceptions. Errors thrown from Swift code are different from exceptions thrown from Objective-C code. The underlying mechanisms differ considerably. Moreover, Swift code cannot catch exceptions thrown from Objective-C, irrespective of ``NSException`` conforming to ``ErrorType``.

As I've mentioned before, it is possible to extend any type to make it conform to ``ErrorType``. This may lead to esoteric code that, for example, throws an integer:

{% highlight swift %}
extension Int : ErrorType {}

func readFromFile(path: String) throws -> String {
  if (!NSFileManager.defaultManager().fileExistsAtPath(path)) {
    throw 42 // throws an error!?
  } else {
    ... // do something useful here.
  }
}
func processText(filePath: String) {
  do {
    let fileContents = try readFromFile(path: filePath)
    ... // do something useful here
  } catch error as Int {
    print("The answer is \(error)")
  } catch { print(error) }
}
{% endhighlight %}

### Closing comments

Swift's error handling mechanism is a breath of fresh air. It takes some well-established ideas, e.g., specific constructs to throw and handle errors and static checking for errors, and builds something truly unique out of them. However, the resulting mechanism is very flexible in that there are many different approaches to define and capture errors. In this post I have analyzed every possibility I could identify. I'd like to hear about other approaches, since I'm bound to have missed some.

### References

* [Apple's official documentation for Swift error handling](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html)

* [Rationale for the design of Swift's error handling constructs. **Very nice**!](https://github.com/apple/swift/blob/master/docs/ErrorHandlingRationale.rst)

* [Careful exploration of Swift's ErrorType protocol. Some interesting lessons to learn here](https://realm.io/news/testing-swift-error-type/)

* [Well thought-out criticism of Swift's error handing constructs, with an alternative proposal](https://gist.github.com/nicklockwood/21495c2015fd2dda56cf)

* [A post on Swift error handling from StackOverflow](http://stackoverflow.com/questions/24010569/error-handling-in-swift-language)

* [What happens with Swift error handling at the IL level? Somone has looked into it](http://finestructure.co/blog/2015/6/18/swift-2-try-catch-under-the-hood)

* [Swift error handling from the Big Nerd Ranch](https://www.bignerdranch.com/blog/error-handling-in-swift-2/)

* [Nice tutorial covering a number of aspects of Swift error handling](http://code.tutsplus.com/tutorials/error-handling-in-swift-2--cms-25096)

* [Using Swift's error handling constructs with asynchronous closures](https://appventure.me/2015/06/19/swift-try-catch-asynchronous-closures/)
