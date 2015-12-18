# microgear-restapi

NETPIE platform ได้จัดเตรียม REST API สำหรับการติดต่อสื่อสารกับ microgear ชนิดอื่นๆ ผ่านทาง HTTP protocol ที่เข้าถึงได้ง่าย โดยไม่ยึดติดกับ programming language หรือ hardware สามารถนำไปประยุกต์ใช้กับ web server แบบดั้งเดิม หรือเรียกผ่าน command line รวมไปถึงการเชื่อมต่อกับ web service ต่างๆ


### API Endpoint
REST API ของ NETPIE ให้บริการอยู่ที่ 

```
https://api.netpie.io/
```

### Authentication
ในการเชื่อต่อ API client จะต้องทำการยืนยันตัวตน โดยใช้หนึ่งในสองวิธีนี้

1. ส่งผ่าน HTTP Header แบบ Basic Auth โดยใช้
```
Username : APPKEY
Password : APPSECRET
```
ตัวอย่างการใช้ Basic Auth ด้วย cURL
```
$ curl -X GET "http://www.domainname.com/resources" -u john:secret 
```
3.2.ส่งผ่านทาง URL parameter ในรูปแบบ
```
?auth=APPKEY:APPSECRET
```
ตัวอย่างการใช้ URL parameter ด้วย cURL
```
$ curl -X GET "http://www.domainname.com/resources?auth=john:secret" 
```

---
### Rate Limit
REST API ถูกจำกัดอัตราการเรียกใช้ที่ 10 request ใน 1 วินาที ต่อ IP หากมีการเรียก API เกินอัตราที่กำหนด จะได้รับ response เป็น 403 Rate Limit Exceeded

---
### Resource Types
#### Topic
topic เป็นจุดแลกเปลี่ยน message ระหว่าง microgear ลักษณะการเขียนจะอยู่ในรูปของ path เช่น /home/bedroom/temp โดย microgear สามารถ PUT/publish, GET/subscribe ไปที่ topic นี้ได้

#### Postbox
postbox เป็นพื้นที่สำหรับเก็บข้อมูลแบบ queue โดย message ที่ถูกส่งเข้าไปใน postbox จะถูกเก็บสะสมไว้ จนกว่าจะมีการอ่านออกไป message ที่ถูกอ่านแล้วจะหายไปจาก postbox ทันที เหมาะที่จะใช้เป็นเครื่องมือสื่อสารกับ microgear ที่ไม่สามารถ online ได้ตลอดเวลา เช่น PHP script

---


#### topic
--
**PUT /topic/**_{appid}_**/**_{topic}_

publish message ไปยัง topic ของ appid ตามที่ระบุ

URL parameter
* *retain* สั่งให้เก็บค่านี้ไว้ (เฉพาะค่าล่าสุดเพียงค่าเดียว)

Body
  * เป็น message ที่จะส่ง หากต้องการลบค่าที่ retain ไว้ ให้ส่งแบบ retain และใช้ body เป็น string เปล่า

ตัวอย่างการเรียก REST API ด้วย cURL สมมติว่าเราได้สร้าง App บน NETPIE ชื่อ myappid และมี Key และ Secret ดังนี้
![myappid-sample](https://cloud.githubusercontent.com/assets/7685964/11860526/509ed20a-a4a8-11e5-9bda-43749e256e70.png)

เราจะใช้ REST API ในการส่ง message แบบ retain ว่า "ON" ไปยัง microgear ที่ subscribe topic /home/bedroom/light ได้ด้วย cURL command line นี้
```
$ curl -X PUT "https://api.netpie.io/topic/myappid/home/bedroom/light?retain" -d "ON" -u jVjzJXaJwdJKHhF:StOAKIZhXB5CaqnIHeb7s1DfiW7mQj 
```
--
**GET /topic/**_{appid}_**/**_{topic}_

อ่าน message จาก appid ที่ topic ตามที่ระบุ client จะได้รับเฉพาะ message ล่าสุดที่ถูก retain ไว้ก่อนหน้านี้

URL parameter
* ไม่มี

Body
* ไม่มี

ตัวอย่าง
```
$ curl -X GET "https://api.netpie.io/topic/myappid/home/bedroom/light" -u jVjzJXaJwdJKHhF:StOAKIZhXB5CaqnIHeb7s1DfiW7mQj 
```

---
#### Microgear
--
**PUT /microgear/**_{appid}_**/alias/**_{alias}_ 

ส่ง message ไปยัง microgear แบบเจาะจง alias เฉพาะ microgear ที่มีการตั้ง alias ตัวเอง เท่านั้น ที่จะได้รับ message นี้

URL parameter
* ไม่มี

Body
  * เป็น message ที่จะส่ง

ตัวอย่างการเรียก REST API ด้วย cURL
```
$ curl -X PUT "https://api.netpie.io/microgear/myappid/doorlock" -d "ON" -u jVjzJXaJwdJKHhF:StOAKIZhXB5CaqnIHeb7s1DfiW7mQj
```

---
#### Postbox
--

**PUT /postbox/**_{appid}_**/**_{postboxname}_

ส่ง message ไปยัง postbox ชื่อ *postboxname* ของ *appid*

URL parameter
* *tag* ผู้ส่งสามารถติด tag ให้ message ได้ เพื่อความสะดวกในการเลือกอ่านเฉพาะ message ที่สนใจ

Body
* message ที่จะส่ง เป็น plain text string หากมีการ encode ด้วยรูปแบบ json ทางปลายทางจะต้องนำ string ไป parse เอง
```
$ curl -X PUT "https://api.netpie.io/postbox/myappid/webbox?tag=error" -d "ON" -u jVjzJXaJwdJKHhF:StOAKIZhXB5CaqnIHeb7s1DfiW7mQj
```
--
**GET /postbox/**_{appid}_**/**_{postboxname}_

อ่าน message ครั้งละหนึ่ง message จาก postbox ชื่อ *postboxname* ของ *appid* โดยจะเรียงตามลำดับเวลา message ที่เข้ามาก่อน จะถูกอ่านก่อน

URL parameter
* *tag* ไม่จำเป็นต้องระบุ แต้หากมีการระบุ tag จะเป็นการเจาะจงอ่านเฉพาะ message ที่ติด tag นั้นเท่านั้น

Body
* ไม่มี
```
$ curl -X GET "https://api.netpie.io/postbox/myappid/webbox?tag=error" -u jVjzJXaJwdJKHhF:StOAKIZhXB5CaqnIHeb7s1DfiW7mQj
```
