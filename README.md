<div align="center">

## Alternating Winsock Chat


</div>

### Description

This code is just a little beginning project of mine that I wrote when I first began learning to use winsock. Because this was my first practical application of my winsock knowledge, it is not quite so sophisticated, and it is thusly an ALTERNATING chat program. . . sorry. . .anyway, even though I wrote this code about 2 years ago, and it is useless to me now, I thought I would post it for those who may wish to learn winsock and need a good example. I tried my best to comment it, but I am really not used to using many comments in my codes, so bear with me. If you need any explanations, mail me.
 
### More Info
 
you MUST add the "mpr.lib" and "wsock32.lib" libraries to you project in order to compile this. If you need detailed instructions on how to do this, E-mail me and I can help.


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Phillip Hopkins](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/phillip-hopkins.md)
**Level**          |Intermediate
**User Rating**    |5.0 (40 globes from 8 users)
**Compatibility**  |Microsoft Visual C\+\+
**Category**       |[Internet/ Browsers/ HTML](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/internet-browsers-html__3-9.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/phillip-hopkins-alternating-winsock-chat__3-5279/archive/master.zip)

### API Declarations

```
iostream.h
winsock.h
```


### Source Code

```

// Created by Phillip D. Hopkins
// of Philware Inc.
// sites
// www.philwareinc.com (soon to be launched)
// www.philwareinc.20m.com (already up)
#include <iostream.h>
#include <winsock.h>
void ClearScreen();	//A VERY cheap and fake "Clear Screen" function
int DisplayMenu()	//A simple function to display the menu and get the response
{
	int choice;
beginning:
	ClearScreen();
	cout << endl << endl << endl << endl << endl << endl << endl << "Philware WINSOCK Test V1.0" << endl;
	cout << "Choose the correct Connection Type:" << endl;
	cout << "1. Server" << endl;
	cout << "2. Client" << endl;
	cout << "3. Exit" << endl;
	cout << "Choice: ";
	cin >> choice;
	if (choice != 1 && choice != 2 && choice != 3)
	{
		cout << "That is not one of the choices." << endl;
		goto beginning;
	}
	return choice;
}
int main()
{
	WSADATA WsaDat;				//Declares what is necessary to use winsock
	int menuchoice;				//Gets the integer from the "DisplayMenu()" function
	if (WSAStartup(MAKEWORD(1,1), &WsaDat) != 0)	//Initiallizes the WSA
	{
		cout << "WSA initialization failed.";
		cout << "Closing. . .";
		Sleep(MAKEWORD(10,30));
		goto end;
	}
	SOCKET mysocket;			//Our Handy Dandy Socket
	mysocket = socket(AF_INET, SOCK_STREAM, 0);		//Sets the properties of our socket
													//Here, it is set to a STREAM Socket
	if (mysocket == INVALID_SOCKET)					//Checks to see if it created correctly
	{
		cout << "Socket creation failed!";
		cout << "Closing. . .";
		Sleep(MAKEWORD(10,30));
		goto end;
	}
	int ip1, ip2, ip3, ip4, yesno;					//Holders for IP address, and a
													//"yesno" var for confirmation later
reipadd:	//Yah, I know it is bad, but I did use a nasty "goto" command
	cout << "\nChoose your IP address:\n";			//It has you enter the server's
	cout << "first numeral: ";						//IP address one numeral at a time
	cin >> ip1;
	cout << "second numeral:";
	cin >> ip2;
	cout << "third numeral: ";
	cin >> ip3;
	cout << "fourth numeral:";
	cin >> ip4;
	cout << "\nIs this your IP address? (1=yes, 2=no)\n" << ip1 << "." << ip2 << "." << ip3 << "." << ip4 << endl;
	cin >> yesno;				//this just shows the ip and ask for verification
	if (yesno!=1)
	{
		goto reipadd;			//here is that nasty "goto" command. . .
	}
	SOCKADDR_IN SockAddr;		//this keeps track of the socket's address info
	SockAddr.sin_port = 50;
	SockAddr.sin_family = AF_INET;
	SockAddr.sin_addr.S_un.S_un_b.s_b1 = ip1;
	SockAddr.sin_addr.S_un.S_un_b.s_b2 = ip2;
	SockAddr.sin_addr.S_un.S_un_b.s_b3 = ip3;
	SockAddr.sin_addr.S_un.S_un_b.s_b4 = ip4;
	char myscreenname[20], theirscreenname[20];	// self-explanatory :)
rescreenname:	//another reference to that "goto" command
	//lets you enter a screen name
	cout << endl << "Type in a one-word \"Screen Name\" less than 20 letters" << endl;
	cin.ignore(80, '\n');
	cin.get(myscreenname, 20);
	cin.ignore(80, '\n');
	//checks to see if you typed it right
	cout << "Your screen name is :" << myscreenname << endl << "Do you want this screen name?(1=yes 2=no)" << endl;
	cin >> yesno;
	if (yesno!=1)
	{
		goto rescreenname; //yup, this will have you retype the screen name
	}
	menuchoice=DisplayMenu();	//shows the menu and gets a choice (defined at top of code)
	if (menuchoice==1)			// if you chose to be a server. . .
	{
								// bind to IP address. . .
		if (bind(mysocket, (SOCKADDR *)(&SockAddr), sizeof(SockAddr)) == SOCKET_ERROR)
		{
			cout << "Attempt to bind failed" << endl;
			cout << "Closing. . .";
			Sleep(MAKEWORD(10,30));
			goto end;
		}
		cout << "Waiting for client. . ." << endl;
								//go into "Listen" mode. . .
		listen(mysocket, 1);
								//when request received to connect, accept it
		SOCKET TempSock = SOCKET_ERROR;
		while (TempSock == SOCKET_ERROR)
		{
			TempSock = accept(mysocket, NULL, NULL);
		}
		mysocket = TempSock;
								//send your screen name to the client
		cout << "Client Found. Sending screen name. . ." << endl;
		send(mysocket, myscreenname, sizeof(myscreenname), 0);
		cout << "Screen name sent successfully." << endl;
		cout << "Retrieving screen name from client. . ." << endl;
								//wait to receive screen name from client
		int WaitforScreenName = SOCKET_ERROR;
		while (WaitforScreenName == SOCKET_ERROR)
		{
			WaitforScreenName = recv(mysocket, theirscreenname, 20, 0);
			if ((WaitforScreenName == 0)||(WaitforScreenName == WSAECONNRESET))
			{
				cout << "Connection closed at the other end while receiving screen name! RATS!" << endl;
				cout << "Closing. . .";
				Sleep(MAKEWORD(10,30));
				goto end;
			}
		cout << "Screen Name Acquired. You are talking with: " << theirscreenname << endl;
		}
		char toclient[50], String2[50];
		int RetVal = SOCKET_ERROR;
send1:							//type and send message to client
		cout << myscreenname << ": ";
		cin.ignore(80, '\n');
		cin.get(toclient, 50);
		cin.ignore(80, '\n');
		send(mysocket, toclient, 50, 0);
//		cout << "MESSAGE SENT" << endl << "Waiting for reply. . ." << endl;
								//wait for the client to send a message. . .
		while (RetVal == SOCKET_ERROR)
		{
			RetVal = recv(mysocket, String2, 50, 0);
			if ((RetVal == 0)||(RetVal == WSAECONNRESET))
			{
				cout << "Connection closed at the other end. . .Sorry. . ." << endl;
				cout << "Closing. . .";
				Sleep(MAKEWORD(10,30));
				goto end;
			}
								//display message
//			cout << "Message Received:" << endl;
			cout << theirscreenname << ": " << String2 << endl;
			RetVal = SOCKET_ERROR;
			goto send1;			// loop and send another message
		}
//		cout << "\nThis message was sent:" << endl;
//		cout << "THIS IS FROM THE SERVER, TEST SUCCESSFUL";
//		int pause;
//		cin >> pause;
	}
	else if (menuchoice==2)
	{
								//attempt to connect to server. . .
		cout << "Trying to connect to a server. . ." << endl;
		if (connect(mysocket, (SOCKADDR *)(&SockAddr), sizeof(SockAddr)) == SOCKET_ERROR)
		{
			cout << "failed to establish connection with server" << endl;
			cout << "Closing. . .";
			Sleep(MAKEWORD(10,30));
			goto end;
		}
								//wait to receive screen name
		cout << "Server Found! Waiting to receive Screen Name. . ." << endl;
		int WaitforScreenName = SOCKET_ERROR;
		while (WaitforScreenName == SOCKET_ERROR)
		{
			WaitforScreenName = recv(mysocket, theirscreenname, 20, 0);
			if ((WaitforScreenName == 0)||(WaitforScreenName == WSAECONNRESET))
			{
				cout << "Connection closed at the other end while receiving screen name!" << endl;
				cout << "Closing. . .";
				Sleep(MAKEWORD(10,30));
				goto end;
			}
		cout << "Screen Name Acquired. You are talking with: " << theirscreenname << endl;
								//send screen name to server
		cout << "Sending your screen name. . ." << endl;
		send(mysocket, myscreenname, sizeof(myscreenname), 0);
		cout << "Screen Name Sent!" << endl;
								//wait for message from server
		cout << "Waiting for first Message from " << theirscreenname << ". . ." << endl;
		}
		int RetVal = SOCKET_ERROR;
		char String[50], toserver[50];
send2:
		while (RetVal == SOCKET_ERROR)
		{
			RetVal = recv(mysocket, String, 50, 0);
			if ((RetVal == 0)||(RetVal == WSAECONNRESET))
			{
				cout << "Connection closed at the other end" << endl;
				cout << "Closing. . .";
				Sleep(MAKEWORD(10,30));
				goto end;
			}
//			cout << "Message Received:" << endl;
			cout << theirscreenname << ": " << String << endl;
//			int pause1;
//			cout << "\nThis message was received:" << endl;
//			cout << String;
//			cin >> pause1;
		}
								//type and send message to server
		cout << myscreenname << ": ";
		cin.ignore(80, '\n');
		cin.get(toserver, 50);
		cin.ignore(80, '\n');
//		cout << "Sending message. . ." << endl;
		send(mysocket, toserver, 50, 0);
//		cout << "Message Sent. Waiting for reply from " << theirscreenname << ". . ." << endl;
		RetVal = SOCKET_ERROR;
		goto send2;				//loop back and wait for another message
	}
	else if (menuchoice==3)
	{
								//close the program
		cout << "Closing. . .";
		Sleep(MAKEWORD(10,30));
		goto end;
	}
end:
	return 0;
}
void ClearScreen()				//Here be the cheesy "Screen Clear" function
{
	cout << "\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n";
}
```

