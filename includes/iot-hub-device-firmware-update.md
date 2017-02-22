## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet får du:

* Skapa en Node.js-konsolapp som svarar på en direkt metod som anropas via molnet
* Utlösa en simulerad uppdatering av inbyggd programvara
* Använda rapporterade egenskaper för att aktivera enhetstvillingfrågor för att identifiera enheter och ta reda på när de senast slutfört en uppdatering av en inbyggd programvara

1. Skapa en tom mapp med namnet **manageddevice**.  I mappen **manageddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. I Kommandotolken i mappen **manageddevice** kör du följande kommando för att installera SDK-paketeten **azure-iot-device** och **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Skapa en **dmpatterns_fwupdate_device.js**-fil i mappen **manageddevice** med hjälp av ett textredigeringsprogram.

4. Lägg till följande ”require”-instruktioner i början av **dmpatterns_fwupdate_device.js**-filen:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**. Ersätt platshållaren `{yourdeviceconnectionstring}` med den anslutningssträng som du noterade tidigare i avsnittet Skapa en enhetsidentifitet:
   
    ```
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Lägg till följande funktion som används för att uppdatera rapporterade egenskaper:
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported: ' + firmwareUpdateValue.status);
      });
    };
    ```
7. Lägg till följande funktioner som simulerar nedladdning och användning av en avbildning för inbyggd programvara:
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. Lägg till följande funktion som uppdaterar uppdateringstatusen för den inbyggda programvaran via rapporterade egenskaper till **waiting** (väntande). Normalt informeras enheter när en uppdatering är tillgänglig och när en princip som en administaratör har definierat börjar ladda ned och tillämpa uppdateringen. Logiken för att aktivera principen ska köras i den här funktionen. För enkelhetens skull visas det här exemplet i fyra sekunder och sedan påbörjas nedladdningen av avbildningen för den inbyggda programvaran:
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. Lägg till följande funktion som uppdaterar uppdateringsstatusen för den inbyggda programvaran via rapporterade egenskaper till **downloading** (laddar ned). Funktionen simulerar sedan en nedladdning av den inbyggda programvaran och uppdaterar slutligen uppdateringsstatusen för den inbyggda programvaran till antingen **downloadFailed** (nedladdningen misslyckades) eller **downloadComplete** (nedladdningen har slutförts):
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. Lägg till följande funktion som uppdaterar uppdateringsstatusen för den inbyggda programvaran via rapporterade egenskaper till **applying** (tillämpas). Funktionen simulerar sedan tillämpning av avbildningen för den inbyggda programvaran och uppdaterar slutligen uppdateringsstatusen för den inbyggda programvaran till antingen **applyFailed** (tillämpningen misslyckades) eller **applyComplete** (tillämpningen har slutförts):
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. Lägg till följande funktion som hanterar den direkta **firmwareUpdate**-metoden och initierar uppdateringsprocessen för den inbyggda programvaran som sker i flera steg:
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = request.payload.fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. Slutligen lägger du till följande kod som ansluter till IoT-hubben:
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate);
    });
    ```

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff) vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].
> 
> 

<!--HONumber=Feb17_HO1-->


