# Stream and Buffer

## What is Buffer

From the definition from [Data Buffer](https://en.wikipedia.org/wiki/Data_buffer), we can see, data buffer is a real
physical storage medium.

A majority of buffers are implemented in software, which typically use the faster RAM to store temporary data, due to the
**much faster access** time compared with hard disk drives.

The main purpose including:

1. Interconnecting two digital circuits operating at different rates

2. Holding data for later use,

3. Allowing timing corrections to be made on a data stream,

4. Collecting binary data bits into groups that can then be operated on as a unit,

5. Delaying the transit time of a signal in order to allow other operations to occur.

## What is Stream

Apprarently, if we can understand the meaning of existence of Buffer, it's easier to understand Stream.

a data stream is a sequence of digitally encoded coherent signals (packets of data or data packets) used to transmit or receive information that is in the process of being transmitted.

If a file is too big and we cannot read it (load it to memory at one time), the data will be saved to Buffer area first, and then, via Stream, we can read the data in order.

## Buffer and Stream in Node.js

### Buffer API

```js
// create Buffer
const buf1 = Buffer.alloc(size, value); // return a hexadecimal byte
// create Buffer from String
const buf2 = Buffer.from("hello"); // <Buffer 68 65 6c 6c 6f>

// String to Buffer
buf2.toString(); // directlt return the string content: 'hello'
buf2.toString("hex"); // return a hexadecimal string: '68656c6c6f'
```

[Tips](https://nodejs.org/dist/latest-v12.x/docs/api/buffer.html#buffer_buffer): Instances of the Buffer class are similar to arrays of integers from 0 to 255 (other integers are coerced to this range by & 255 operation) but correspond to fixed-sized, raw memory allocations outside the V8 heap. The size of the Buffer is established when it is created and cannot be changed.

### Use Buffer in Node.js

In Node.js Stream, we don't need to directly operate Buffer as Node.js has package it in Stream class like as below:

```js
const inputStream = fs.createReadStream("input.txt");
const outputStream = fs.createWriteStream("output.txt");
inputStream.pipe(outputStream);
```

In `crypto`, buffer can be used to be filled by bytes.

Besides, in Http server, using Buffer to transfer data is more efficient than String:

```js
res.end(Buffer.from("{a:1}"));
res.end("{a:1}");
```

### Stream

Streams can be readable, writable, or both. All streams are instances of [EventEmitter](https://nodejs.org/dist/latest-v12.x/docs/api/events.html#events_class_eventemitter), indluding Writable, Readable, Duplex and Transform.

1. createWriteStream

```js
const writeS = fs.createWriteStream("output.txt");
writeS.once("open", fd => {
  for (let i = 0; i < 100; i++) {
    writeS.write(`${i}\n`);
  }
  writeS.end();
});
```

2. createReadStream

```js
const readS = fs.createReadStream("output.txt");
let content = "";
readS.on("data", chunk => {
  content += chunk;
});
readS.on("end", chunk => {
  if (!chunk) {
    console.log(content);
  }
});
```

In addition, `pipe` event can be used to access stream, in order to such as compress files:

```js
const fs = require("fs");
const r = fs.createReadStream("file.txt");
const z = zlib.createGzip();
const w = fs.createWriteStream("file.txt.gz");
r.pipe(z).pipe(w);
```

3. Duplex streams are streams that implement both the Readable and Writable interfaces.

4. Transform streams are Duplex streams where the output is in some way related to the input. Like all Duplex streams, Transform streams implement both the Readable and Writable interfaces.

When operating large size files, obviously, `Stream` is the better choice compared with `fs.readFile`.

## Reference

More official information can be seen at [document](https://nodejs.org/dist/latest-v12.x/docs/api/stream.html).
