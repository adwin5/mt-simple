# mt-simple
simple MT API to use TI-2530 Soc through Monitor-and-Test command

##Test File 
####/test has 4 demo files
*  demoNV.js        
*  demoTempature.js    
*  demotime.js 
*  demoTimer.js

####/test-web has 1 demo file
*  demowebserver

##To Use
  ```js
var MT = require("index");
  ```
  
##Init
  ```js
  MT.init("/dev/ttyUSB0", function(){
      start();
    });
  ```
1. Portname needs to be specified  
2. If you don't know your portname, please check out your portname through the "dmesg" command
3. Callback function will be called after init is done
4. In this case, start function will be called after init is done

##Write Code Here
  ```js
  function start(){
    ...code...
  }
  ```
1. Write your code in a function that is called after init
2. It makes sure that your code is executed after port and parser is ready

##Method Example 2: SYS_TIME_SET
  ```js
    MT.setCurrentTime(function(err, data){
       console.log("***User: set current time Successfully");
    });
  ```
1.  setCurrentTime is one of the mt-simple API function which sets the time/clock of TI-cc2530 to current time
2.  The time in TI-cc2530 is not current time if your just power on and read the time.

##Method Example 2: SYS_NV_WRITE
  ```js
  var commandSysNVWrite = { id : 0x0F00, offset : 0x00, len : 0x01, value : [0x55] };
   MT.SysNVWrite(commandSysNVWrite, function(err, data){
     console.log("***User: write NV Successfully");
     console.log(data);
   });
  ```
1.  SysNVWrite is one of the mt-simple API function which writes to none volatial memory of TI-cc2530
2.  User should create an object to specifies the attribute
3.  the console.log(...) will be called when the parser receive response from TI-cc2530

##Indication Dealer Example : 
  ```js
  //set timer 0 ,it will expire in 2 secs//
  MT.startTimer({id:0, timeout:2000}, function(err, data){});
    
  MT.hub.on('messageIND',function(data){
    //do someting ... when the timer expired ... aka ... the indication comes//
    //get tempature//
    MT.getTempature(function(err, data){
      console.log("***User: the current tempature is:");
      console.log(data.AdcRead.toString(16));
      });
  });
  ```
1. Indication means an response from Soc and it is not synchronous
2. Use hub.on to listen to the indication
2. In this case, when the timer expires, then execute the callback function in hub.on('messsageIND', callback(){})
3. We show an example to get the value of tempature when the timer expires a.k.a after 2000 millisecond

##Notrecognized Command Dealer Example : 
  ```js
  MT.hub.on('notRecogMessage',function(data){
     console.log(data);
  });
  ```
1. If the ID of indication/synchronous response is not regonized by parser, then execute the callback function in hub.on('notRecogMessage', callback(){})

