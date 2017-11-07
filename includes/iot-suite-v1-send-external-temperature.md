## <a name="configure-the-nodejs-simulated-device"></a>Konfigurera Node.js simulerade enheten
1. Klicka på fjärranslutna instrumentpanelen för övervakning, **+ Lägg till en enhet** och Lägg sedan till en *anpassade*. Anteckna IoT-hubben värdnamn, enhets-id och nyckeln för enheten. Behöver du dem senare i den här självstudiekursen när du förbereder klientprogrammet remote_monitoring.js enhet.
2. Se till att Node.js-version 0.12.x eller senare är installerat på utvecklingsdatorn. Kör `node --version` vid en kommandotolk eller i ett gränssnitt för att kontrollera versionen. Information om hur du använder en Pakethanteraren installera Node.js på Linux finns [installera Node.js via Pakethanteraren][node-linux].
3. När du har installerat Node.js klona den senaste versionen av den [azure iot-sdk-nod] [ lnk-github-repo] databasen på utvecklingsdatorn. Använd alltid den **master** grenen för den senaste versionen av bibliotek och exempel.
4. Från den lokala kopian av den [azure iot-sdk-nod] [ lnk-github-repo] databasen, kopiera följande två filer från mappen nod-enhet-exempel till en tom mapp på utvecklingsdatorn:
   
   * Packages.JSON
   * remote_monitoring.js
5. Öppna filen remote_monitoring.js och leta efter följande variabeldefinitionen:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Ersätt **[anslutningssträngen för IoT-hubb enheten]** med anslutningssträngen enhet. Använd värdena för din IoT-hubb värdnamn, enhets-id och nyckeln för enheten som du antecknade i steg 1. En anslutningssträng för enheten har följande format:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Om din IoT Hub-värdnamnet är **contoso** och enhets-id är **mydevice**, anslutningssträngen ser ut som följande utdrag:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Spara filen. Kör följande kommandon i ett gränssnitt eller en kommandotolk i den mapp som innehåller filerna för att installera de nödvändiga paketen och kör exempelprogrammet:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Se dynamiska telemetri i praktiken
Instrumentpanelen visar temperatur- och fuktighetskonsekvens telemetri från befintliga simulerade enheter:

![Standardinstrumentpanelen][image1]

Om du väljer den simulerade enheten Node.js som du körde i föregående avsnitt visas temperatur, fuktighet och externa temperatur telemetri:

![Lägg till externa temperatur på instrumentpanelen][image2]

Remote övervakningslösning automatiskt identifierar typen ytterligare externa temperatur telemetri och lägger till den diagram på instrumentpanelen.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png