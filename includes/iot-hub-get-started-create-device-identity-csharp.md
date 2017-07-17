## Skapa en enhetsidentitet
<a id="create-a-device-identity" class="xliff"></a>
I det här avsnittet ska du skapa en .NET-konsolapp som skapar en enhetsidentitet i identitetsregistret i IoT Hub. En enhet kan inte ansluta till IoT Hub om den inte har en post i identitetsregistret. Mer information finns i avsnittet om identitetsregistret i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. När du kör den här konsolappen genererar det ett unikt enhets-ID och en nyckel som din enhet kan använda för att identifiera sig själv när den skickar ”enhet-till-molnet”-meddelanden till IoT Hub. Enhets-ID är skiftlägeskänsliga.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den nya lösningen med hjälp av projektmallen **Konsolapp (.NET Framework)**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Ge projektet namnet **CreateDeviceIdentity** och lösningen namnet **IoTHubGetStarted**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt][10]
2. Högerklicka i Solution Explorer på projektet **CreateDeviceIdentity** och klicka sedan på **Hantera NuGet-paket**.
3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.
   
    ![Fönstret för NuGet-pakethanteraren][11]
4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;
5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i föregående avsnitt.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. Lägg till följande metod i klassen **Program**:
   
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
   
    Den här metoden skapar en enhetsidentitet med ID:t **myFirstDevice**. (Om enhets-ID:t redan finns i identitetsregistret hämtar koden bara den befintliga enhetsinformationen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade enhetsappen för att ansluta till din IoT Hub.
7. Slutligen lägger du till följande rader till **Main**-metoden:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();
8. Kör det här programmet och notera enhetsnyckeln.
   
    ![Enhetsnyckel som genereras av programmet][12]

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhetsidentiteter för att skydda åtkomsten till IoT Hub. Registret lagrar enhets-ID:n och enhetsnycklar som ska användas som säkerhetsreferenser och en aktiverad/inaktiverad-flagga som du kan använda för att inaktivera åtkomst för en enskild enhet. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].
> 
> 

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp3.png


<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
