# Networking

## TCP 

1. Here is how you could write an echo server that can handle one client at a time 👉

    ```rust

    use std::io::{Read, Result, Write};

    fn main() -> Result<()> {
        let listener = std::net::TcpListener::bind("0.0.0.0:12345")?;

        // This single threaded server can handle only one incoming connection at a
        // time.
        for stream in listener.incoming() {
            let mut stream = stream?;
            let mut buffer = [0u8; 4096];
            let count = stream.read(&mut buffer)?;
            stream.write_all(&buffer[0..count])?;
        }
        Ok(())
    }

    ```

2. And this is how you write and read data over a `TcpStream` as a client 👇

    ```rust
    use std::io::{Read, Result, Write};

    fn main() -> Result<()> {
        let mut stream = std::net::TcpStream::connect("127.0.0.1:12345")?;

        stream.write_all(&[0, 1, 2, 3])?;

        let mut buffer = [0u8; 4];
        stream.read_exact(&mut buffer)?;
        println!("Received {buffer:?}");

        Ok(())
    }
    ```

## UDP

1. If you want to use the User Datagram Protocol, which provides no guarantees about the order in which frames are sent nor guarantees delivery, you can use the 👉 UdpSocket.

    ```rust
    use std::net::UdpSocket;

    fn main() -> std::io::Result<()> {
        let socket = UdpSocket::bind("127.0.0.1:34254")?;

        // Receives a single datagram message on the socket. If `buf` is too small to hold
        // the message, it will be cut off.
        let mut buf = [0; 10];
        let (amt, src) = socket.recv_from(&mut buf)?;

        // Redeclare `buf` as slice of the received data and send reverse data back to origin.
        let buf = &mut buf[..amt];
        buf.reverse();
        socket.send_to(buf, &src)?;
        Ok(())
    }
    ```

## Lesson Summary 

In this lesson, you've learned about TCP and UDP through examples, explored how to write an echo server that can handle one client at a time, and how to use User Datagram Protocol.
