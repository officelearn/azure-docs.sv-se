---
title: Quickstart - Use symmetric key to provision simulated device to Azure IoT Hub using C
description: I den här snabbstarten ska du använda SDK för C-enheten för att skapa en simulerad enhet som använder en symmetrisk nyckel med Azure IoT Hub Device Provisioning-tjänsten
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4d1e1ff5312cfb5aa0b2e378a24d5e0db7fb3605
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423546"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Snabbstart: Etablera en simulerad enhet med symmetriska nycklar

I den här snabbstarten får lära dig att skapa och köra en enhetssimulator på en Windows-utvecklingsdator. Du konfigurerar den här simulerade enheten för att använda en symmetrisk nyckel för att autentisera med hjälp av en enhetsetableringstjänstinstans och för att tilldelas till en IoT-hubb. Exempelkoden från [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) används för att simulera en startsekvens för enheten som initierar etablering. Enheten identifieras baserat på enskild registrering med etableringstjänstinstansen och tilldelas till en IoT-hubb.

Även om den här artikeln visar etablering med en enskild registrering kan du använda samma procedurer med registreringsgrupper. Den enda skillnaden är att du måste använda en härledd enhetsnyckel med ett unikt registrerings-ID för enheten. Med registreringsgrupper används den symmetriska nyckeln i registreringen inte direkt. Även om registreringsgrupper för symmetrisk nyckel inte är begränsade till äldre enheter finns det ett exempel för registreringsgrupper i avsnittet om [hur du etablerar äldre enheter med symmetrisk nyckelattestering](how-to-legacy-device-symm-key.md). Mer information finns i avsnittet om [gruppregistreringar för symmetrisk nyckelattestering](concepts-symmetric-key-attestation.md#group-enrollments).

Om du inte känner till processen för automatisk etablering bör du gå igenom [Begrepp inom automatisk etablering](concepts-auto-provisioning.md). 

Se även till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub-enhetsetableringstjänsten med Azure-portalen) innan du fortsätter med den här snabbstarten. Den här snabbstarten kräver att du redan har skapat en Device Provisioning Service-instans.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns i informationen om att [etablera för flera innehavare](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Krav

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 or later with the ['Desktop development with C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) workload enabled.
* Senaste versionen av [Git](https://git-scm.com/download/) installerad.


<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du en utvecklingsmiljö som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

SDK innehåller exempelkod för en simulerad enhet. Den här simulerade enheten försöker etablera under enhetens startsekvens.

1. Download the [CMake build system](https://cmake.org/download/).

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona Azure IoT C SDK GitHub-lagringsplatsen:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Den här åtgärden kan förväntas ta flera minuter att slutföra.


3. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Kör följande kommando som skapar en version av SDK:t som är specifik för plattformen i din utvecklingsklient. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Om `cmake` inte hittar din C++-kompilerare kan du få kompileringsfel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Skapa en post för enhetsregistrering i portalen

1. Sign in to the Azure portal, select the **All resources** button on the left-hand menu and open your Device Provisioning service.

2. Select the **Manage enrollments** tab, and then select the **Add individual enrollment** button at the top. 

3. In the **Add Enrollment** panel, enter the following information, and press the **Save** button.

   - **Mekanism:** välj **Symmetrisk nyckel** som identitetsattesterings*mekanism*.

   - **Auto-generate keys**: Check this box.

   - **Registrerings-ID**: Ange ett registrerings-ID för att identifiera registreringen. Använd endast alfanumeriska gemener och bindestreck (”-”). For example, **symm-key-device-007**.

   - **Enhets-ID för IoT Hub:** Ange en enhetsidentifierare. Till exempel **device-007**.

     ![Lägga till en enskild registrering för symmetrisk nyckelattestering i portalen](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. Once you have saved your enrollment, the **Primary Key** and **Secondary Key** will be generated and added to the enrollment entry. Registreringen av din symmetriska nyckel visas som **symm-key-device-007** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar*. 

    Öppna registreringen och kopiera värdet för din genererade **primärnyckel**.



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulera första startsekvens för enheten

I det här avsnittet uppdaterar du exempelkoden för att skicka enhetens startsekvens till din instans av enhetsetableringstjänsten. Den här startsekvensen medför att enheten identifieras och tilldelas till en IoT-hubb som är länkad till instansen av enhetsetableringstjänsten.



1. In the Azure portal, select the **Overview** tab for your Device Provisioning service and note the **_ID Scope_** value.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. I Visual Studio öppnar du lösningsfilen **azure_iot_sdks.sln** som har genererats genom att köra CMake. Lösningsfilen bör vara på följande plats:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. I fönstret *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Exempel**. Expandera exempelprojektet som heter **prov\_dev\_client\_sample**. Expandera **Källfiler** och öppna **prov\_dev\_client\_sample.c**.

4. Hitta konstanten `id_scope` och ersätt värdet med ditt värde för **ID-omfång** som du kopierade tidigare. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Hitta definitionen för funktionen `main()` i samma fil. Kontrollera att variabeln `hsm_type` är inställd på `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` enligt nedan:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Leta upp anropet till `prov_dev_set_symmetric_key_info()` i **prov\_dev\_client\_sample.c** som har kommenterats bort.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Avkommentera funktionsanropet och ersätt platshållarvärdena (inklusive hakparenteserna) med ditt registrerings-ID och primärnyckelvärdena.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```
   
    Spara filen.

7. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt). 

8. I Visual Studio-menyn väljer du **Felsökning** > **Starta utan felsökning** för att köra lösningen. In the prompt to rebuild the project, select **Yes**, to rebuild the project before running.

    Följande utdata är ett exempel på när den simulerade enheten lyckas med starten och ansluter till etableringstjänstinstansen för att tilldelas en IoT-hubb:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

9. In the portal, navigate to the IoT hub your simulated device was assigned to and select the **IoT devices** tab. On successful provisioning of the simulated to the hub, its device ID appears on the **IoT Devices** blade, with *STATUS* as **enabled**. You might need to press the **Refresh** button at the top. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-symm-key/hub-registration.png) 


## <a name="clean-up-resources"></a>Rensa resurser

If you plan to continue working on and exploring the device client sample, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning service. Open **Manage Enrollments** for your service, and then select the **Individual Enrollments** tab. Select the check box next to the *REGISTRATION ID* of the device you enrolled in this quickstart, and press the **Delete** button at the top of the pane. 
1. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. Open **IoT devices** for your hub, select the check box next to the *DEVICE ID* of the device you registered in this quickstart, and then press the **Delete** button at the top of the pane.

## <a name="next-steps"></a>Nästa steg

In this quickstart, you’ve created a simulated device on your Windows machine and provisioned it to your IoT hub using Symmetric key with the Azure IoT Hub Device Provisioning Service on the portal. To learn how to enroll your device programmatically, continue to the quickstart for programmatic enrollment of X.509 devices. 

> [!div class="nextstepaction"]
> [Azure quickstart - Enroll X.509 devices to Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
