Usage
=====
To build the R-Type project:

```bash
∼/B-CPP-500> ./build_linux.sh
```
To use R-Type Server:
```bash
∼/B-CPP-500>  ./build/rtype_server TcpServerAddr TcpServerPort UdpServerAddr UdpServerPort
```
ex : ./build/r-type-server 127.0.0.1 1234 127.0.0.1 1235

To use R-Type Client:
```bash
∼/B-CPP-500>  ./build/rtype_client TcpServerAddr TcpServerPort UdpServerAddr UdpServerPort UdpClientAddr UdpClientPort
```
ex : ./build/r-type-client 127.0.0.1 1234 127.0.0.1 1235 127.0.0.1 3454