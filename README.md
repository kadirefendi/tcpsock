#### Aşağıda, Node.js kullanarak bir TCP sunucusu oluşturmanız ve bu sunucu üzerinden gelen verileri bir HTML sayfasında görüntülemeniz için kullanabileceğiniz örnek bir kod bloğu bulunmaktadır. İlk olarak, Node.js'in kurulu olduğundan emin olun.

```js
// Gerekli modülleri içe aktarın
const net = require("net");
const http = require("http");
const fs = require("fs");

// TCP sunucusunu oluşturun
const tcpServer = net.createServer((socket) => {
  console.log("Yeni bir istemci bağlandı.");

  // İstemciden gelen verileri dinleyin ve HTTP sunucusuna iletilmek üzere bir HTML sayfasına yazın
  socket.on("data", (data) => {
    const htmlContent = `
      <!DOCTYPE html>
      <html>
      <head>
        <title>TCP Veri Gösterimi</title>
      </head>
      <body>
        <h1>Alınan Veri:</h1>
        <p>${data}</p>
      </body>
      </html>
    `;

    // HTML içeriğini bir dosyaya kaydedin
    fs.writeFile("tcp_data.html", htmlContent, (err) => {
      if (err) {
        console.error("Dosya kaydedilirken hata oluştu:", err);
      }
    });
  });
});

// HTTP sunucusunu oluşturun
const httpServer = http.createServer((req, res) => {
  // Kaydedilen HTML dosyasını okuyun ve istemciye yanıt olarak gönderin
  fs.readFile("tcp_data.html", (err, data) => {
    if (err) {
      res.writeHead(500, { "Content-Type": "text/plain" });
      res.end("Hata: HTML dosyası bulunamadı.");
    } else {
      res.writeHead(200, { "Content-Type": "text/html" });
      res.write(data);
      res.end();
    }
  });
});

// TCP sunucusunu belirtilen portta dinlemeye başlayın
const tcpPort = 3000;
tcpServer.listen(tcpPort, () => {
  console.log(`TCP Sunucusu ${tcpPort} portunda dinleniyor.`);
});

// HTTP sunucusunu belirtilen portta dinlemeye başlayın
const httpPort = 8080;
httpServer.listen(httpPort, () => {
  console.log(`HTTP Sunucusu ${httpPort} portunda dinleniyor.`);
});
```

#### Bu kod, TCP sunucusunu oluşturur, istemciden gelen verileri bir HTML sayfasına kaydeder ve bir HTTP sunucusu kullanarak bu HTML sayfasını istemcilere sunar. TCP sunucusu 3000 portunda dinlerken, HTTP sunucusu 8080 portunda dinler. Veriler, HTML sayfasının içinde görüntülenir.
