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
                    //a secret key to validate connection,
                    // incoming connection without this key will be rejected,
                    opt.query = "SecretKey=xxxxx";
                    socket = IO.socket("http://socketer.gotdns.ch:300",opt);
                }catch(URISyntaxException e){
                    throw new RuntimeException(e);
                }
            }

connect to server, once the user open app , so call this function into OnCreate of main activity

response is event : "connected" with data = "true"

    socket.connect();
    socket.on("error",handleRejected); //handle rejected connection <<
    socket.on("connected",handleConnected);
    
    //listener function for connected event  (this function copied from internet , so not sure 100% if there is no mistakes
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
        
 
 
 to send data to server side
"request" is the event name , Parms is the json object

      JSONObject Parms = new JSONObject();
       JSONObject Values = new JSONObject();
       Values.put("email","bishoy.eagle@yahoo.com");
       Values.put("password","myPWD");
       object.put("sign_up", Values);
      socket.emit("request",Parms);
      
you can send string as json object with one line , for example :

     socket.emit("request","{ \"sign_in\" : {\"email\" : \"beshoy@gmail.com\" : , \"password\" :\"unknownPW\"}}");
     
to receive data from server side
"response" is the event name , responsed data is json array or string , depending on the results

    socket.on("response",handleResponse); //call a function to handle responsed data and use it in app
      

****************************************************************************************************
# Docs

for sign up / registration

>> request

     { "sign_up": { "email" : "bishoy.eagle@yahoo.com" , "password": "broken" }}
     
>> response in json objects

if there is a error like invalid email / invalid password or any kind of errors

    {sign_up : {error : ErrorMsg}} // ErrorMsg is the responsed error message
    
sign up success and inserted in database , response is

    {sign_up: {success: { cid: CustomerID , PhoneState : 0 , EmailState : 0}}}
    
  CustomerID : is the customer id, PhoneState : is the state of phone confirmation , EmailState is the email confirmation
  
once the user sign up successfully, a confirmation code/msg will send to his email and code will expire in 15 minutes

****************************************************************************************************
for sign in / login

>> request

     { "sign_in": { "email" : "bishoy.eagle@yahoo.com" , "password": "broken" }}
     
>> response in json objects

if there is a error like incorrect email , password or any kind of errors

    {sign_in : {error : ErrorMsg}} // ErrorMsg is the responsed error message
    
sign in success and new inserted in database , response is

    {sign_in: {success: { cid: CustomerID , PhoneState : State , EmailState : State}}}
    
  CustomerID : is the customer id, PhoneState : is the state of phone confirmation , EmailState is the email confirmation
  
  State : if 0 = not confirmed else 1 = confirmed
  
 
****************************************************************************************************

to resend confirmation code to phone number or email address

     { "resend_confirmation": { "type" : "email" , "cid": "25" }}
type is : "phone" or "email" to resend confirmation code , and code will expire in 15 minutes

cid >> is the Customer ID , if the user registered or logged in so response data will include cid , so save it in app

there is a server side check on cid , so you have to use returned cid

error response

     {resend_confirmation: {error: ErrorMsg}}

success response

    {resend_confirmation: {success: { type: RequestType}}} // RequestType "phone" or "email"

****************************************************************************************************
to confirm email address

    { "confirm_email": {"code": "ConfirmationCode" ,"cid": "25" }}
cid : is the cusomter id which returned by sign up or sign in success

ConfirmationCode : is the written code by USER


error response

     {confirm_email: {error: ErrorMsg}}

success response

    {confirm_email: {success: "confirmed"}} 

 ****************************************************************************************************
to confirm phone number

    { "confirm_phone": {"code": "ConfirmationCode" ,"cid": "25" }}
cid : is the cusomter id which returned by sign up or sign in success

ConfirmationCode : is the written code by USER


error response

     {confirm_phone: {error: ErrorMsg}}

success response

    {confirm_phone: {success: "confirmed"}} 

        
