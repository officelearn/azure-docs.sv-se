---
title: Den här snabbstarten visar du hur använder en symmetrisk nyckel till Azure IoT Hub med C | Microsoft Docs
description: I den här snabbstarten ska du använda SDK för C-enheten för att skapa en simulerad enhet som använder en symmetrisk nyckel med Azure IoT Hub Device Provisioning-tjänsten
author: wesmc7777
ms.author: wesmc
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5899b2b667df4800bf98aa6ed7b70f2f8ba4f931
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337112"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Snabbstart: Etablera en simulerad enhet med symmetriska nycklar

I den här snabbstarten får lära dig att skapa och köra en enhetssimulator på en Windows-utvecklingsdator. Du konfigurerar den här simulerade enheten för att använda en symmetrisk nyckel för att autentisera med hjälp av en enhetsetableringstjänstinstans och för att tilldelas till en IoT-hubb. Exempelkoden från [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) används för att simulera en startsekvens för enheten som initierar etablering. Enheten identifieras baserat på enskild registrering med etableringstjänstinstansen och tilldelas till en IoT-hubb.

Även om den här artikeln visar etablering med en enskild registrering kan du använda samma procedurer med registreringsgrupper. Den enda skillnaden är att du måste använda en härledd enhetsnyckel med ett unikt registrerings-ID för enheten. Med registreringsgrupper används den symmetriska nyckeln i registreringen inte direkt. Även om registreringsgrupper för symmetrisk nyckel inte är begränsade till äldre enheter finns det ett exempel för registreringsgrupper i avsnittet om [hur du etablerar äldre enheter med symmetrisk nyckelattestering](how-to-legacy-device-symm-key.md). Mer information finns i avsnittet om [gruppregistreringar för symmetrisk nyckelattestering](concepts-symmetric-key-attestation.md#group-enrollments).

Om du inte känner till processen för automatisk etablering bör du gå igenom [Begrepp inom automatisk etablering](concepts-auto-provisioning.md). 

Se även till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub-enhetsetableringstjänsten med Azure-portalen) innan du fortsätter med den här snabbstarten. Den här snabbstarten kräver att du redan har skapat en Device Provisioning Service-instans.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns i informationen om att [etablera för flera innehavare](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

* Visual Studio 2015 eller [Visual Studio 2017](https://www.visualstudio.com/vs/) med arbetsbelastningen [”Desktop development with C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) (Skrivbordsutveckling med C++) aktiverad.
* Senaste versionen av [Git](https://git-scm.com/download/) installerad.


<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du en utvecklingsmiljö som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

SDK innehåller exempelkod för en simulerad enhet. Den här simulerade enheten försöker etablera under enhetens startsekvens.

1. Ladda ned version 3.11.4 av [CMake-buildsystemet](https://cmake.org/download/). Kontrollera den hämta binära filen med hjälp av det motsvarande kryptografiska hashvärdet. I följande exempel används Windows PowerShell för att verifiera den kryptografisk hashen för version 3.11.4 av x64 MSI-distributionen:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Följande hash-värden för version 3.11.4 visades på CMake-webbplatsen när detta skrevs:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona Azure IoT C SDK GitHub-lagringsplatsen:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Storleken på den här lagringsplatsen är för närvarande cirka 220 MB. Den här åtgärden kan förväntas ta flera minuter att slutföra.


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

1. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna Device Provisioning-tjänsten.

2. Välj fliken **Hantera registreringar** och klicka sedan på knappen **Lägg till enskild registrering** längst upp. 

3. På **Lägg till registrering** anger du följande information och klickar på knappen **Spara**.

    - **Mekanism**: Välj **Symmetrisk nyckel** som *identitetsattesteringsmekanism*.

    - **Generera nycklar automatiskt**: Markera den här kryssrutan.

    - **Registrerings-ID**: Ange ett registrerings-ID som identifierar registreringen. Använd endast alfanumeriska gemener och bindestreck (”-”). Till exempel `symm-key-device-007`.

    - **Enhets-ID för IoT Hub:** Ange en enhetsidentifierare. Till exempel **device-007**.

    ![Lägga till en enskild registrering för symmetrisk nyckelattestering i portalen](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. När du har sparat din registrering genereras **primärnyckeln** och **sekundärnyckel** och läggs till registreringsposten. Registreringen av din symmetriska nyckel visas som **symm-key-device-007** under kolumnen *Registrerings-ID* på fliken *Enskilda registreringar*. 

    Öppna registreringen och kopiera värdet för din genererade **primärnyckel**.



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulera första startsekvens för enheten

I det här avsnittet uppdaterar du exempelkoden för att skicka enhetens startsekvens till din instans av enhetsetableringstjänsten. Den här startsekvensen medför att enheten identifieras och tilldelas till en IoT-hubb som är länkad till instansen av enhetsetableringstjänsten.



1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

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

8. I Visual Studio-menyn väljer du **Felsökning** > **Starta utan felsökning** för att köra lösningen. I meddelandet för att omkompilera projektet klickar du på **Ja**, för att omkompilera projektet innan du kör.

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

9. I portalen går du till den IoT-hubb som din simulerade enhet tilldelades och klickar på fliken **IoT-enheter**. Vid lyckad etablering av den simulerade enheten till hubben visas dess enhets-ID på bladet **IoT-enheter** med *STATUS* **aktiverad**. Du kan behöva klicka på knappen **Uppdatera** längst upp. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som har skapats i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Öppna **Hantera registreringar** för tjänsten och klicka på fliken **Enskilda registreringar**. Välj *REGISTRERINGS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp. 
1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Öppna **IoT-enheter** för din hubb, välj *ENHETS-ID* för den enhet som du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en simulerad enhet på Windows-datorn och etablerat den på IoT-hubben med hjälp av symmetrisk nyckel med Azure IoT Hub Device Provisioning-tjänsten på portalen. Information om hur du registrerar enheten programmässigt får du om du fortsätter till snabbstarten för programmässig registrering av X.509-enheter. 

> [!div class="nextstepaction"]
> [Azure snabbstart – Registrera X.509-enheter på Azure IoT Hub Device Provisioning-tjänsten](quick-enroll-device-x509-java.md)
