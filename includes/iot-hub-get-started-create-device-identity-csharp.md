## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet

I det här avsnittet ska du skapa en .NET-konsolapp som skapar en enhetsidentitet i identitetsregistret i IoT Hub. En enhet kan inte ansluta till IoT Hub om den inte har en post i identitetsregistret. Mer information finns i avsnittet om identitetsregistret i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. När du kör den här konsolappen genereras ett unikt enhets-ID och en unik nyckel. Enheten använder dessa värden till att identifiera sig själv när den skickar meddelanden mellan enhet och moln till IoT Hub. Enhets-ID:n är skiftlägeskänsliga.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den nya lösningen med hjälp av projektmallen **Konsolapp (.NET Framework)**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Ge projektet namnet **CreateDeviceIdentity** och lösningen namnet **IoTHubGetStarted**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][10]

1. Högerklicka i Solution Explorer på projektet **CreateDeviceIdentity** och klicka sedan på **Hantera NuGet-paket**.

1. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren][11]

1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i föregående avsnitt.

    ```csharp
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Lägg till följande metod i klassen **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
      string deviceId = "myFirstDevice";
      Device device;
      try
      {
          device = await registryManager.AddDeviceAsync(new Device(deviceId));
      }
      catch (DeviceAlreadyExistsException)
      {
          device = await registryManager.GetDeviceAsync(deviceId);
      }
      Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Den här metoden skapar en enhetsidentitet med ID:t **myFirstDevice**. (Om enhets-ID:t redan finns i identitetsregistret hämtar koden bara den befintliga enhetsinformationen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade enhetsappen för att ansluta till din IoT Hub.
[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddDeviceAsync().Wait();
    Console.ReadLine();
    ```

1. Kör det här programmet och notera enhetsnyckeln.

    ![Enhetsnyckel som genereras av programmet][12]

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp3.png

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
