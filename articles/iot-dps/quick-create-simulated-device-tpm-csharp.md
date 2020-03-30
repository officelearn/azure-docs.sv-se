---
title: 'Snabbstart – etablera en simulerad TPM-enhet till Azure IoT Hub med C #'
description: Snabbstart – Skapa och etablera en simulerad TPM-enhet med C#-enheten SDK för Azure IoT Hub Device Provisioning Service (DPS). Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 82bd284ede23e8880f79c614f4a6e2f588a4293c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74977003"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Snabbstart: Skapa och etablera en simulerad TPM-enhet med C#-enheten SDK för IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

De här stegen visar hur du använder [Azure IoT-exempel för C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) för att simulera en TPM-enhet på en utvecklingsdator som kör Windows operativsystem. Exemplet ansluter även den simulerade enhet till en IoT Hub med hjälp av enhetsetableringstjänsten. 

Exempelkoden använder Windows TPM-simulatorn som enhetens [maskinvarusäkerhetsmodul (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). 

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

## <a name="provision-the-simulated-device"></a>Etablera den simulerade enheten

1. Logga in på Azure Portal. Välj knappen **Alla resurser** på menyn till vänster och öppna tjänsten Enhetsetablering. Notera värdet för **_ID-scope_** i bladet **Översikt.**

    ![Kopiera omfångs-ID för etableringstjänsten från portalbladet](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. I en kommandotolk ändrar du kataloger till projektkatalogen för TPM-enhetsetableringsexemplet.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Skriv följande kommando för att skapa och köra TPM-enhetsetableringsexemplet. Ersätt `<IDScope>`-värdet med ID-omfånget för etableringstjänsten. 

    ```cmd
    dotnet run <IDScope>
    ```

    Det här kommandot startar simulatorn för TPM-chip i en separat kommandotolk. I Windows kan du stöta på en Windows-säkerhetsvarning som frågar om du vill tillåta att Simulator.exe kommunicerar i offentliga nätverk. I det här exemplet kan du avbryta begäran.

1. I det ursprungliga kommandofönstret visas **_bekräftelsenyckeln,_** **_registrerings-ID:t_** och ett föreslaget **_enhets-ID_** som behövs för enhetsregistrering. Anteckna värdena. Du använder dessa värden för att skapa en enskild registrering i din instans av enhetsetableringstjänsten. 
   > [!NOTE]
   > Blanda inte ihop fönstret som innehåller utdata från kommandot med fönstret som innehåller utdata från TPM-simulatorn. Du kan behöva välja det ursprungliga kommandofönstret för att föra det till förgrunden.

    ![Utdata från fönstret Kommando](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. Välj **Hantera registreringar**på menyn Enhetsetablering på Azure-portalen. Välj fliken **Enskilda registreringar** och välj knappen **Lägg till individuell registrering** högst upp. 

1. Ange följande information på panelen **Lägg till registrering:**
   - Välj **TPM** som identitet för bestyrkande *mekanism*.
   - Ange *registrerings-ID* och *bekräftelsenyckel* för din TPM-enhet från de värden som du noterade tidigare.
   - Välj en IoT hub som är länkad till din etableringstjänst.
   - Du kan även ange följande information:
       - Ange ett unikt *enhets-ID* (du kan använda det föreslagna eller ange ditt eget). Se till att undvika känsliga data när du namnger din enhet. Om du väljer att inte ange ett används registrerings-ID:et för att identifiera enheten i stället.
       - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
   - När du är klar trycker du på **knappen Spara.** 

     ![Ange information för enhetsregistrering på portalbladet](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Vid lyckad registrering visas *Registrerings-ID* för enheten i listan under fliken *Individual Enrollments* (Enskilda registreringar). 

1. Tryck på *Retur* i kommandofönstret (det som visade **_bekräftelsetangenten_**, **_registrerings-ID_** och föreslog **_enhets-ID)_** för att registrera den simulerade enheten. Lägg märke till de meddelanden som simulerar enhetsstart och anslutning till Device Provisioning-tjänsten för att hämta IoT-hubinformationen. 

1. Kontrollera att enheten har etablerats. När den simulerade enheten har etablerats till IoT-hubben som är länkad till etableringstjänsten visas enhets-ID:et på hubbens **IoT-enhetsblad.** 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som skapas i den här snabbstarten. Om du inte planerar att fortsätta använder du följande steg för att ta bort alla resurser som skapats av den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Stäng fönstret för TPM-simulatorn på datorn.
1. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din enhetsetableringstjänst. Tryck på **Ta bort** högst upp i fönstret högst upp i **översiktsbladet.**  
1. På menyn till vänster i Azure-portalen väljer du **Alla resurser** och väljer sedan din IoT-hubb. Tryck på **Ta bort** högst upp i fönstret högst upp i **översiktsbladet.**  

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en TPM-simulerad enhet på datorn och etablerat den i din IoT-hubb med hjälp av IoT Hub Device Provisioning Service. Om du vill veta hur du registrerar TPM-enheten programmässigt fortsätter du till snabbstarten för programmatisk registrering av en TPM-enhet. 

> [!div class="nextstepaction"]
> [Snabbstart i Azure – Registrera TPM-enhet till Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-csharp.md)
