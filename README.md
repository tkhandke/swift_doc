# swift_doc  
# Additional Considerations for Swift on Linux  

# create a package   

mkdir swifttesting  
cd swifttesting
swift build --init library   
(replace library with your executable of app)   

create following hierarchy    
package    
---sources  
---testing   
---app_files   


# a simple object function   

```swift
public struct swift_example
{
    let title: String
    let completed: Bool
    let order: Int
    
    init(title: String, completed: Bool, order: Int)
    {
        self.title = title
        self.completed = completed
        self.order = order
    }
}
```
here we have initialized title,completed,order variables   
Let's build that in Swift and we'll put it in Sources/swift_example.swift   

# setup unit test code  

Setup unit test class separate, so in package/testing create unit test class.  
we can call this class as swift_unittesting.swift  
and we'll write a simple unit test to get us started:   

```swift
class TestURL : XCTestCase {
    static var allTests = {
        return [
            ("test_URLStrings", test_URLStrings),
            ("test_fileURLWithPath_relativeToURL", test_fileURLWithPath_relativeToURL),
            ("test_fileURLWithPath", test_fileURLWithPath),
            ("test_fileURLWithPath_isDirectory", test_fileURLWithPath_isDirectory),
            // Other tests go here
        ]
    }()

    func test_fileURLWithPath_relativeToURL() {
        // Write your test here. Most of the XCTAssert macros you are familiar with are available.
        XCTAssertTrue(theBestNumber == 42, "The number is wrong")
    }

    // Other tests go here
```

# linux specific additions

To test under Linux, we need to do two things.    
Firstly we need to provide a list all the unit test methods in the test class as a static variable and
secondly we need to list our test classes in a file called LinuxMain.swift.  

we need to add the following block of code in linux for making tests run  
```swift
#if os(Linux)
extension FooTests {
	static var allTests : [(String, TestURL -> () throws -> Void)] {
		return [
      XCTMain([testCase(TestNSString.allTests), testCase(TestNSArray.allTests), testCase(TestNSDictionary.allTests)])
		]
	}
}
#endif 
```
//later this is from orginal document.

We are currently investigating ideas on how to make these additional steps for test discovery automatic   
when running on the Swift runtime.

# Command Line Usage
The `XCTMain` function does not return, and will cause your test app to exit with either `0`   
for success or `1` for failure. Certain command line arguments can be used to modify the test runner behavior:

* A particular test or test case can be selected to execute. For example:

```sh
$ ./FooTests Tests.FooTestCase/testFoo  # Run a single test case
$ ./FooTests Tests.FooTestCase          # Run all the tests in FooTestCase
```
* Tests can be listed, instead of executed.

```sh
$ ./FooTests --list-tests
Listing 4 tests in FooTests.xctest:

Tests.FooTestCase/testFoo
Tests.FooTestCase/testBar
Tests.BarTestCase/test123

$ ./FooTests --dump-tests-json
{"tests":[{"tests":[{"tests":[{"name":"testFoo"},{"name":"testBar"}],
"name":"Tests.FooTestCase"},{"tests":[{"name":"test123"}],"name":"Tests.BarTestCase"}],"name":"Tests.xctest"}],"name":"All tests"}




