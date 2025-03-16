# QOS
unlike ROS1 which only depend upon tcp for its communication ROS2 offers QoS (Quality of Service)policies which help you to fine tune communication (helps you chose better way of communication tcp or udp)
ROS2 provides a set of predefined QoS profiles for common use cases 
Quality of Service (QoS) in DDS controls how data is sent, received, and stored. It helps customize communication based on speed, reliability, and resource usage.
QoS has certain policies which helps to choose a better communication
History : there are 2 types of storage 
          1. keep last: stores only n samples (depends upon the queue size)
          2. stores all samples

Depth: if the history is set too keep last you need to specify the queue size (how many samples nneed to be stored)

reliability:reliable it retries multiple times if a sample is not recieved
            best effort: no retries

durability: should the past messages be stored or not?
          transient local: the publisher stores recent msgs and the late subscribers can recieve                               some past msgs as long the publisher is running
          volatile:only new msgs are recieved by the subscriber

deadline: if a msg doesn't arrive within the deadline an alert is triggered

lifespan: the max time between publishing and the reception of a msg 

liveliness: it checks whether the nodes of a publisher are alive for another "lease duration"                 when any one of its publishers has published a msg

lease duration: the maximum period of time a publishe has to tell that it is alive before the 
              system thinks it is dead

A connection between a publisher and a subscription is only made if the pair has compatible QoS profiles.In DDS, a publisher (who sends data) and a subscriber (who receives data) must agree on communication settings (QoS policies) before they can exchange messages.
Subscribers "Request" a QoS profile → They define the minimum quality they need.
Publishers "Offer" a QoS profile → They define the maximum quality they can provide.
A connection is only made if the publisher’s offer meets or exceeds the subscriber’s request.
therefore if publisher(reliable) connects to a subscriber(best effort)
A single publisher can connect to multiple subscribers at the same time.
Each subscriber can have a different requested QoS profile (as long as the publisher meets their needs).
Subscribers do not affect each other—each one connects independently to the publisher based on their own QoS settings.

In ROS 1:
Publishers and subscribers must have the same message type, just like in ROS 2.
However, ROS 1 uses a centralized ROS Master, which helps in setting up connections.
In ROS 2 (with DDS):
Message types must still match.
The ROS Master is removed, and DDS handles discovery automatically.
QoS policies allow more flexibility (e.g., different levels of reliability, durability, etc.), but they do not override message type compatibility.

## pseodocode
Setting QoS on DDS entities

    create a Domain Participant, -1 defaults to value defined in configuration file
    dds::domain::DomainParticipant dp(-1);

    dds::topic::qos::TopicQos topicQos
                 = dp.default_topic_qos()
                     << dds::core::policy::Durability::Transient()
                     << dds::core::policy::Reliability::Reliable();

    dds::topic::Topic<tutorial::TempSensorType> topic(dp, "TempSensor", topicQos);

    dds::pub::qos::PublisherQos pubQos
                 = dp.default_publisher_qos()
                     << dds::core::policy::Partition("building-1:floor-2:room:3");

    dds::pub::Publisher pub(dp, pubQos);

    dds::pub::qos::DataWriterQos dwqos = topic.qos();
    dds::core::policy::TransportPriority transportPriority(10);
    dwqos << transportPriority; 

    dds::pub::DataWriter<tutorial::TempSensorType> dw(pub, topic, dwqos);
