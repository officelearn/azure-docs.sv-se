---
title: 'Etablera simulerad X.509-enhet till Azure IoT Hub med C #'
description: Snabbstart – Skapa och etablera en simulerad X.509-enhet med C#-enheten SDK för Azure IoT Hub Device Provisioning Service (DPS). Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 639ab07113a0e62cac43af5b79f052da1efd93ab
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976527"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Snabbstart: Skapa och etablera en simulerad X.509-enhet med C#-enheten SDK för IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

De här stegen visar hur du använder [Azure IoT-exempel för C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) för att simulera en X.509-enhet på en utvecklingsdator som kör Windows operativsystem. Exemplet ansluter även den simulerade enhet till en IoT Hub med hjälp av enhetsetableringstjänsten.

Om du inte känner till processen för automatisk etablering bör du också granska [begrepp för automatisk etablering](concepts-auto-provisioning.md). Se också till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal) innan du fortsätter. 

Azure IoT Device Provisioning Service stöder två typer av registreringar:
- [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
- [Enskilda registreringar](concepts-service.md#individual-enrollment): används för att registrera en enskild enhet.

Den här artikeln visar enskilda registreringar.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön 

1. Kontrollera att du har [.NET Core 2.1 SDK eller senare](https://www.microsoft.com/net/download/windows) installerat på datorn. 

1. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

1. Öppna en kommandotolk eller Git Bash. Klona Azure IoT-exempel för C# GitHub-lagringsplatsen:
    
    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Skapa ett självsignerat X.509-enhetscertifikat och en post för enskild registrering

I det här avsnittet använder du ett självsignerat X.509-certifikat. Det är viktigt att tänka på följande:

* Självsignerade certifikat är endast till för testning och ska inte användas i produktion.
* Standardutgångsdatumet för ett självsignerat certifikat är ett år.

Du kommer att använda exempelkoden från [Etablera enhetsklientexempel – X.509-attestering](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) till att skapa det certifikat som ska användas med posten för enskild registrering för den simulerade enheten.


1. I en kommandotolk ändrar du kataloger till projektkatalogen för X.509-enhetsetableringsexemplet.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Exempelkoden konfigureras att använda X.509-certifikat som lagras i en lösenordsskyddad PKCS12-formaterad fil (certificate.pfx). Dessutom behöver du en certifikatfil för offentlig nyckel (certificate.cer) för att skapa en enskild registrering senare i den här snabbstarten. Generera ett självsignerat certifikat och dess associerade .cer- och .pfx-filer genom att köra följande kommando:

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

3. Med skriptet uppmanas du att ange ett PFX-lösenord. Kom ihåg lösenordet. Du måste använda det när du kör exemplet.

    ![ Ange PFX-lösenordet](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Logga in på Azure-portalen, välj knappen **Alla resurser** på menyn till vänster och öppna etableringstjänsten.

5. Välj **Hantera registreringar**på menyn Tjänst för enhetsetablering . Välj fliken **Enskilda registreringar** och välj knappen **Lägg till individuell registrering** högst upp. 

6. Ange följande information på panelen **Lägg till registrering:**
   - Välj **X.509** som identitet för bestyrkande *mekanism*.
   - Under filen *Primär certifikat .pem eller .cer*väljer du *Välj en fil* för att välja certifikatfilen **certificate.cer** som skapats i föregående steg.
   - Lämna **Enhets-ID** tomt. Enheten etableras med dess enhet-ID inställt på nätverksnamnet (CN) i X.509-certifikatet **iothubx509device1**. Det här är även namnet som används som registrerings-ID för posten för enskild registrering. 
   - Du kan även ange följande information:
       - Välj en IoT hub som är länkad till din etableringstjänst.
       - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
   - När du är klar trycker du på **knappen Spara.** 

     [![Lägga till individuell registrering för X.509-intyg i portalen](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Vid lyckad registrering visas X.509-registreringsposten som **iothubx509device1** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar*. 

## <a name="provision-the-simulated-device"></a>Etablera den simulerade enheten

1. Observera **_id-scope-värdet_** från **bladet Översikt** för etableringstjänsten.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Skriv följande kommando för att skapa och köra X.509-enhetsetableringsexemplet. Ersätt `<IDScope>`-värdet med ID-omfånget för etableringstjänsten. 

    ```cmd
    dotnet run <IDScope>
    ```

3. Vid uppmaning anger du lösenordet för PFX-filen du skapade tidigare. Lägg märke till de meddelanden som simulerar enhetsstart och anslutning till Device Provisioning-tjänsten för att hämta IoT-hubinformationen. 

    ![Utdata för exempelenhet](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

4. Kontrollera att enheten har etablerats. När den simulerade enheten har etablerats till IoT-hubben som är länkad till etableringstjänsten visas enhets-ID:et på hubbens **IoT-enhetsblad.** 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Förstå och använda enhetstvillingar i IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som skapas i den här snabbstarten. Om du inte planerar att fortsätta använder du följande steg för att ta bort alla resurser som skapats av den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Stäng fönstret för TPM-simulatorn på datorn.
1. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din enhetsetableringstjänst. Tryck på **Ta bort** högst upp i fönstret högst upp i **översiktsbladet.**  
1. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din IoT-hubb. Tryck på **Ta bort** högst upp i fönstret högst upp i **översiktsbladet.**  

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en simulerad X.509-enhet på din Windows-dator och etablerat den i din IoT-hubb med hjälp av Azure IoT Hub Device Provisioning Service på portalen. Om du vill veta hur du registrerar din X.509-enhet programmässigt fortsätter du till snabbstarten för programmatisk registrering av X.509-enheter. 

> [!div class="nextstepaction"]
> [Snabbstart i Azure – Registrera X.509-enheter till Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-csharp.md)
