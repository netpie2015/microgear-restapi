# microgear-restapi

NETPIE platform prepares the REST API for connecting to other devices thru the HTTP protocol. This API is programming language independent. It can be used with traditional web server, through command lines or or through web services. 

### API Endpoint
NETPIE's REST API service is at 

```
https://api.netpie.io/
```

### Authentication
To connect to NETPIE, the API client has to authenticate itself using one of these two methods.

1. Basic Authentication through the HTTP Header, using 
```
Username : APPKEY
Password : APPSECRET
```
Example of Basic Authentication with cURL
```
$ curl -X GET "http://www.domainname.com/resources" -u john:secret 
```
2. Authenticate via URL parameter, using 
```
?auth=APPKEY:APPSECRET
```
Example of URL parameter with cURL
```
$ curl -X GET "http://www.domainname.com/resources?auth=john:secret" 
```
---
### Resource Types
#### Topic
Topic is the location for mesage exchange among microgears. Topic name has a path format, for example, /home/bedroom/temp.  Microgear can PUT/publish, GET/subscribe to this topic. 

#### Microgear
Microgear is the device that connects to NETPIE. We can send a message directly to any microgears by addressing their alias. 
#### Postbox
postbox is the message storage location. Messages are stored in postbox as queue (first in, first out) until they are read. Read messages will be removed from postbox. It is suitable for microgear that cannot have real-time communication like PHP script.

---


#### topic
--
**PUT /topic/**_{appid}_**/**_{topic}_

publish message to the topic of a specified appid 

URL parameter
* *retain* Keep this message (only keep the newest message)

Body
  * message to publish. If you want to remove the previous retained message, set the retain parameter as True and send an empty-string message.

Example of calling REST API with cURL 
Suppose we have an application on NETPIE with appid = myappid and App Key and App Secret are as follows.
![myappid-sample](https://cloud.githubusercontent.com/assets/7685964/11860526/509ed20a-a4a8-11e5-9bda-43749e256e70.png)

We will use REST API to send a retained message  "ON" to a microgear that  subscribes topic /home/bedroom/light using this cURL command line 
```
$ curl -X PUT "https://api.netpie.io/topic/myappid/home/bedroom/light?retain" -d "ON" -u jVjzJXaJwdJKHhF:StOAKIZhXB5CaqnIHeb7s1DfiW7mQj 
```
--
**GET /topic/**_{appid}_**/**_{topic}_

Read message from a topic of a specified appid. Client will only receive the newest retained message. 

URL parameter
* None

Body
* None

Example
```
$ curl -X GET "https://api.netpie.io/topic/myappid/home/bedroom/light" -u jVjzJXaJwdJKHhF:StOAKIZhXB5CaqnIHeb7s1DfiW7mQj 
```

---
#### Microgear
--

**PUT /microgear/**_{appid}_**/**_{gearalias}_

Send a message to a microgear of the appid *appid* whose alias is *gearalias*

Body
* message to be sent as a plain text string. If the message is encoded as JSON, the receiver has to parse the string by itself.

---
#### Postbox
--

**PUT /postbox/**_{appid}_**/**_{postboxname}_

Send a message to a postbox named *postboxname* that belongs to an *appid*

URL parameter
* *tag* Sender can set a tag to a message. This tag can be use to select messages of interest.

Body
* message to be sent as a plain text string. If the message is encoded as JSON, the receiver has to parse the string by itself.
```
$ curl -X PUT "https://api.netpie.io/postbox/myappid/webbox?tag=error" -d "ON" -u jVjzJXaJwdJKHhF:StOAKIZhXB5CaqnIHeb7s1DfiW7mQj
```
--
**GET /postbox/**_{appid}_**/**_{postboxname}_

Read a message one message at a time from the postbox named *postboxname* that belongs to an *appid*. The message that arrives first will be read out first. 

URL parameter
* *tag* Don't need to be specified. If a tag is specified, only the messages with that tag will be read out.

Body
* None
```
$ curl -X GET "https://api.netpie.io/postbox/myappid/webbox?tag=error" -u jVjzJXaJwdJKHhF:StOAKIZhXB5CaqnIHeb7s1DfiW7mQj
```
