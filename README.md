# ngx-socket-io
[![Build Status](https://travis-ci.org/rodgc/ngx-socket-io.svg?branch=master)](https://travis-ci.org/rodgc/ngx-socket-io)
[![npm version](https://badge.fury.io/js/ngx-socket-io.svg)](https://badge.fury.io/js/ngx-socket-io)
[![npm downloads](https://img.shields.io/badge/Downloads-132%2Fmonth-brightgreen.svg)](https://github.com/rodgc/ngx-socket-io)

[Socket.IO](http://socket.io/) module for Angular 4

## Install
``` npm install ngx-socket-io ```

## How to use

### Import and configure SocketIoModule

```ts
import { SocketIoModule, SocketIoConfig } from 'ngx-socket-io';

const config: SocketIoConfig = { url: 'http://localhost:8988', options: {} };

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    SocketIoModule.forRoot(config) 
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

We need to configure ```SocketIoModule``` module using the object ```config``` of type ```SocketIoConfig```, this object accepts two optional properties they are the same used here [io(url[, options])](https://github.com/socketio/socket.io-client/blob/master/docs/API.md#iourl-options).

Now we pass the configuration to the static method ```forRoot``` of ```SocketIoModule```


### For Angular 6 Project in version 1.0.8

 To be sure that you are using Angular 6, check your version:

```bash

 ng -v

```

If you are using Angular 6 you need change two thing

1. Install rxjs-compat

```bash

npm install rxjs-compat

```

2. Change polyfills.ts

Add this code in the first line of the file:

```ts

(window as any).global = window;

```

### Using your socket Instance

The ```SocketIoModule``` provides now a configured ```Socket``` service that can be injected anywhere inside the ```AppModule```.

```typescript

import { Injectable } from '@angular/core';
import { Socket } from 'ngx-socket-io';

@Injectable()
export class ChatService {

    constructor(private socket: Socket) { }

    sendMessage(msg: string){
        this.socket.emit("message", msg);
    }
     getMessage() {
        return this.socket
            .fromEvent("message")
            .map( data => data.msg );
    }
}

```

### Using multiple sockets with different end points

In this case we do not configure the ```SocketIoModule``` directly using ```forRoot```. What we have to do is: extend the ```Socket``` service, and call ```super()``` with the ```SocketIoConfig``` object type (passing ```url``` & ```options``` if any).

```typescript

import { Injectable, NgModule } from '@angular/core';
import { Socket } from 'ngx-socket-io';

@Injectable()
export class SocketOne extends Socket {

    constructor() {
        super({ url: 'http://url_one:portOne', options: {} });
    }

}

@Injectable()
export class SocketTwo extends Socket {

    constructor() {
        super({ url: 'http://url_two:portTwo', options: {} });
    }

}

@NgModule({
  declarations: [
    //components
  ],
  imports: [
    SocketIoModule,
    //...
  ],
  providers: [SocketOne, SocketTwo],
  bootstrap: [/** AppComponent **/]
})
export class AppModule { }

```

Now you can inject ```SocketOne```, ```SocketTwo``` in any other services and / or components.


## API

Most of the functionalities here you are already familiar with.

The only addition is the ```fromEvent``` method, which returns an ```Observable``` that you can subscribe to.

### `socket.on(eventName: string)`

Takes an event name and callback.
Works the same as in Socket.IO.

### `socket.removeListener(eventName: string, callback: Function)`

Takes an event name and callback.
Works the same as in Socket.IO.

### `socket.removeAllListeners(eventName: string)`

Takes an event name.
Works the same as in Socket.IO.

### `socket.emit(eventName:string, message?: any, [callback: Function])`

Sends a message to the server.
Optionally takes a callback.
Works the same as in Socket.IO.

### `socket.fromEvent<T>(eventName: string): Observable<T>`

Takes an event name and returns an Observable that you can subscribe to.

### `socket.fromEventOnce<T>(eventName: string): Promise<T>`

Creates a Promise for a one-time event.

You should keep a reference to the Observable subscription and unsubscribe when you're done with it.
This prevents memory leaks as the event listener attached will be removed (using ```socket.removeListener```) ONLY and when/if you unsubscribe.

If you have multiple subscriptions to an Observable only the last unsubscription will remove the listener.


## LICENSE

MIT

Related projects
----------------

- [bougarfaoui/ng-socket-io](https://github.com/bougarfaoui/ng-socket-io) - Socket.IO module for Angular