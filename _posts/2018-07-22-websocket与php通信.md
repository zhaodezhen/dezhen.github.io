---
published: false
---
最近学习了websocket的握手协议，打开和关闭连接等基础内容。最近用PHP实现了和websocket的双向通信，学习的概念的时候感觉看懂了内容，真正实践过程中还是会遇到各种问题，网上也有一些关于PHP和websocket的实现，但是只有自己亲手写过之后才知道其中的感受。
websocket协议提供给javascript的API就是特别简洁易用，就不多说了，下面主要说双向通信解码问题；
error：
WebSocket connection to 'ws://127.0.0.1:1999/' failed: Error in connection establishment: net::ERR_CONNECTION_REFUSED
这个原因是因为双方使用的协议不一致导致的连接失败，服务端接受到的是http Header 头信息，需要服务端来解码	

首次与客户端握手
```
function dohandshake($sock, $data, $key) {
     if (preg_match("/Sec-WebSocket-Key: (.*)\r\n/", $data, $match)) {
         $response = base64_encode(sha1($match[1] . '258EAFA5-E914-47DA-95CA-C5AB0DC85B11', true));
        $upgrade  = "HTTP/1.1 101 Switching Protocol\r\n" .
            "Upgrade: websocket\r\n" .
            "Connection: Upgrade\r\n" .
            "Sec-WebSocket-Accept: " . $response . "\r\n\r\n";
            socket_write($sock, $upgrade, strlen($upgrade));
            $this->hands[$key] = true;
     }
}	
```
字符解码
```
function decode($buffer) {
    $len = $masks = $data = $decoded = null;
    $len = ord($buffer[1]) & 127;
     if ($len === 126) {
         $masks = substr($buffer, 4, 4);
         $data = substr($buffer, 8);
       }
       else if ($len === 127) {
       $masks = substr($buffer, 10, 4);
       $data = substr($buffer, 14);
       }
       else {
           $masks = substr($buffer, 2, 4);
           $data = substr($buffer, 6);
       }
       for ($index = 0; $index < strlen($data); $index++) {
           $decoded .= $data[$index] ^ $masks[$index % 4];
       }
       return $decoded;
 }

```
字符解码 

```
public function encode($buffer) {
      $length = strlen($buffer);
      if($length <= 125) {
          return "\x81".chr($length).$buffer;
      } else if($length <= 65535) {
          return "\x81".chr(126).pack("n", $length).$buffer;
      } else {
          return "\x81".char(127).pack("xxxxN", $length).$buffer;
      }
  }
```


