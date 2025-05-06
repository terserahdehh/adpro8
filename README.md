## Reflection ##

1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?
Answer : Unary RPC sends one request and waits for one response, server streaming sends one request and then receives a continuous flow of responses, while bi-directional streaming lets both sides send streams of messages independently. Unary suits simple lookups or CRUD operations that need single reply, server streaming works well for real-time feeds like log updates or video frames needing one way updates, and bi-directional streaming shines in chat apps or collaborative editing where both ends need to talk continuously.

2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?
Answer : Rust gRPC services often handle authentication by validating metadata tokens or using mutual-TLS certificates through Tonic’s built in interceptor support. Authorization usually means adding interceptor logic to inspect those tokens or certificate identity and enforce role-based access before dispatching each RPC.

3. What are the potential challenges or issues that may arise when handling bi-directional streaming in Rust gRPC, especially in scenarios like chat applications? 
Answer : Managing backpressure when one side sends faster than the other can lead to resource exhaustion or dropped messages. Coordinating message order and dealing with errors mid stream without losing context demands careful handling. Ensuring cancelled or failed streams are cleaned up under high concurrency also risks memory leaks or deadlocks.

4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?
Answer : ReceiverStream hooks a Tokio channel into gRPC streaming with hardly any setup. It still needs manual channel creation and error checks, and a fixed buffer can overflow and drop messages if traffic spikes. Under heavy use, that extra channel step can slow things down and make clean shutdowns trickier.

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?
Answer : Auto generated proto code lives in a dedicated services module, with server logic in grpc_server.rs and client logic in grpc_client.rs. Each service implementation and its helper functions go in separate modules, and shared interceptors or utilities sit in a common module. A central build script plus a crate for common types keeps definitions consistent and cuts down on repeated setup.

6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?
Answer: Adding real payment gateway integration or a database call to record and verify each transaction. Introducing retry logic, error handling, and idempotency checks so failed or duplicate requests don’t corrupt state. Instrumenting logs and metrics for auditing and monitoring under production load.

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?
Answer: gRPC uses HTTP/2 and Protocol Buffers, so data moves in a small, fast format. It has libraries for many languages, so different services use the same APIs. Browsers or REST/JSON clients need a proxy or gateway to connect.

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?
Answer: HTTP/2 can send many requests and responses at once over a single connection and shrinks headers to speed things up compared to HTTP/1.1. With WebSocket, HTTP/1.1 needs an extra upgrade step for two-way messaging, while HTTP/2 does streams and server push by default. It’s harder to set up and debug, and some old proxies only work with HTTP/1.1.

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?
Answer: REST APIs work by sending one request and waiting for one response, so real time updates need polling or long polling and add extra delay. gRPC bidirectional streaming keeps a channel open so both sides can send messages continuously without waiting. That makes gRPC more responsive for live data scenarios like chats or dashboards.

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?
Answer: gRPC uses Protocol Buffers, so every message follows a fixed schema and turns into small binary data. JSON in REST skips schemas, letting payloads change freely and stay human-readable, but JSON is bulkier and can fail at runtime. That means Protobuf runs faster and spots errors early, while JSON stays flexible and easier to debug.
