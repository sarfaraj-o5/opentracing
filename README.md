Introduction
Recently, there has been a lot of discussion around OpenTracing. We’ll start this blog by introducing OpenTracing, explaining what it is and why it is gaining attention. Next, we will discuss distributed tracing system Jaeger and how it helps in troubleshooting microservices-based distributed systems. We will also set up Jaeger and learn to use it for monitoring and troubleshooting purposes.

Drift to Microservice Architecture
Microservice Architecture
Microservice Architecture has now become the obvious choice for application developers. In the Microservice Architecture,  a monolithic application is broken down into a group of independently deployed services. In simple words,  an application is more like a collection of microservices. When we have millions of such intertwined microservices working together, it's almost impossible to map the inter-dependencies of these services and understand the execution of a request.

If a monolithic application fails then it is more feasible to do the root cause analysis and understand the path of a transaction using some logging frameworks. But in a microservice architecture, logging alone fails to deliver the complete picture.

Is this service called first in the chain? How do I span all these services to get insight into the application? With questions like these, it becomes a significantly larger problem to debug a set of interdependent distributed services in comparison to a single monolithic application, making OpenTracing more and more popular.

OpenTracing
What is Distributed Tracing?
Distributed tracing is a method used to monitor applications, mostly those built using the microservices architecture. Distributed tracing helps to highlight what causes poor performance and where failures occur.

How OpenTracing Fits Into This?
The OpenTracing API provides a standard, vendor neutral framework for instrumentation. This means that if a developer wants to try out a different distributed tracing system, then instead of repeating the whole instrumentation process for the new distributed tracing system, the developer can easily change the configuration of Tracer.

OpenTracing uses basic terminologies, such as Span and Trace. You can read about them in detail here.

OpenTracing
OpenTracing is a way for services to “describe and propagate distributed traces without knowledge of the underlying OpenTracing implementation.”

Let us take the example of a service like renting a movie on any rental service like iTunes. A service like this requires many other microservices to check that the movie is available, proper payment credentials are received, and enough space exists on the viewer’s device for download. If either one of those microservice fail, then the entire transaction fails. In such a case, having logs just for the main rental service wouldn’t be very useful for debugging. However, if you were able to analyze each service you wouldn’t have to scratch your head to troubleshoot  which microservice failed and what made it fail.

In real life, applications are even more complex and with the increasing complexity of applications, monitoring the applications has been a tedious task. Opentracing helps us to easily monitor:

Spans of services
Time taken by each service
Latency between the services
Hierarchy of services
Errors or exceptions during execution of each service.
Jaeger: A Distributed Tracing System by Uber
Jaeger, is released as an open source distributed tracing system by Uber Technologies. It is used for monitoring and troubleshooting microservices-based distributed systems, including:

Distributed transaction monitoring
Performance and latency optimization
Root cause analysis
Service dependency analysis
Distributed context propagation
Major Components of Jaeger
Jaeger Client Libraries
Agent
Collector
Query
Ingester
Running Jaeger in a Docker Container
1.  First, install Jaeger Client on your machine:

CODE: https://gist.github.com/velotiotech/97c0847485743c387faba0b28fb84725.js

2.  Now, let’s run Jaeger backend as an all-in-one Docker image. The image launches the Jaeger UI, collector, query, and agent:

CODE: https://gist.github.com/velotiotech/b83b5f7374989ed5dfcc11ec1e989913.js

TIP:  To check if the docker container is running, use: Docker ps.

Once the container starts, open http://localhost:16686/  to access the Jaeger UI. The container runs the Jaeger backend with an in-memory store, which is initially empty, so there is not much we can do with the UI right now since the store has no traces.

Creating Traces on Jaeger UI
1.   Create a Python program to create Traces:

Let’s generate some traces using a simple python program. You can clone the Jaeger-Opentracing repository given below for a sample program that is used in this blog.

CODE: https://gist.github.com/velotiotech/e8f0e959d07bf55426da91b44b837626.js

The Python program takes a movie name as an argument and calls three functions that get the cinema details, movie showtime details, and finally book a movie ticket.

It creates some random delays in all the functions to make it more interesting, as in reality the functions would take certain time to get the details. Also the function throws random errors to give us a feel of how the traces of a real-life application may look like in case of failures.

Here is a brief description of how OpenTracing has been used in the program:

Initializing a tracer:
CODE: https://gist.github.com/velotiotech/dc7cf76f7960956dd4aa804d5ffe7dd6.js

Using the tracer instance:
CODE: https://gist.github.com/velotiotech/50d21c045321c4343d2e3ea831abc632.js

Starting new child spans using start_span:  
CODE: https://gist.github.com/velotiotech/3a056c61d59d45384c0d77dc2d7cd848.js

Using Tags:
CODE: https://gist.github.com/velotiotech/4d394f4762bf8af39a4dcff3f070c49c.js

Using Logs:
CODE: https://gist.github.com/velotiotech/d1dd0d1426d75bc0b3fd39fa25ec57ad.js

2. Run the python program:‍

CODE: https://gist.github.com/velotiotech/9f88e9a6eabaa3301968ba3b2a61ea29.js

Now, check your Jaeger UI, you can see a new service "booking" added. Select the service and click on "Find Traces" to see the traces of your service. Every time you run the program a new trace will be created.

Jaeger UI - Traces
You can now compare the duration of traces through the graph shown above. You can also filter traces using  “Tags” section under “Find Traces”. For example, Setting “error=true” tag will filter out all the jobs that have errors, as shown:

Jaeger UI - Find Traces
To view the detailed trace, you can select a specific trace instance and check details like the time taken by each service, errors during execution and logs.

Jaeger - Trace Instances 
The above trace instance has four spans, the first representing the root span “booking”, the second is the “CheckCinema”, the third is the “CheckShowtime” and last is the “BookShow”. In this particular trace instance, both the “CheckCinema” and “CheckShowtime” have reported errors, marked by the error=true tag.

Conclusion
In this blog, we’ve described the importance and benefits of OpenTracing, one of the core pillars of modern applications. We also explored how distributed tracer Jaeger collect and store traces while revealing inefficient portions of our applications. It is fully compatible with OpenTracing API and has a number of clients for different programming languages including Java, Go, Node.js, Python, PHP, and more.

References
https://www.jaegertracing.io/docs/1.9/
https://opentracing.io/docs/