## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet

I det här avsnittet kan du använda verktyget Node.js [iothub explorer] [ iot-hub-explorer] att skapa en enhetsidentitet för den här kursen. Enhets-ID är skiftlägeskänsliga.

1. Kör du följande i Kommandotolken miljön:

    `npm install -g iothub-explorer@latest`

1. Kör sedan följande kommando för att logga in på din hubb. Ersätt `{iot hub connection string}` med IoT-hubb anslutningssträngen som du kopierade tidigare:

    `iothub-explorer login "{iot hub connection string}"`

1. Skapa en ny enhetsidentitet som kallas slutligen `myDeviceId` med kommandot:

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anteckna anslutningssträngen för enheten från resultatet. Den här enheten anslutningssträngen används av appen enheter för att ansluta till din IoT-hubb som en enhet.

![][img-identity]

Referera till [komma igång med IoT-hubb] [ lnk-getstarted] programmässigt skapa enheten identiteter.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
