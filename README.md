# Anully

*(Standard v1.0.3)*   

Cuando se elabora un servidor de red mediante *net.createServer(handlerServer)* se
necesita procesar de alguna manera las cabezeras del estandar HTTP (v1.1) y su cuerpo. 
El cuerpo que se recibe en el *Strema Duplex* llamado *socket*, que es el argumento
principal de la función *handlerServer*, **Anully** tiene como misión procesar
los datos de este *Stream Duplex* y convertirlo en un objeto *reqObject* para ser
mejor manejado.

He aquí demostración:

```javascript
import net from 'node:net';
import anully from 'anully';

function handler (socket) {
    let headersProccessing = true;
    socket.on('data', eventArg=> {
        if (headersProccessing) {
            // convert Buffer to object
            const obRequest = anully(eventArg);
           if (obRequest) {
                // typing headers..
                socket.write(
                    "HTTP/1.1 200 Ok\r\n" +
                    "content-type: text/html\r\n" +
                    `date: ${Date.now()}\r\n` +
                    "server: NS\r\n\r\n"
                );
                socket.write(`<h1 align="center">Your HTTP method is ${obRequest.method}</h1>`);
           }
           socket.end();
           headersProccessing = false;
        }
        else {
            // more datas from socket..
        }
    });
}
const SERVER = net.createServer(handler);

SERVER.listen(80, '127.0.0.3', function () {
    console.log(`http://${this.address().address}`);
});
```
La interfaz **Anully** es una función que procesa el buffer del
*Stream Duplex socket* y luego retorna un objeto *obRequest*
para ser mas cómodo la lectura de este.

Nota retornará *false* solo si no recibe un buffer procesable del estandar HTTP 1.1.