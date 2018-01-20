---
title: Etablera en enhet med Azure IoT Hub enheten etablering Service (.NET) | Microsoft Docs
description: "Etablera enheten till en enda IoT-hubb med hjälp av Azure IoT Hub enheten etablering Service (.NET)"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 6919f962d853faa572ee7ad5d0cb9aeacd3bd2b6
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Registrera enheten till en IoT-hubb med hjälp av Azure IoT Hub etablering Service klienten (.NET)

Du lärt dig hur du ställer in en enhet för att ansluta till tjänsten Enhetsetableringen i föregående självstudierna. I kursen får du lära dig hur att använda tjänsten för att etablera enheten till en enda IoT-hubb med både  **_enskilda registrering_**  och  **_registrering grupper_**. I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter måste du konfigurera din enhet och dess *maskinvarusäkerhetsmodul* enligt beskrivningen i självstudierna [konfigurera en enhet för att etablera med hjälp av Azure IoT-hubb Device etablering Service](./tutorial-set-up-device.md).

* Visual Studio 2015 eller Visual Studio 2017

> [!NOTE]
> Visual Studio krävs inte. Installationen av [.NET](https://www.microsoft.com/net) räcker och utvecklare kan använda sina önskad redigerare på Windows- eller Linux.  

Den här självstudiekursen simulerar perioden under eller direkt efter den maskinvara som tillverkar processen när enhetsinformation läggs till tjänsten etablering. Den här koden körs vanligtvis på en dator eller en factory-enhet som kan köra .NET-kod och inte att lägga till enheter själva.


## <a name="enroll-the-device"></a>Registrera enheten

Det här steget måste du lägga till enhetens unik säkerhet artefakter till enheten Etableringstjänsten. Dessa artefakter säkerhet är följande:

- För TPM-baserade enheter:
    - Den *bekräftelsenyckel* som är unik för varje TPM-chip eller simuleringen. Läs den [förstå bekräftelsenyckel för TPM](https://technet.microsoft.com/library/cc770443.aspx) för mer information.
    - Den *registrerings-ID* som används för att unikt identifiera en enhet i namnområdet/omfattning. Detta kan eller kan inte vara samma som enhets-ID. ID: T är obligatorisk för varje enhet. Registrerings-ID kan härledas från TPM, till exempel en SHA-256-hash för TPM-bekräftelsenyckel för TPM-baserade enheter.

- För X.509-baserade enheter:
    - Den [X.509-certifikat som utfärdades för enheten](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx), i form av antingen en *.pem* eller en *.cer* fil. För enskilda registrering måste du använda den *lövcertifikatet* för X.509-systemet, medan för registrering av grupper du behöver använda den *rotcertifikat* eller motsvarande *undertecknare certifikatet*.
    - Den *registrerings-ID* som används för att unikt identifiera en enhet i namnområdet/omfattning. Detta kan eller kan inte vara samma som enhets-ID. ID: T är obligatorisk för varje enhet. Registrerings-ID är härledd från certifikatets nätverksnamn (CN) för X.509-baserade enheter. Mer information om kraven finns [enheten begrepp](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-device).

Det finns två sätt att registrera enheten till tjänsten etablering enhet:

- **Enskilda registreringar** representerar en post för en enda enhet som kan registrera med enheten Etableringstjänsten. Enskilda registreringar kan använda X.509-certifikat eller SAS-token (i TPM verkliga eller virtuella) som mekanismer för attestering. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer, eller för enheter som kan bara använda SAS-token via TPM som metod för attestering. Enskilda registreringar kanske önskade IoT-hubb enhets-ID angetts.

- **Registrering grupper** representerar en grupp av enheter som delar en mekanism för specifika attestering. Vi rekommenderar en grupp för registrering för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter alla kommer att samma klientorganisation. X.509 finns bara i registrering grupper och alla delar ett signerat certifikat i kedjan sina X.509-certifikat.

### <a name="enroll-the-device-using-individual-enrollments"></a>Registrera enheten med enskilda registreringar

1. Skapa ett Visual C#-konsolprogram-projekt i Visual Studio med hjälp av den **Konsolapp** projektmall. Namnge projektet **DeviceProvisioning**.
    
1. I Solution Explorer högerklickar du på den **DeviceProvisioning** projektet och klicka sedan på **hantera NuGet-paket...** .

1. I den **NuGet Package Manager** väljer **Bläddra** och Sök efter **microsoft.azure.devices.provisioning.service**. Välj posten och klicka på **installera** att installera den **Microsoft.Azure.Devices.Provisioning.Service** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhet som etablerar SDK-tjänsten](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet-paketet och dess beroenden.

1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med DP-anslutningssträngen som anges i föregående avsnitt.
   
    ```csharp
    static readonly string ServiceConnectionString = "{DPS connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Lägg till följande om du vill implementera registreringen för enheten:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Slutligen lägger du till följande kod i **Main** metod för att öppna anslutningen till din IoT-hubb och påbörja registreringen:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. Högerklicka på lösningen i Visual Studio Solution Explorer och klicka sedan på **ange Startprojekt...** . Välj **enda Startprojekt**, och välj sedan den **DeviceProvisioning** projekt i den nedrullningsbara menyn.  

1. Kör .NET enhetsapp **DeviceProvisiong**. Det bör ställa in etablering för enheten: 

    ![Enskilda registrering kör](./media/tutorial-net-provision-device-to-hub/individual.png)

När enheten har registrerats, bör du se den visas i portalen enligt följande:

   ![Lyckad registrering i portalen](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Registrera enheten med hjälp av grupper för registrering

> [!NOTE]
> Grupp-exempel registrering kräver ett X.509-certifikat.

1. Öppna i Visual Studio Solution Explorer i **DeviceProvisioning** projekt skapade ovan. 

1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med X509 certifikat plats.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Lägg till följande till **Program.cs** implementera registreringen för gruppen:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Till sist ersätter följande kod i **Main** metod för att öppna anslutningen till din IoT-hubb och börja registrera för gruppen:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Kör .NET enhetsapp **DeviceProvisiong**. Det bör ställa in gruppen etablering för enheten: 

    ![Gruppregistrering kör](./media/tutorial-net-provision-device-to-hub/group.png)

    När enhetsgrupp har registrerats, du bör se den i portalen enligt följande:

   ![Lyckad grupp registrering i portalen](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Starta enheten

Följande inställningar är nu klar för registrering av enheten:

1. Din enhet eller grupp av enheter registreras till din enhet etablering av tjänst, och 
2. Enheten är klar med säkerhet konfigurerats och är tillgänglig via det program som använder enheten Etableringstjänsten klient-SDK.

Starta enhet så att ditt klientprogram att starta registreringen med din enhet etablering av tjänst.  


## <a name="verify-the-device-is-registered"></a>Kontrollera att enheten är registrerad

När din enhet Starter följande åtgärder ska utföras. Se exempelprogrammet TPM simulator [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) för mer information. 

1. Enheten skickar en begäran om registrering till din enhet etableringstjänsten.
2. Etablering av tjänst skickar tillbaka en registrering utmaning som enheten svarar för TPM-enheter. 
3. På lyckad registrering skickar enheten Etableringstjänsten IoT-hubb URI, enhets-ID och den krypterade nyckeln tillbaka till enheten. 
4. IoT-hubb klientprogrammet på enheten sedan ansluter till din hubb. 
5. Lyckad anslutning till hubben, bör du se enheten visas i IoT-hubben **enheten Explorer**. 

    ![Anslutning till hubb i portalen](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

Gå vidare till nästa kurs att lära dig att etablera flera enheter över Utjämning av nätverksbelastning hubs. 

> [!div class="nextstepaction"]
> [Etablera enheter över Utjämning av nätverksbelastning IoT-hubbar](./tutorial-provision-multiple-hubs.md)
