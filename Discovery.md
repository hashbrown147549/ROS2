# Discovery
eProsima Fast DDS has 2 supported options for ROS2 node discovery
1. simple discovery
2. discovery server

## simple discovery
default
Generally operates on the basis that any ROS 2 devices on the network should be discoverable and connect automatically
Requires multicasting on the network (may be restricted on school or corporate networks)
Good for simple systems (small number of nodes on a dedicated local network)

## discovery server
Requires manual configuration in the robot.yaml
Generally operates on the basis that ROS 2 networks should be controlled and should only discover other nodes as needed
Does not require multicasting (more likely to work on school or corporate networks)

## simple discover
1.Each device (like a robot, computer, or any connected equipment) sends out regular announcement messages to let others know that it's there. These messages are called multicast because they are sent to all devices in the network, not just one specific device.
2.Along with the announcement, each device also shares some information about itself. This could be things like:

    Unicast address: the specific address where it can be reached.
    Topics: what it's interested in or what it’s doing (for example, “controlling robot arm” or “camera feed”).
    Services: what kind of services it offers (like “motion control” or “data processing”).
3.When a device sends out its announcement, all other devices on the network hear it and learn about that device and what it's offering, even if they don’t need that information. This helps them to discover all the devices around them.
4.Devices don’t need to directly ask each other for information. They automatically share and listen to these announcements, so they just "know" what other devices are available and what they do.
Multicast means the message is sent to all other devices on the network, not just one specific device.
The unicast address allows other robots to directly communicate with the sending robot if needed.

## discovery server
A discovery server in simple terms is a system that helps devices (or participants) in a network find each other and share information about what they can do, without everyone needing to know everything about everyone else right away.
A discovery server keeps track of the discovery information for devices in the network. This is like a lookup table that records which devices are available and what services they offer.
When a device (referred to as a participant or node) starts up, it checks in with the discovery server to share what it can do and what information it needs
A participant doesn’t need to know everything about all the other devices on the network. It only needs to know where to find the ones it cares about. 
The ROS_DISCOVERY_SERVER variable tells each robot (or device) which discovery server it should communicate with to get the necessary information. 
Multiple discovery servers can be used in the system for things like backup (in case one server goes down) or separating information (e.g., organizing by different regions or types of devices). It makes the system more robust and efficient.

for any ros2 and fast dds enabled device 'simple discovery' is the default settings
you can switch to discovery server for any of the following reasons:

    If your network does not support multicasting (often true on school or corporate networks)
    If you have a large system (several robots or an excess number of nodes/topics)
    If you want to control which devices have access to which information
    If you have a weak network (for example in cases of long range wireless connection)

discovery server configurations
fully connected:
          Every robot has its own local discovery server.
          All ROS 2 devices in the system (which can include the robots) can see all the topics from all other devices.
          Each ROS 2 device is configured (via the robot.yaml file) to communicate with all the discovery servers in the system. This means that each robot or device can get information from the others by reaching out to their discovery servers.
          This configuration is achieved by listing all of the computers' hostnames in the servers section on all of the robots. Including the offboard computer




command center:
        Each robot in the system works on its own. They don’t need to rely on other robots to function. Each robot has its own discovery server (
        There’s also an offboard computer (a computer outside of the robots, like a central controller) that checks in with each robot. The offboard computer connects to all the robots’ discovery servers, so it can see everything each robot is doing (all the topics or tasks they’re working on).
        The robots don’t communicate with each other directly. But the offboard computer can see all the topics (information like robot actions or sensor data) from all the robots at once. It can also send information back to the robots.
        The offboard computer can also run its own ROS 2 nodes (programs that manage robot tasks), and it doesn’t need to depend on the robots to function
## UDP IN Discovery
Discovery Process:

    When a new ROS 2 node (like a robot or a sensor) joins the system, it needs to announce itself to others and discover what topics or services other nodes are offering.
    UDP is used to broadcast these discovery messages to all other nodes on the network. The UDP broadcast allows all the other nodes on the same local network to receive the message and respond with their own discovery information.

Why UDP for Discovery?

    Speed: UDP is fast because it doesn’t have the overhead of establishing a connection like TCP does. This is important in real-time systems like robotics, where discovery must happen quickly.
    Broadcast: UDP allows messages to be sent to all devices on the local network at once (broadcasting), which is useful for discovery. In ROS 2, when a new node starts, it sends out a UDP multicast or broadcast message to inform other nodes about its presence and capabilities.
    No Reliability: In discovery, you don’t need guaranteed delivery (which TCP provides) because the nodes will keep sending their discovery messages periodically. If a message is missed, it’s not a big deal; the node will try again. This is where UDP is appropriate, as it's designed for speed and doesn't ensure message delivery.

After Discovery (Communication):

    Once discovery happens, ROS 2 nodes can communicate over UDP or TCP, depending on the transport settings. The discovery phase simply uses UDP to find each other, and the communication (data transfer) between nodes can then use the most appropriate transport (UDP or TCP).
        
