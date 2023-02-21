---
title: Breaking Down the Code; Creating a P2P Chat Client with C++ and QTNetwork.
tags: ['C++', 'QTNetwork', 'P2P', 'chat client', 'network programming']
date: 2023-02-21
author: "Axel Vanherle"
---

In this blog post, we'll explore how to create a peer-to-peer chat client using C++ and QTNetwork. This chat client allows users to connect with each other using the TCP protocol, communicate through sending and receiving messages, and receive a list of connected peers on first connect. The chat client is designed to be extensible, with new peers being added dynamically, and with the ability to send and receive messages to and from all connected peers.

This was all done for the course in network programming, but I added some extra features that weren't required, leveling up my skills in Qt, so to speak.

But what was the challenge? Before I explain my code, I'm going to talk about what the end result should've been.

As part of their coursework, each student was required to create a TCP peer-to-peer chat client using C++ OOP style. This involved creating a chat client that could connect with one other peer, with the IP address provided as a command line argument. The first peer, however, did not require an argument. When a client first connects, the chat client needed to be able to receive a list of other peers from the first peer, and connect to these new peers. Finally, the chat client needed to be capable of sending and receiving messages to and from all other peers in the network. Overall, this project required students to demonstrate their understanding of fundamental concepts in network programming, as well as their ability to apply these concepts in a practical project.

If this is still not clear, have a look at this image. This explain the topology of a peer-to-peer network. Compare this to the topology of a regular chat service, where the server is responsible for sending and receiving all messages to and from clients.

peer-to-peer             |  server based
:-------------------------:|:-------------------------:
![p2pexample](/img/21-02-23_p2ptcpchatclient/P2P_network.png)  |  ![serverexample](/img/21-02-23_p2ptcpchatclient/Server_network.png)

Now that you know what it is about, I will explain my code and why I did certain things as best as I can. I will be using small code blocks, to explain my code in sections to make it easier to understand. Note that I am not writing this blog post to boast or even attempt to teach anyone anything. I simply enjoy writing and plan to maintain this blog, so I can look back on it. If someone picks something up or learns something new, even better. To get a bigger picture of the entire project, head to the GitHub found [here](https://github.com/axelvanherle/P2P-TCP-chatclient-NP).

Let's start where the code starts, in the main. The entire `main.cpp` file can be found [here](https://github.com/axelvanherle/P2P-TCP-chatclient-NP/blob/main/p2pchatclient/main.cpp), I will be pulling code blocks from this and explaining it in more detail.

It starts with the includes. These are needed for the various functions i use later on. Most noteworthy are these;
- `<QObject>`: This header is used for QObject, which is the base class for all Qt objects that can emit signals and slots.
- `<QTcpSocket>` and `<QTcpServer>`: These headers provide classes for creating TCP clients and servers, respectively, for network communication.
- `<QAbstractSocket>`: This header provides an abstract base class for all socket classes in Qt.
- `<QDebug>`: This header provides debugging and logging functionality.
- `<QInputDialog>`: This header provides a class for creating dialog boxes that prompt the user for input.
- `<QApplication>` and `<QtWidgets>`: These headers are used for creating GUI applications with Qt, including widgets and other graphical elements.


After the includes are done I initialize a Qt application using the QApplication class and creates an instance of it named `a`. The `TcpClient` object is then created using the default constructor.
```cpp
QApplication a(argc, argv);

TcpClient client;
```

This code creates a graphical user interface (GUI) with several widgets using the Qt library.

The widgets created are:
- `outputLabel`: a `QLabel` widget with the text "Enter message:"
- `inputLineEdit`: a` QLineEdit` widget for the user to enter text
- `sendButton`: a `QPushButton` widget with the text "Send"
- `inputLabel`: a `QLabel` widget with the text "Received messages:"
- `receivedTextEdit`: a `QPlainTextEdit` widget for displaying received messages. It is set to read-only, has a maximum block count of 100, - and is able to display multiple lines of text.
- `debugLabel`: a `QLabel` widget with the text "Debug messages:"
- `debugTextEdit`: a `QPlainTextEdit` widget for displaying debug messages. It is set to read-only, has a maximum height of 110 pixels, has a maximum block count of 100, and is styled with red text.

The widgets are then added to a `QVBoxLayout`, which is a vertical layout manager that arranges the widgets vertically. Finally, the layout is set as the layout for the window widget.
```cpp
QLabel* outputLabel = new QLabel("Enter message:");
QLineEdit* inputLineEdit = new QLineEdit();
QPushButton* sendButton = new QPushButton("Send");
QLabel* inputLabel = new QLabel("Received messages:");
QPlainTextEdit* receivedTextEdit = new QPlainTextEdit();
receivedTextEdit->setReadOnly(true);
receivedTextEdit->setMaximumBlockCount(100);
QLabel* debugLabel = new QLabel("Debug messages:");
QPlainTextEdit* debugTextEdit = new QPlainTextEdit();
debugTextEdit->setReadOnly(true);
debugTextEdit->setMaximumHeight(110);
debugTextEdit->setMaximumBlockCount(100);
debugTextEdit->setStyleSheet("color: red;");

QVBoxLayout* layout = new QVBoxLayout();
layout->addWidget(outputLabel);
layout->addWidget(inputLineEdit);
layout->addWidget(sendButton);
layout->addWidget(inputLabel);
layout->addWidget(receivedTextEdit);
layout->addWidget(debugLabel);
layout->addWidget(debugTextEdit);
window.setLayout(layout);
```

Then there's this part. It doesn't do much; it simply checks whether command line arguments have been passed. If they have, the `firstConnect` function is called.
```cpp
if (argc == 3)
    client.firstConnect(argv[1], atoi(argv[2]));
```

This snippet creates a connection between a `QTcpSocket` and the `TcpClient` object. When a new connection is made, it prints the peer address and sets up a signal-slot connection to read incoming data from all connected sockets. The code is a lambda function that is executed when a new connection is made that takes a `QTcpSocket` pointer as its argument. This function first prints the peer address of the socket using `qDebug()`. It then appends the same information to the `debugTextEdit` using `appendPlainText()`. Finally, it sets up a signal-slot connection between the socket's `readyRead` signal and the `readFromAll` slot of the `TcpClient` object using the `QObject::connect()` function.
```cpp
QObject::connect(&client, &TcpClient::newConnection, [&](QTcpSocket *socket)
{
    qDebug() << "New connection from: " << socket->peerAddress().toString();
    debugTextEdit->appendPlainText("New connection from: " + socket->peerAddress().toString());
    QObject::connect(socket, &QTcpSocket::readyRead, &client, &TcpClient::readFromAll);
});
```

This code uses a lambda function to connect the `newMessageReceived` signal of the `TcpClient` object to a slot. When a new message is received, the signal is emitted and the lambda function is called. The function then logs the received message using `qDebug` and appends it to the `receivedTextEdit` widget.
```cpp
QObject::connect(&client, &TcpClient::newMessageReceived, [&](QString message)
{
    qDebug() << "New message received: " << message;
    receivedTextEdit->appendPlainText(message);
});
```

This is a lambda function that is used to connect the click signal of the `sendButton` to a slot that sends a message to all peers. When the `sendButton` is clicked, the function gets executed. It first retrieves the message entered by the user from the `inputLineEdit`, and checks if it is empty. If it is, it displays an error message using a `QMessageBox`. Otherwise, it logs the message to the `debugTextEdit`, sends it to all connected peers using the `sendToAll` function of the `TcpClient` object, and clears the `inputLineEdit`.
```cpp
QObject::connect(sendButton, &QPushButton::clicked, [&]()
{
    QString message = inputLineEdit->text();
    if (message.isEmpty())
    {
        // Show message box with error
        QMessageBox::warning(&window, "Error", "Message cannot be empty!");
        return;
    }
    qDebug() << "Message sent: " << message;
    debugTextEdit->appendPlainText("Message sent: " + message);
    client.sendToAll(message);
    inputLineEdit->clear();
});
```

And here we are, at the end of the main with the last two lines, but fear not; there is more. `window.show()` makes the window visible to the user. The second line `return a.exec()` starts the event loop of the Qt application, which waits for and processes events.
```cpp
window.show();
return a.exec();
```

Now we move on to the `TcpClient` class. I will not be explaining the header file at all, since this is just the declaration of the functions, slots, etc. The header file can be found [here](https://github.com/axelvanherle/P2P-TCP-chatclient-NP/blob/main/p2pchatclient/tcpclient.h) and the CPP file can be found [here](https://github.com/axelvanherle/P2P-TCP-chatclient-NP/blob/main/p2pchatclient/tcpclient.cpp).

This code snippet defines the constructor for the `TcpClient` class, which is a subclass of the `QObject` class. This initializes the server socket, connects the `newConnection()` signal to the `handleNewConnection()` slot to handle new connections (this is self-explanatory), and starts listening for incoming connections on a specified port number. This means that when a new connection is made, the `handleNewConnection()` slot will be called.
```cpp
TcpClient::TcpClient(QObject *parent)
    : QObject(parent)
{
    // Create the server socket and listen for connections
    server = new QTcpServer(this);
    connect(server, SIGNAL(newConnection()), this, SLOT(handleNewConnection()));
    server->listen(QHostAddress::Any, 24042);
}
```

Here we define the `handleNewConnection()` slot. The purpose of this slot is to handle a new incoming connection from a client to the server. It also sends the list of currently connected peers to the new client using the `getPeers()` function, connects the `readyRead()` signal of the socket to the `readFromAll()` slot of the `TcpClient` object, adds the socket to the list of connected sockets, and emits a `newConnection()` signal to indicate that a new client has connected.
```cpp
void TcpClient::handleNewConnection()
{
    QTcpSocket *socket = server->nextPendingConnection();
    std::string peers = getPeers();
    socket->write(peers.c_str());
    connect(socket, SIGNAL(readyRead()), this, SLOT(readFromAll()));
    m_sockets.append(socket);

    emit newConnection(socket);
}
```

This code snippet defines the `sendToAll()` function. This function allows for the `TcpClient` object to send a message to all currently connected clients with a single call.
```cpp
void TcpClient::sendToAll(QString message)
{
    for (QTcpSocket *socket : m_sockets)
    {
        socket->write(message.toUtf8());
    }
}
```

This code snippet defines the `readFromAll()` slot for the class. The purpose of this slot is to read incoming data from all connected clients and emit a signal for each new message received. It uses a for each loop to iterate over the list of sockets, and check each one if there is data available to read. After reading, it converts it to a `QString` object, and emits a `newMessageReceived()` signal for each new message received.
```cpp
void TcpClient::readFromAll()
{
    for (QTcpSocket *socket : m_sockets)
    {
        while (socket->bytesAvailable() > 0)
        {
            QString message = QString::fromUtf8(socket->readAll());
            emit newMessageReceived(message);
        }
    }
}
```

Now here's a big boy code block, are you ready? This code defines the `firstConnect()` function that connects to a peer with the specified IP address and port number. It sends a `"Hello there!"` message to the peer, waits for a response and if the connection is successful, the function reads the response using the `readAll()` function, which returns the response as a string. It then parses this string to extract new IP addresses and port numbers using a `std::istringstream` object and a `getline()` function. It then connects to each of these new peers in turn, sending another `"Hello there!"` message and waiting for a response before adding them to the list of connected sockets. If the connection is successful, the function emits a `newConnection()` signal, adds the new socket to the list of connected sockets using the `append()` function, and connects the `readyRead()` signal of the new socket to the `readFromAll()` slot using the `connect()` function. If the connection fails, the function prints an error message to the console.
```cpp
void TcpClient::firstConnect(std::string firstIp, int firstPort)
{
    QTcpSocket *firstSocket = new QTcpSocket(this);
    // Get me a damn socket! Grrrr
    firstSocket->connectToHost(firstIp.c_str(), firstPort);
    firstSocket->write("Hello there!");
    firstSocket->waitForBytesWritten(1000);
    firstSocket->waitForReadyRead(1000);

    if (firstSocket->waitForConnected())
    {
        connect(firstSocket, SIGNAL(readyRead()), this, SLOT(readFromAll()));
        m_sockets.append(firstSocket);
        emit newConnection(firstSocket);
        qDebug() << "Connected to: " << firstIp.c_str() << ":" << firstSocket;

        std::string recv;
        recv = firstSocket->readAll();

        // Parse the received buffer filled with new IP and Ports.
        std::istringstream ss(recv);
        std::string token, newIP, newPort;
        getline(ss, token); // discard first line

        while (getline(ss, token))
        {
            std::string temp;
            std::stringstream tokenStream(token);
            getline(tokenStream, newIP, ':');
            getline(tokenStream, newPort);

            QTcpSocket *socket = new QTcpSocket(this);

            socket->connectToHost(newIP.c_str(), atoi(newPort.c_str()));
            socket->write("Hello there!");
            socket->waitForBytesWritten(1000);
            socket->waitForReadyRead(1000);

            temp = socket->readAll();
            // Are we connected?
            if (socket->state() == QAbstractSocket::ConnectedState)
            {
                std::cout << "Connected to " << newIP << ":" << newPort << " successfully!" << std::endl;
                connect(socket, SIGNAL(readyRead()), this, SLOT(readFromAll()));
                m_sockets.append(socket);
                emit newConnection(socket);
            }
            else
            {
                std::cout << "Failed to connect to " << newIP << ":" << newPort << "." << std::endl;
            }
        }
    }
    else
    {
        qDebug() << "Failed to connect to: " << firstIp.c_str() << ":" << firstSocket;
    }
}
```

This function returns a list of peers connected to the `TcpClient` object as a string. The function first initializes the `peerList` variable with the string `"NEWCON"` followed by a newline character. Then, it loops through each socket in the `m_sockets` list and checks if it is in a connected state using the `QTcpSocket::state()` function. If the socket is connected, it retrieves the IP address of the peer using `socket->peerAddress().toIPv4Address()` and converts it to a `QString`. It then appends the IP address, followed by the string `":24042"` and a newline character, to the `peerList` variable. Finally, the function converts the variable to a `std::string` and returns it, for other functions to call and use.
```cpp
std::string TcpClient::getPeers(void)
{
    QString peerList = "NEWCON";
    peerList += '\n';
    for (QTcpSocket *socket : m_sockets)
    {
        if(socket->state()== QTcpSocket::ConnectedState)
        {
            QString address = QHostAddress(socket->peerAddress().toIPv4Address()).toString();
            peerList += address + ":" + "24042" + '\n';
        }
    }

    std::string temp = peerList.toStdString();
    return temp;
}
```

In conclusion, I found this project to be very enjoyable and challenging. I had never worked with QT or CPP in this way before, so it's safe to say that it has leveled up my skills. This was similar to what happened last year in the networking course when I worked with C, which gave me a better understanding of C itself. It turns out that OOP is not such a bad thing after all, who would've though? This project also gave me a better appreciation for QT. At the beginning of the project, I wanted to switch to Berkeley sockets like I was used to in C, but I ended up persevering and it was worth it. Not only am I now better at CPP, but this is also my first GUI-based application! Hooray! I went beyond what I was supposed to do, and I have to say that I'm pretty proud of it.

I do wonder how the rest of the class did on the project. I only got to see their work when I asked them in class, and it's a bit disappointing that no one else created a public repository like mine. I really enjoy reading through them. However, I don't think many people attempted the project, which doesn't bother me too much since creating it wasn't a requirement. All, other students were free to use it. Additionally, the next assignment is an individual project worth more, so this project was just an introduction to the next one for me.

I hope you enjoyed reading this post! If you have any questions or if there's something you don't understand, don't hesitate to ask.
