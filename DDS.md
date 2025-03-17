# DDS
In order to improve the communication system of ROS1 a new middleware was build.The benefit of using an end-to-end middleware, like DDS, is that there is much less code to maintain and the behavior and exact specifications of the middleware have already been distilled into documentation. In addition to system-level documentation, DDS also has recommended use cases and a software API. 
DDS provides a publish-subscribe transport using "interface description language" and distributed discovery system which allows any 2 DDS programs to communicate without any ROS master which adds to its reliability and flexibility 
It ensures that the right information reaches the right place at the right time
It allows different parts of a system to publish data and other to subscribe to it (Think of it like a radio broadcast—a station sends out signals, and anyone tuned to that frequency (subscribers) receives it.)
It allows fine-tuning of how messages are sent and recieved 
DDS can act like both udp and tcp (feature of QoS) (but mainly uses udp) i.e(If you need fast data updates, DDS can blast data like UDP (fast but no guarantee of delivery). If you need reliable communication, DDS can act like TCP (ensuring all messages are received, even after delays))  all of these things can be controlled by changing the QoS parameters.
it allows direct communication between devices without a central hub which makes it faster and more robust

How does DDS works
1. a device publishes data under a topic and send it through data writer
2. a subscriber listens to it using a data reader to recieve updates whenever the publisher send new data
3. dds ensures that the messages are delivered based on the defined Quality of Service (QoS) settings.
If low latency is needed, DDS prioritizes speed over reliability(udp).
If reliability is required, DDS resends lost messages and ensures delivery(tcp).
4.New subscribers can join anytime and start receiving messages instantly.
If a publisher disconnects, DDS can store past messages for new subscribers (if configured).

If a ROS package directly accesses a specific DDS implementation, it will require an extra package dependency (specific to that DDS vendor).If users bypass ROS and access DDS directly, their code will no longer work with different DDS vendors.However, keeping ROS independent of a single DDS vendor allows users to choose the best DDS implementation for their needs (e.g., better real-time performance, lower memory usage).

discovery system
DDS has a built-in discovery system that automatically detects and connects devices without needing a central server(if one device fails, others can still communicate.).
about user defined metadata :
      DDS allows users to attach extra information (metadata) when devices discover each other.
      ROS can use this metadata to add extra features to the basic DDS communication system.

ros1 never used shared memory because it was slower than tcp.it could have used zero copying mechanism but ros1 used nodelets which allows diff part of ros program to share data without copying by parsing memory pointers(shared pointers)
ROS 2 improves performance by using DDS, which can automatically use shared memory for faster communication.
Shared memory is a way for multiple programs (or parts of a program) to access the same data directly in memory instead of sending it through a network or copying it multiple times.
DDS splits messages into multiple small packets (UDP datagrams) and sends them separately.
However, sending many small packets is slower than sending one large TCP message.
So, to avoid this problem, DDS vendors use shared memory instead (which is even faster).
✅ Same Computer → Uses Shared Memory (Fastest).
✅ Different Computers → Uses UDP, TCP, or Multicast (Network Communication).
❌ Shared memory does NOT work between different computers because they do not share the same physical memory.
DDS can also use multicast, where one message is sent to multiple computers at the same time instead of sending individual copies to each one.
This reduces network traffic when many computers are communicating.


