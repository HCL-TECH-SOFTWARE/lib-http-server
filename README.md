# lib-http-server
A library for [DevOps Model RealTime](https://www.hcl-software.com/devops-model-realtime) which allows an application to communicate with an HTTP server (both sending and receiving messages).

## Usage
This library contains two capsules; one for communicating from the real-time application to an HTTP server (HttpServer_Out) and one for communicating from an HTTP server to the real-time application (HttpServer_In).

## HttpServer_Out
A capsule for outgoing communication with an HTTP server. You should inherit this capsule and create an instance of it in your application. Initialize the instance by calling the operation `configureServer()` before you make any HTTP requests (for example by redefining the initial transition). In the state machine of your inherited capsule, handle all events that you want to map to HTTP requests. Call operations such as `httpGet()` to perform the HTTP requests. You can for example use the `RTJsonEncoding` class to encode the received message and its data to a JSON representation which then can be sent in the HTTP request.
Running your capsule instance in its own thread is recommended but not strictly necessary if the HTTP response comes fast enough.

## HttpServer_In
A capsule for incoming communication with an HTTP server. You should inherit this capsule and create an instance of it in your application. You need to initialize the instance by calling the operation `configureServer()` before you can receive any messages from the HTTP server.
Fetching messages from the HTTP server uses a long-running HTTP request that only completes when an incoming message is available. You must therefore run your capsule instance in its own thread so that the rest of your application is not blocked. When a message from the HTTP server is available, the `onMessageReceived()` operation will be called. Override it to handle the received HTTP message, for example by mapping it to sending of corresponding protocol events to other parts of your application.

## Build the Library
The library uses the [POCO C++ libraries](https://pocoproject.org) and you must therefore first clone and build the [POCO GitHub repository](https://github.com/pocoproject/poco) for your particular platform.

Add the TC `httpServerLib.tcjs` as a prerequisite of the executable TC for your Model RealTime application. Also specify the location of the POCO library root folder by setting the 'pocoLoc' property of your TC (using the Code tab of the TC editor). For example:

`
tc.pocoLoc = 'D:\\github\\poco';
`

Also tell the linker where to find the built POCO libraries. For example, for Visual Studio 64 bit set the following:

`
tc.linkArguments = '/MACHINE:X64 /LIBPATH:"' + tc.pocoLoc + '\\lib64"';
`

Finally, if you have built POCO as shared libraries remember to set the PATH variable (on Windows) or LD\_LIBRARY\_PATH (on Unix) to include the folder where they are located. For 64 bit builds the folder is called 'bin64' and is in the POCO root folder.

Note that the TC `httpServerLib.tcjs` is designed to be built as a prerequisite of an executable TC and will reuse some of the TC properties from that executable (using the topTC variable). Here is an example of such a TC property:

` 
tc.compileArguments = topTC.compileArguments || '$(DEBUG_TAG)';
`

If you want to be able to build the library TC directly (i.e. as a "top TC"), you should first modify these TC properties (essentially making sure that the value after the '||' is appropriate).
