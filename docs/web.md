Part 2: Server
==============

Description
===========
The server is the main engine that will interfere between the game and the client.
It will exchange all data with the game and execute these instructions.
At the same time, it send the information to the client.

Communication
=============
In this part we will see in more details the communication protocol
and the management of the commands received by the server.

we use boost asio library to manage the communication between the server and the client.

we use a UDP Server and TCP Server
to communicate between the client and the server.

the UDP for game and TCP for Menu and first Connection

We use a serialization of data in binary format to send the data
between the client and the server.

Connection
==============

The client will connect to the server with a TCP connection.
The server will send the client the list of the games available.
The client will choose the game he want to play and the server will send him
the information about the game.

The client will send the server the information about the game he want to play.
The server will send the client the information about the game.

- main for server connection

```Cpp
int main(int argc, char const *argv[])
{
	std::string tcpServerAddr, udpServerAddr;
  	int tcpServerPort, udpServerPort;

  	if (argc != 5) {
  	  	std::cout << "./r-type-server serverAddress serverPort\n";
  	  	return 84;
  	} else {
  	  	tcpServerAddr = std::string(argv[1]);
  	  	tcpServerPort = atoi(argv[2]);
		udpServerAddr = std::string(argv[3]);
  	  	udpServerPort = atoi(argv[4]);
  	}
	std::shared_ptr<bool> isRunning = std::make_shared<bool>(true);
	std::unique_ptr<ServerNetworking> serverNetworking = std::make_unique<ServerNetworking>(tcpServerAddr, tcpServerPort, udpServerAddr, udpServerPort, isRunning);

    ServerCore serverCore(std::move(serverNetworking), isRunning);
    
	serverCore.Loop();
    
	serverCore.End();

	return 0;
}
```

- main for client connection

```Cpp
int main(int argc, char** argv)
{
    std::string tcpServerAddr, udpServerAddr;
    int tcpServerPort, udpServerPort;
    
    if (argc != 5) {
      std::cout << "./r-type-client serverAddress serverPort\n";
      return 84;
    } else {
      tcpServerAddr = std::string(argv[1]);
      tcpServerPort = atoi(argv[2]);
      udpServerAddr = std::string(argv[3]);
      udpServerPort = atoi(argv[4]);
    }
    
    std::shared_ptr<bool> isRunning = std::make_shared<bool>(true);
    std::unique_ptr<ClientNetworking> clientNetworking = make_unique<ClientNetworking>(tcpServerAddr, tcpServerPort, udpServerAddr, udpServerPort, isRunning);
    
    ClientCore clientCore(std::move(clientNetworking), isRunning);
    
    clientCore.Loop();
    
    clientCore.End();

    return 0;
}
```
Architecture for server :
![](/assets/Server/class_model.png)
