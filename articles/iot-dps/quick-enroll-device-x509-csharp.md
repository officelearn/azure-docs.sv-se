---
title: 'Registrera X.509-enheten till Azure Device Provisioning Service med C #'
description: Den här snabbstarten använder gruppregistreringar. I den här snabbstarten registrerar du X.509-enheter till AZURE IoT Hub Device Provisioning Service (DPS) med C#.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 64bc3921a606ab3211173b46b268ded53952c8bb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75434666"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Snabbstart: Registrera X.509-enheter till Device Provisioning Service med hjälp av C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Den här snabbstarten beskriver hur du använder C# för att programmässigt skapa en [registreringsgrupp](concepts-service.md#enrollment-group) som använder mellanliggande CA X.509-certifikat eller CA X.509-rotcertifikat. Registreringsgruppen skapas med hjälp av [Microsoft Azure IoT SDK för .NET](https://github.com/Azure/azure-iot-sdk-csharp) och ett exempel på C# .NET Core-program. En registreringsgrupp kontrollerar åtkomst till etableringstjänsten för enheter som delar ett gemensamt signeringscertifikat i certifikatkedjan. Läs mer i informationen om att [kontrollera enhetsåtkomst till etableringstjänsten med X.509-certifikat](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Mer information om att använda X.509-certifikatbaserad Public Key Infrastructure (PKI) med Azure IoT Hub och enhetsetableringstjänst finns i [Översikt över certifikatsäkerhet med X.509 CA](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Den här snabbstarten förväntar sig att du redan har skapat en IoT-hubb och enhetsetableringstjänstinstans. Om du inte redan har skapat dessa resurser slutför du [etableringstjänsten för IoT Hub Device Provisioning med snabbstarten för Azure-portalen](./quick-setup-auto-provision.md) innan du fortsätter med den här artikeln.

Även om stegen i den här artikeln fungerar på både Windows- och Linux-datorer använder den här artikeln en Windows-utvecklingsdator.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Installera [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Installera [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Förbereda testcertifikat

I den här snabbstarten måste du ha en .pem- eller en .cer-fil som innehåller den offentliga delen av ett mellanliggande X.509-certifikat eller ett X.509-rotcertifikat. Det här certifikatet måste laddas upp till din etableringstjänst och verifieras av tjänsten.

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) innehåller testverktyg som kan hjälpa dig att skapa en X.509-certifikatkedja, ladda upp en rot eller ett mellanliggande certifikat från den kedjan och göra bevis på innehav med tjänsten för att verifiera certifikatet.

> [!CAUTION]
> Använd certifikat som skapats med SDK-verktyg för utvecklingstestning.
> Använd inte dessa certifikat i produktion.
> De innehåller hårdkodade lösenord, till exempel *1234*, som löper ut efter 30 dagar.
> Om du vill lära dig mer om att hämta certifikat som är lämpliga för produktion kan du läsa [How to get an X.509 CA certificate](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) (Hämta ett X.509 CA-certifikat) i dokumentationen för Azure IoT Hub.
>

Så här använder du det här testverktyget för att generera certifikat:

1. Hitta taggnamnet för den [senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av Azure IoT C SDK.

2. Öppna en kommandotolk eller Git Bash-gränssnittet och växla till en arbetsmapp på datorn. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-databasen. Använd taggen som du hittade i föregående `-b` steg som värde för parametern:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

   Testverktyget finns i den *azure-iot-sdk-c/tools/CACertificates* för den lagringsplats som du klonade.

3. Följ stegen på sidan om att [hantera CA-testcertifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Förutom verktygen i C SDK visar [verifieringsprovet för gruppcertifikat](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) i *Microsoft Azure IoT SDK för .NET* hur du gör bevis på innehav i C# med ett befintligt X.509-mellan- eller rotcertifikatutfärdarcertifikat.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Hämta anslutningssträngen för etableringstjänsten

För exemplet i den här snabbstarten behöver du anslutningssträngen för etableringstjänsten.

1. Logga in på Azure-portalen, välj **Alla resurser**och sedan enhetens etableringstjänst.

1. Välj **Principer för delad åtkomst**och välj sedan den åtkomstprincip som du vill använda för att öppna dess egenskaper. Kopiera och spara anslutningssträngen för primärnyckel i **Åtkomstprincip.**

    ![Hämta etableringsanslutningssträng från portalen](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Skapa exemplet för registrering av grupp 

I det här avsnittet visas hur du skapar en .NET Core-konsolapp som lägger till en registreringsgrupp i etableringstjänsten. Med vissa ändringar kan du även följa de här stegen för att skapa en [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konsolapp för att lägga till registreringsgruppen. Mer information om hur du utvecklar med IoT Core i finns i [Windows IoT Core-dokumentationen för utvecklare](https://docs.microsoft.com/windows/iot-core/).

1. Öppna Visual Studio och välj **Skapa ett nytt projekt**. I **Skapa ett nytt projekt**väljer du **konsolappen (.NET Core)** för C#-projektmallen och väljer **Nästa**.

1. Ge projektet namnet *CreateEnrollmentGroup*och tryck sedan på **Skapa**.

    ![Konfigurera Visual C# Windows Classic Desktop-projekt](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. När lösningen öppnas i Visual Studio högerklickar du på projektet **CreateEnrollmentGroup** i fönstret **Solution Explorer** och väljer sedan **Hantera NuGet-paket**.

1. I **NuGet Package Manager**väljer du **Bläddra,** söker efter och väljer **Microsoft.Azure.Devices.Provisioning.Service**och trycker sedan på **Installera**.

    ![Fönstret för NuGet-pakethanteraren](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Det här steget hämtar, installerar och lägger till en referens till [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet-paketet och dess beroenden.

1. Lägg till `using` följande satser efter de andra `using` uttalandena högst upp `Program.cs`i:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Lägg till följande `Program` fält i klassen och gör de angivna ändringarna.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Ersätt `ProvisioningServiceConnectionString` platshållarvärdet med anslutningssträngen för den etableringstjänst som du vill skapa registreringen för.

   * Ersätt `X509RootCertPath` platshållarvärdet med sökvägen till en .pem- eller .cer-fil. Den här filen representerar den offentliga delen av ett mellanliggande certifikat eller ett rotcertifikat för certifikatutfärdare X.509 som tidigare har överförts och verifierats med etableringstjänsten.

   * Du kan eventuellt `EnrollmentGroupId` ändra värdet. Strängen får bara innehålla gemener och bindestreck.

   > [!IMPORTANT]
   > I produktionskoden ska du vara medveten om följande säkerhetsöverväganden:
   >
   > * Hårdkodning av anslutningssträngen för etableringstjänstadministratören går emot bästa säkerhetsmetoder. Istället ska anslutningssträngen lagras på ett säkert sätt, som i en säker konfigurationsfil eller i registret.
   > * Glöm inte att överföra den offentliga delen av signeringscertifikatet. Ladda aldrig upp .pfx- (PKCS12) eller .pem-filer som innehåller privata nycklar till etableringstjänsten.

1. Lägg till följande `Program` metod i klassen. Den här koden skapar en registreringsgrupppost och anropar sedan `CreateOrUpdateEnrollmentGroupAsync` metoden `ProvisioningServiceClient` för att lägga till registreringsgruppen i etableringstjänsten.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
           #endregion
 
       }
   }
   ```

1. Slutligen, ersätta kroppen `Main` av metoden med följande rader:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Skapa lösningen.

## <a name="run-the-enrollment-group-sample"></a>Köra exemplet för registrering av grupp
  
Kör exemplet i Visual Studio för att skapa registreringsgruppen. Ett kommandotolksfönster visas och visar bekräftelsemeddelanden. När kommandotolksfönstret har skapats visas egenskaperna för den nya registreringsgruppen.

Du kan kontrollera att registreringsgruppen har skapats. Gå till sammanfattningen av enhetsetableringstjänsten och välj **Hantera registreringar**och välj sedan **Registreringsgrupper**. Du bör se en ny registreringspost som motsvarar det registrerings-ID som du använde i exemplet.

![Egenskaper för registrering i portalen](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Markera den post som ska verifiera certifikatets tumavtryck och andra egenskaper för transaktionen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska c#-tjänstexemplet ska du inte rensa de resurser som skapas i den här snabbstarten. Annars använder du följande steg för att ta bort alla resurser som skapats av den här snabbstarten.

1. Stäng utdatafönstret för C#på datorn.

1. Navigera till enhetens etableringstjänst i Azure-portalen, välj **Hantera registreringar**och välj sedan **registreringsgrupper**. Välj *registrerings-ID* för registreringsposten som du skapade med den här snabbstarten och tryck på **Ta bort**.

1. Välj **Certifikat**från tjänsten Enhetsetablering i Azure-portalen , välj det certifikat som du har laddat upp för den här snabbstarten och tryck på **Ta bort** högst upp på **Certifikatinformation**.  

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en registreringsgrupp för ett X.509 mellanliggande certifikat eller rotcertifikatutfärdare med hjälp av Azure IoT Hub Device Provisioning Service. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen.

> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)
