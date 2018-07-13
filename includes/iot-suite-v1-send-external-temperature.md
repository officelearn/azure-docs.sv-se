## <a name="configure-the-nodejs-simulated-device"></a>Konfigurera den simulerade enheten för Node.js
1. Klicka på instrumentpanelen för fjärrövervakning, **+ Lägg till en enhet** och Lägg sedan till en *anpassad enhet*. Anteckna IoT Hub-värdnamnet, enhets-id och enhetsnyckel. Du behöver dem senare i den här självstudien när du förbereder remote_monitoring.js-tjänstens klientprogram.
2. Se till att Node.js version 0.12.x eller senare är installerat på utvecklingsdatorn. Kör `node --version` i en kommandotolk eller i ett shell för att kontrollera versionen. Information om hur du använder en pakethanterare för att installera Node.js på Linux finns i [installera Node.js via Pakethanteraren][node-linux].
3. När du har installerat Node.js, klona den senaste versionen av den [azure-iot-sdk-nod] [ lnk-github-repo] lagringsplatsen till din utvecklingsdator. Använd alltid den **master** grenen för den senaste versionen av de bibliotek och exempel.
4. Från den lokala kopian av den [azure-iot-sdk-nod] [ lnk-github-repo] lagringsplatsen, kopiera följande två filer från mappen samples/enhet/nod till en tom mapp på utvecklingsdatorn:
   
   * Packages.JSON
   * remote_monitoring.js
5. Öppna filen remote_monitoring.js och leta efter följande variabeldefinitionen:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Ersätt **[anslutningssträngen för IoT Hub device]** med enhetens anslutningssträng. Använd värdena för din IoT Hub-värdnamnet, enhets-id och enhetsnyckeln som du antecknade i steg 1. En anslutningssträng för enheten har följande format:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Om din IoT Hub-värdnamnet är **contoso** och din enhets-id är **mydevice**, anslutningssträngen ser ut som följande kodavsnitt:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Spara filen. Kör följande kommandon i en shell eller Kommandotolken i mappen som innehåller filerna för att installera de nödvändiga paketen och kör exempelprogrammet:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Notera dynamisk telemetri i praktiken
Instrumentpanelen visar temperatur och fuktighet telemetri från befintliga simulerade enheter:

![Standardinstrumentpanelen][image1]

Om du väljer den simulerade enheten Node.js som du körde i föregående avsnitt kan se du temperatur, fuktighet och extern temperatur telemetri:

![Lägga till extern temperatur på instrumentpanelen][image2]

Lösningen för fjärrövervakning automatiskt identifierar telemetrityp ytterligare extern temperatur och lägger till den i diagrammet på instrumentpanelen.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png