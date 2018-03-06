# Snatch

[![Maintainability](https://api.codeclimate.com/v1/badges/f56175a9f2d027469773/maintainability)](https://codeclimate.com/github/isaac-weisberg/snatch/maintainability)

Snatch is Promises meet URLSession.


## Goals

1. Provide a Promise-based wrapping around URLSession.
2. Provide an API sowewhat reminiscent of the __EcmaScript fetch API__. 
3. Be as lightweight as a kite, providing THE most basic functionality there is, the examples are in "Features" section.
4. Be usable on both Linux and Apple platforms. (Disclaimer: doesn't work on Linux platform, at least as of Swift 4.0.3 because URLSession is not entirely implemented in Foundation framework).

## Installation

#### Xcode, macOS/iOS/watchOS/tvOS targets

Download the binary release from the GitHub releases. If none are available, then...

Building from source:
1. install khanlou/Promise
2. `git clone`
3. Open Snatch.xcodeproj
4. Build Settings -> Framework Search Path, make sure that features a directory enclosing your local khanlou/Promise installation
5. Build Settings -> Skip Install, make sure is set to NO
6. (Optionally) Run test target 
7. Archive->Export

#### Swift Package Manager, Linux target

No binary releases, at least right now.

Recommended to automate the process by declaring a dependency on the project, target snatch.

Building from source:
1. `git clone`
2. `swift package resolve`
3. `swift test`
4. `swift build -c release`

Swiftdoc and Swiftmodule will be at .build/release

Alternatively: `make release`

## Usage

Contrary to what Apple's APIs often do, Snatch __doesn't have__ it's own static `sharedInstance`, so you have to create and manage one yourself. The constructor optionally accepts a `URLSessionConfiguration` that it uses to create an underlying `URLSession` object. One `Snatch` instance = `URLSession` object.

`request(_:)` method accepts URLRequest object and initiates a `dataTask` on underlying `URLSession`, resolving upon completion of the `dataTask`, fulfilling with a Result object that contains raw response body in type `Data?` and a `HTTPURLResponse` instance, rejecting upon any clientside or connection errors. The `request(_:)` __will not__ reject upon any errornous HTTP response status codes, just like that other API, called fetch.

Result has `json(_:)` method that accepts a `Decodable` type to try to interpret the response body as. Rejects on errors, fulfills with results.

`Snatch` has 2 shortcut modules built in, `get` and `post` which allow for shortcut assembly of GET and POST requests thorough their redefined `subscript` values. Custom headers are supported for both options. Arbitrary encodable is supported for `post` request, which will be ALWAYS encoded as JSON. Unescaped basic URLQuery assembly is supported for `get`.

## Samples

Basic GET with parameters and headers:
```swift
let snatch: Snatch = Snatch()
let url = URL(string: "https://apple.com")!
let params = ["objective-c-runtime": "penis"]
let headers = ["User-Agent":"70 years old Casio calculator"]

snatch.get[ url, params, headers ].then { res in
    res.data // body of response
}.catch { err in

}
```

Generic whatever request
```swift
let snatch: Snatch = Snatch()
let url = URL(string: "https://apple.com")!
var request = URLRequest(url: url)

// Do whatever you want to do with `request`
// request.httpMethod = "GAY"

snatch.request(request).then { res in
    res.data // body of response
}.catch { err in

}
```