﻿<a name="title" />
# Building the Backend#

---
<a name="Overview" />
## Overview ##

A Websocket is a protocol designed to allow web applications to create a full-duplex channel over TCP between the web browser and a web server. It is fully compatible with HTTP and uses TCP port number 80. 
WebSockets allow real-time web applications to support advanced interactions between the client and the server. 

Socket.IO is a simple JavaScript library and Node.js module that allows you to create real-time bidirectional event-based communication apps simply and quickly. It simplifies the process of using WebSockets significantly.

Azure DocumentDB is a NoSQL document database service designed from the ground up to natively support JSON and JavaScript directly inside the database engine. It’s the right solution for applications that run in the cloud when predictable throughput, low latency, and flexible query are key.

This demo introduces the use of the Socket.IO module that allows for real-time bidirectional communication. Here we see how to connect, broadcast and receive messages in a chat app. It also shows how to use Azure DocumentDB, a NoSQL database, to save and retrieve messages.

<a id="goals" />
### Goals ###
In this demo, you will see how to:

1. Create bidirectional communication between client and server by using Socket.IO module

1. Add Azure DocumentDB to your app to retrieve and save messages

<a name="technologies" />
### Key Technologies ###

- [Node.js][1]
- [Socket.IO][2]
- [Azure DocumentDB][3]
- [Visual Studio Code][4]

[1]: https://nodejs.org/
[2]: http://socket.io/
[3]: http://azure.microsoft.com/en-us/services/documentdb/
[4]: https://code.visualstudio.com/

<a name="Setup" />
### Setup and Configuration ###
Follow these steps to set up your environment for the demo.

1. Download [Visual Studio Code](https://code.visualstudio.com/Download) for your platform and follow the [installation and setting up instructions](https://code.visualstudio.com/Docs/editor/setup).

1. Install [Node.js](https://nodejs.org/en/download/).

1. Open File Explorer and browse to the **source/Setup** folder.

1. Copy the **nodecamp-buildingthebackend-snippets** folder and paste it into the Visual Studio Code Extensions folder to install the JavaScript snippets for this demo. Depending on your platform it is located:
 * **Windows**: `%USERPROFILE%\.vscode\extensions`
 * **Mac**: `$HOME/.vscode/extensions` 
 * **Linux**: `$HOME/.vscode/extensions`

1. Open a **command prompt/terminal** according to your platform in the **source/Begin/Chatroom** folder.

1. Run **npm install** to install all the missing dependencies.

	![Installing Missing npm Packages](images/VSCode/installing-missing-npm-packages.png?raw=true "Installing Missing npm Packages")

	_Installing Missing npm Packages_

<a name="CodeSnippets" />
### Using the Code Snippets ###

Throughout the demo document, you will be instructed to insert code blocks. For your convenience, most of this code is provided as code snippets, which you can access from within Visual Studio Code to avoid having to add it manually.

> **Note:** This demo is accompanied by a starting solution located in the **Begin** folder that allows you to follow the demo. Inside the source code you will also find an **End** folder containing the code that results from completing the steps in the demo. You can use this folder as guidance if you need additional help as you work through this demo.

---

<a name="Demo" />
## Demo ##
This demo is composed of the following segments:

1. [Creating a chat server with Socket.IO](#segment1)
1. [Saving messages to an Azure DocumentDB database](#segment2)
1. [Creating a DocumentDB database account](#segment3)
1. [Configuring local environment variables](#segment4)
1. [Appendix: Saving messages to a MongoDB database](#appendix1)

<a name="segment1" />
### Creating a chat server with Socket.IO ###

1. In the command prompt/terminal run **npm install --save socket.io** to install the socket.io package.

	> **Note:** This will install socket.io to your project and add it to the package.json file.

	![Installing Socket.IO package](images/VSCode/installing-socket-io-package.png?raw=true "Installing Socket.IO package")

	_Installing Socket.IO package_

1. Run **code .** to open the current directory with **Visual Studio Code**.

1. In the **Explore** view, open the **package.json** file located in the root of the folder.

	![Showing the package.json file](images/VSCode/showing-the-package-json-file.png?raw=true "Showing the package.json file")

	_Showing the package.json file_

1. In the **Explore** view, create a new **socketio.js** file by clicking the **New File** icon and fill its content with the following code snippet.

	> **Speaking point:** We will log each user entering the chatroom by hooking a callback function to be executed on every single "connection" event via WebSocket to our HTTP server. 

	(Code Snippet - _BuildingTheBackend-SocketioOnConnection_)

	````JavaScript
	module.exports = function (io, config) {
		 io.on('connection', function (socket) {
			  console.log('a user connected');
		 });
	};
	````

	![Creating a new socketio.js file](images/VSCode/creating-a-new-socketiojs-file.png?raw=true "Creating a new socketio.js file")

	_Creating a new socketio.js file_

1. Add the following code snippet inside the callback of the **connection** event.

	> **Speaking point:** To do the same for when a user leaves, we have to hook up to the “disconnect” event for each socket.

	(Code Snippet - _BuildingTheBackend-SocketioOnDisconnect_)

	````JavaScript
	socket.on('disconnect', function () {
		console.log('user disconnected');
	});
	````

1. Add the following code snippet inside the callback of the **connection** event below the **disconnect** event handler.

	> **Speaking point:** Socket.IO gives us a function called **emit** that we can use to send an event. We will use it to broadcast any message received on the "chat" channel to all the other connections on this socket.

	(Code Snippet - _BuildingTheBackend-SocketioOnChat_)

	````JavaScript
	socket.on('chat', function (msg) {
		socket.broadcast.emit('chat', msg);
	});
	````

1. Open the **www** file located in the **bin** folder and insert the following code snippet at the end of the file.

	> **Speaking point:** Here we capture the HTTP server created by express in a variable called **server** and pass it to the socket.io module so it can attach to it.
We then pass the socket.io instance to the module we created.

	(Code Snippet - _BuildingTheBackend-SocketioInitialization_)

	````JavaScript
	var io = require('socket.io')(server);
	require('../socketio')(io);
	````

1. Run the application using Visual Studio Code debugger. To do this, you will first need to setup your debugging launch configuration file (**launch.json**). Bring up the **Debug** view by clicking on the Debugging icon in the View Bar on the side of Visual Studio Code. Click on the Configure gear icon and select **Node.js** as your Debug Environment; this will generate a **launch.json**. Make sure that the **Lunch** configuration is selected in the dropdown and press **F5** to start debugging. For more infomation, see the [Debugging](https://code.visualstudio.com/Docs/editor/debugging) documentation.

	![Launching the application in Debug mode with Visual Studio Code](images/VSCode/launching-the-app-in-debug-mode-with-vscode.png?raw=true "Launching the application in Debug mode with Visual Studio Code")

	_Launching the application in Debug mode with Visual Studio Code_

1. Open two instances of your browser, navigate to **http://localhost:3000** and show that messages are being broadcasted to all the connections on the socket.

	![Testing socket.io](images/VSCode/testing-socket-io.png?raw=true "Testing socket.io")

	_Testing socket.io_

1. Switch back to Visual Studio Code and stop the debugger.

<a name="segment2" />
### Saving messages to an Azure DocumentDB database ###

1. Switch back to the command prompt/terminal and run **npm install --save documentdb** to install the documentdb package.

	![Installing documentdb package](images/VSCode/installing-documentdb-package.png?raw=true "Installing documentdb package")

	_Installing documentdb package_

1. Go back to Visual Studio Code. In the **Explore** view, add a new JavaScript file named **docDBUtils.js**.

	> **Speaking Point:**  We will create a few helper functions in this new file.

1. Add the following code snippet in the **docDBUtils.js** file you created.

	(Code Snippet - _BuildingTheBackend-DocDBUtilsStructure_)

	````JavaScript
	var DocDBUtils = {
	};

	module.exports = DocDBUtils;
	````

1. Add the following function inside the DocDBUtils object definition to get or create a database.

	> **Speaking Point:**  This helper function will help to create or get a  database.


	(Code Snippet - _BuildingTheBackend-DocDBUtilsGetOrCreateDatabase_)

	````JavaScript
    getOrCreateDatabase: function (client, databaseId, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                    name: '@id',
                    value: databaseId
                }]
        };
        
        client.queryDatabases(querySpec).toArray(function (err, results) {
            if (err) {
                callback(err);

            } else {
                if (results.length === 0) {
                    var databaseSpec = {
                        id: databaseId
                    };
                    
                    client.createDatabase(databaseSpec, function (err, created) {
                        callback(null, created);
                    });

                } else {
                    callback(null, results[0]);
                }
            }
        });
    },
	````

1. Add the following function inside the DocDBUtils object definition to get or create a collection below the previous function.

	> **Speaking Point:**  This helper function will help to create or get a collection in a database.

	(Code Snippet - _BuildingTheBackend-DocDBUtilsGetOrCreateCollection_)

	````JavaScript
	getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                    name: '@id',
                    value: collectionId
                }]
        };
        
        client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
            if (err) {
                callback(err);

            } else {
                if (results.length === 0) {
                    var collectionSpec = {
                        id: collectionId
                    };
                    
                    var requestOptions = {
                        offerType: 'S1'
                    };
                    
                    client.createCollection(databaseLink, collectionSpec, requestOptions, function (err, created) {
                        callback(null, created);
                    });

                } else {
                    callback(null, results[0]);
                }
            }
        });
    },
	````

1. Then, add the following function at the end of the DocDBUtils object definition to simplify the initialization of a collection.

	> **Speaking Point:**  This helper function will help to create or get a collection in a database by just calling the ones defined previously.

	(Code Snippet - _BuildingTheBackend-DocDBUtilsInitCollection_)

	````JavaScript
    initCollection: function (client, databaseId, collectionId, callback) {
        var self = this;
        
        DocDBUtils.getOrCreateDatabase(client, databaseId, function (err, db) {
            if (err) {
                callback(err);
            } else {
                DocDBUtils.getOrCreateCollection(client, db._self, collectionId, callback);
            }
        });
    }
	````

1. Now, open the **socketio.js** file you created in the previous section and add the following code snippet at the beginning of the file.

	(Code Snippet - _BuildingTheBackend-DocDBInitialization_)

	````JavaScript
	var DocumentDBClient = require('documentdb').DocumentClient;
	var docdbUtils = require('./docDBUtils');
	````

1. Add the following code snippet below the `console.log('a user connected');`.

	> **Speaking point:** We want to connect to the database using the URI we have in the **DOCUMENT_DB_HOST** environment variable using the key we have in the **DOCUMENT_DB_AUTH_KEY** environment variable, and insert the chat message received in the socket connection.

	(Code Snippet - _BuildingTheBackend-DocDBClientInitialization_)

	````JavaScript
	var docDbClient = new DocumentDBClient(process.env.DOCUMENT_DB_HOST, {
		masterKey: process.env.DOCUMENT_DB_AUTH_KEY
	});
	````

1. Add the following code snippet below the previous snippet.

	> **Speaking Point:**  We want to emit the previously received chat messages on the same channel.

	(Code Snippet - _BuildingTheBackend-DocDBLoadMessages_)

	````JavaScript
	docdbUtils.initCollection(docDbClient, 'chatroom', 'messages', function (err, collection) {
		if (err) {
			console.warn(err.message);
		} else {
			docDbClient.queryDocuments(collection._self, 'SELECT r.content FROM root r')
				.forEach(function (err, msg) {
					if (msg) {
						console.log('emitting chat');
						socket.emit('chat', msg.content);
					}
				});
		}
	});
	````

1. Add the following code snippet inside the chat event handler and before the **socket.broadcast.emit** function call.

	> **Speaking point:** We want to insert the chat message received in the socket connection.

	(Code Snippet - _BuildingTheBackend-DocDBSaveMessages_)

	````JavaScript
	docdbUtils.initCollection(docDbClient, 'chatroom', 'messages', function (err, collection) {
		 if (err) {
			  console.warn(err.message);
		 } else {
			  var documentDefinition = { content: msg };
			  docDbClient.createDocument(collection._self, documentDefinition, function (err, o) {
					if (err) {
						 console.warn(err.message);
					} else {
						 console.log("chat message inserted into db: " + msg);
					}
			  });
		 }
	});
	````

<a name="segment3" />
### Creating a CosmosDB database account ###

1. Sign in to the online [Microsoft Azure portal](http://bit.ly/azportal).

1. In the Jumpbar, click **New**, then **Databases**, and **Azure CosmosDB**.

	_Creating a new DocumentDB account_

1. In the **New CosmosDB account** blade, specify the desired configuration for the CosmosDB account.

	![Setting up the new CosmosDB account](images/VSCode/setting-up-the-new-cosmosdb-account.png?raw=true "Setting up the new CosmosDB account")

	_Setting up the new DocumentDB account_

1. Once the new DocumentDB account options are configured, click **Create**. It can take a few minutes for the DocumentDB account to be created. To check the status, you can monitor the progress on the Startboard or from the Notifications hub.

1. After the DocumentDB account is created, it is ready to use with the default settings in the online portal. Now navigate to the Keys blade of your DocumentDB account; we will use these values in the web application we create next.

	![Copying the DocumentDB account keys](images/VSCode/copying-the-keys.png?raw=true "Copying the DocumentDB account keys")

	_Copying the DocumentDB account keys_

<a name="segment4" />
### Configuring local environment variables ###

1. Switch back to Visual Studio Code and open the **launch.json** file located in the **.vscode** folder.

1. Add the following variables inside the **configurations/env** node, replacing the values with those obtained in the previous section.

	````C#
	"DOCUMENT_DB_HOST": "https://{your-document-db}.documents.azure.com:443/",
    "DOCUMENT_DB_AUTH_KEY": "{Your-DocumentDb-primary-key}"
	````

	![Setting up the Environment Variables](images/VSCode/setting-up-the-env-variables.png?raw=true "Setting up the Environment Variables")

	_Setting up the Environment Variables_


1. Run the application using the Visual Studio Code debugger.

1. Open your browser, navigate to **http://localhost:3000** and send a few messages.

1. Restart the browser and navigate again to **http://localhost:3000**. Show how the messages you sent are retrieved from the DocumentDB account.

	![Retrieving messages from DocumentDB](images/VSCode/retrieving-messages-from-documentdb.png?raw=true "Retrieving messages from DocumentDB")

	_Retrieving messages from DocumentDB_

1. Switch back to Visual Studio Code and stop the debugger.

---

<a name="appendix1" />
### Appendix: Saving messages to a MongoDB database ###

1. Install the **MongoDB** package as you did with the **socket.IO** package in previous segment.

1. Add the following code before `var app = express();` in the **app.js** file:

	`var mongo = require('mongodb').MongoClient;`

1. Add the next code snippet inside the "connection" event and after  `console.log('a user connected');`:

	> **Speaking point:** We want to give our users the last 10 messages from the chatroom so they have some context when they have just joined. To do that, we need to connect to Mongo. We use the **limit** function to limit the results to only 10 messages. We will stream the results from Mongo so that we emit them as soon as they arrive to the chatroom.
	
	(Code Snippet - _NodeJsSocketIO-mongoDB-retrieve-messages_)

	````JavaScript
    mongo.connect(process.env.MONGODB_URI, function (err, db) {
        if(err){
            console.warn(err.message);
        } else {
            var collection = db.collection('chat messages')
            var stream = collection.find().sort().limit(10).stream();
            stream.on('data', function (chat) { console.log('emitting chat'); socket.emit('chat', chat.content); });
        }
    });
	````

1. Add the next code snippet inside the "chat" event and before the `socket.broadcast.emit('chat', msg);` line:

	> **Speaking point:** We want to connect to the database using the URI we have in the **MONGODB_URI** environment variable and insert the chat message received in the socket connection.

	(Code Snippet - _NodeJsSocketIO-mongoDB-save-message_)

	````JavaScript
	mongo.connect(process.env.MONGODB_URI, function (err, db) {
		if (err) {
			console.warn(err.message);
		} else {
			var collection = db.collection('chat messages');
			collection.insert({ content: msg }, function (err, o) {
				if (err) { console.warn(err.message); }
				else { console.log("chat message inserted into db: " + msg); }
			});
		}
	});
	````

---

<a name="summary" />
## Summary ##

By completing this demo, you have learned how to create a simple chat application where communication between users and server is implemented using the Socket.IO module. You have also seen how to save/retrieve messages to/from a NoSQL database like Azure DocumentDB.

---
