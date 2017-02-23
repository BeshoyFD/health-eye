# health-eye
documentation for health eye backend server


 Socket.io API For Android

  imports :

    import com.github.nkzawa.emitter.Emitter;
    import com.github.nkzawa.socketio.client.IO;
    import com.github.nkzawa.socketio.client.Socket;
 
      compile in : build.gradle
         compile "com.github.nkzawa:socket.io-client:0.5.0"


         socket function for connection
         private Socket socket;
            {
                try{
                    IO.Options opt = new IO.Options();
                    opt.query = "SecretKey=xxxxx";
                    socket = IO.socket("http://socketer.gotdns.ch:300",opt);
                }catch(URISyntaxException e){
                    throw new RuntimeException(e);
                }
            }

connect to server, once the user open app , so call this function into OnCreate of main activity

response is event : "connected" with data = "true"

    socket.connect();
    socket.on("connected",handleConnected);
    
    //listener function for connected event  (this function copied from internet , so not sure 100% if it without mistakes
    private Emitter.Listener handleConnected = new Emitter.Listener(){
     @Override
        public void call(Object... args) {
            JSONArray obj = (JSONArray) args[0];
              String data = obj.toString();
            runOnUiThread(new Runnable() {
                public void run() {
                if(data = "true"){
                   // connected successfully , user can sing up / sing in
                   socket.emit("ping","true"); //a ping to keep user active/online on server for chat and other stuffs
                  }else{
                  // failed to connect
                  }
                }
            });
        }
    };
    
call socket disconnect into OnDestroy function or app exit (socket.disconnect() must be called to close connection on server side)

    public void onDestroy() {
            super.onDestroy();
            socket.disconnect();
        }
        
create a thread in background of app to send ping to server side every 30 seconds

        socket.emit("ping","true"); //a ping to keep user active/online on server for chat and other stuffs
        
 
 
main event to send data to server side
"request" is the event name , pars is the json array

    pars = "type":[ {  parameters array } ]
      socket.emit("request",pars);
      
main event to receive data from server side
"response" is the event name , responsed data is json array or string , depending on the results

    socket.on("response",handleResponse); //call a function to handle responsed data and use it in app
      

****************************************************************************************************
# Docs




  
        
