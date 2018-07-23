## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet

I det här avsnittet ska du använda verktyget Node.js [iothub-explorer] [ iot-hub-explorer] att skapa en enhetsidentitet i den här självstudien. Enhets-ID är skiftlägeskänsliga.

1. Kör följande i miljön kommandoraden:

    `npm install -g iothub-explorer@latest`

1. Kör sedan följande kommando för att logga in till hubben. Ersätt `{iot hub connection string}` med IoT Hub-anslutningssträngen som du kopierade tidigare:

    `iothub-explorer login "{iot hub connection string}"`

1. Skapa en ny enhetsidentitet som kallas slutligen `myDeviceId` med kommandot:

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anteckna enhetsanslutningssträngen från resultatet. Den här enhetens anslutningssträng används av app för enheter för att ansluta till din IoT-hubb som en enhet.

![][img-identity]

Referera till [komma igång med IoT Hub] [ lnk-getstarted] du programmässigt skapar enhetsidentiteter.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/quickstart-send-telemetry-dotnet.md
