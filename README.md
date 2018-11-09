# lib-http-server
A library for HCL RTist which allows an application to communicate with an HTTP server (both sending and receiving messages)

## Usage
This library contains two capsules; one for communicating from the real-time application to an HTTP server (HttpServer_Out) and one for communicating from an HTTP server to the real-time application (HttpServer_In).

## HttpServer_In
A capsule for outgoing communication with an HTTP server. You should inherit this capsule and create an instance of it in your application. Initialize the instance by calling the operation `configureServer()` before you make any HTTP requests (for example by redefining the initial transition). In the state machine of your inherited capsule, handle all events that you want to map to HTTP requests. Call operations such as `httpGet()` to perform the HTTP requests. You can for example use the `RTJsonEncoding` class to encode the received message and its data to a JSON representation which then can be sent in the HTTP request.
Running your capsule instance in its own thread is recommended but not strictly necessary if the HTTP response comes fast enough.

## HttpServer_Out
A capsule for incoming communication with an HTTP server. You should inherit this capsule and create an instance of it in your application. You need to initialize the instance by calling the operation `configureServer()` before you can receive any messages from the HTTP server.
Fetching messages from the HTTP server uses a long-running HTTP request that only completes when an incoming message is available. You must therefore run your capsule instance in its own thread so that the rest of your application is not blocked. When a message from the HTTP server is available, the `onMessageReceived()` operation will be called. Override it to handle the received HTTP message, for example by mapping it to sending of corresponding protocol events to other parts of your application.
