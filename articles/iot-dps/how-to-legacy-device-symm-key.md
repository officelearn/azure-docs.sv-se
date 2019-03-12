---
title: Hur du använder symmetriska nycklar för att etablera äldre enheter med Azure IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Hur du använder symmetriska nycklar för att etablera äldre enheter med din device provisioning-tjänstinstans
author: wesmc7777
ms.author: wesmc
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: cf4ee8b1b9d96ad4d32a0ac8b1682968b90653f9
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541498"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Hur du etablerar äldre enheter med symmetriska nycklar


Ett vanligt problem med många äldre enheter är att de ofta har en identitet som består av en enda typ av information. Den här identitetsinformation är vanligtvis en MAC-adress eller ett serienummer. Äldre enheter kan inte ha ett certifikat, TPM eller andra säkerhetsfunktion som kan användas för att på ett säkert sätt identifiera enheten. Enhetsetableringstjänsten för IoT-hubb innehåller symmetriska nyckelattestering. Symmetrisk nyckelattestering kan användas för att identifiera en enhet som baseras på information, t.ex. MAC-adress eller ett serienummer.

Om du kan enkelt installera en [maskinvarusäkerhetsmodul (HSM)](concepts-security.md#hardware-security-module) och ett certifikat och som kan vara en bättre metod för att identifiera och etablera dina enheter. Eftersom denna metod kan du kringgå uppdaterar den kod som distribueras till alla dina enheter och du behöver inte en hemlig nyckel som är inbäddad i din enhetsavbildning.

Den här artikeln förutsätter att varken en HSM eller ett certifikat är ett genomförbart alternativ. Det antas dock att du har någon metod för att uppdatera enheten kod för att använda Device Provisioning-tjänsten för att etablera dessa enheter. 

Den här artikeln förutsätter också att uppdaterats sker i en säker miljö för att förhindra obehörig åtkomst till den överordnade gruppnyckeln eller härledda enhetsnyckeln.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns i informationen om att [etablera för flera innehavare](how-to-provision-multitenant.md).


## <a name="overview"></a>Översikt

En unik registrerings-ID definieras för varje enhet baserat på information som identifierar enheten. Till exempel MAC-adress eller ett serienummer.

En registreringsgrupp som använder [symmetriska nyckelattestering](concepts-symmetric-key-attestation.md) skapas med Device Provisioning-tjänsten. Registreringsgruppen innehåller en huvudnyckel för gruppen. Denna master-nyckel används för att hash-varje unika registrerings-ID för att generera ett unikt enhets-nyckel för varje enhet. Enheten använder den härledda enhetsnyckeln med dess unika registrerings-ID för att intyga med Device Provisioning-tjänsten och vara tilldelad till en IoT-hubb.

Koden för enheten som visas i den här artikeln följer samma mönster som den [Snabbstart: Etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md). Kod som simulerar en enhet med ett exempel från den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Den simulerade enheten kommer intyga med en grupp för registrering i stället för en enskild registrering som visas i snabbstarten.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Förutsättningar

* Slutförandet av den [konfigurera IoT Hub Device Provisioning-tjänsten med Azure portal](./quick-setup-auto-provision.md) Snabbstart.
* Visual Studio 2015 eller [Visual Studio 2017](https://www.visualstudio.com/vs/) med arbetsbelastningen [”Desktop development with C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) (Skrivbordsutveckling med C++) aktiverad.
* Senaste versionen av [Git](https://git-scm.com/download/) installerad.


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du en utvecklingsmiljö som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

SDK innehåller exempelkod för den simulerade enheten. Den här simulerade enheten försöker etablera under enhetens startsekvens.

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


## <a name="create-a-symmetric-key-enrollment-group"></a>Skapa en symmetrisk nyckel registreringsgrupp

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna Device Provisioning Service-instans.

2. Välj den **hantera registreringar** fliken och klicka sedan på den **Lägg till grupp för registrering** längst upp på sidan. 

3. På **lägga till Registreringsgruppen**, anger du följande information och klicka på den **spara** knappen.

    - **Gruppnamn**: Ange **mylegacydevices**.

    - **Typ av attestering**: Välj **symmetrisk nyckel**.

    - **Generera nycklar automatiskt**: Markera den här kryssrutan.

    - **Välj hur du vill tilldela enheter till hubs**: Välj **statisk konfiguration** så att du kan tilldela till en specifik hubb.

    - **Välj IoT-hubbar som den här gruppen kan tilldelas till**: Välj en av dina hubs.

    ![Lägg till grupp för registrering för symmetrisk nyckelattestering](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. När du har sparat din registrering genereras **primärnyckeln** och **sekundärnyckel** och läggs till registreringsposten. Symmetrisk nyckel registreringsgruppen visas som **mylegacydevices** under den *gruppnamn* kolumnen i den *Registreringsgrupper* fliken. 

    Öppna registreringen och kopiera värdet för din genererade **primärnyckel**. Den här nyckeln är din master gruppnyckel.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Välj en unika registrerings-ID för enheten

En unik registrerings-ID måste anges för att identifiera varje enhet. Du kan använda MAC-adressen, serienummer eller unik information från enheten. 

I det här exemplet använder vi en kombination av en MAC-adress och serienummer som utgör följande sträng för ett registrerings-ID.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Skapa en unik registrerings-ID för din enhet. Giltiga tecken är alfanumeriska gemener och bindestreck (”-”).


## <a name="derive-a-device-key"></a>Härled en enhetsnyckel 

Generera enhetsnyckeln genom att använda huvudnyckeln grupp för att beräkna en [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) av unika registrerings-ID för enheten och konvertera resultatet i Base64-format.

Ta inte med din grupp huvudnyckel i din kod för enheten.


#### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation, kan du använda openssl för att generera härledda enhetsnyckeln som du ser i följande exempel.

Ersätt värdet för **nyckel** med den **primärnyckel** du antecknade tidigare.

Ersätt värdet för **REG_ID** med registrerings-ID.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Windows-baserade arbetsstationer

Om du använder en Windows-arbetsstation, kan du använda PowerShell för att generera härledda enhetsnyckeln som du ser i följande exempel.

Ersätt värdet för **nyckel** med den **primärnyckel** du antecknade tidigare.

Ersätt värdet för **REG_ID** med registrerings-ID.

```PowerShell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```PowerShell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Enheten använder härledda enhetsnyckeln med ditt unika registrerings-ID för att utföra symmetriska nyckelattestering med registreringsgruppen under etableringen.



## <a name="create-a-device-image-to-provision"></a>Skapa en enhetsavbildning att etablera

I det här avsnittet ska du uppdaterar ett etablering exempel med namnet **prov\_dev\_klienten\_exempel** finns i Azure IoT C SDK du skapade tidigare. 

Den här exempelkoden simulerar en startsekvens för enheten som skickar en begäran om etablering till din instans av Device Provisioning-tjänsten. Startsekvens medför att enheten ska identifieras och tilldelad till IoT-hubben som du har konfigurerat i registreringsgruppen.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio, öppna den **azure_iot_sdks.sln** lösningsfilen som har genererats genom att köra CMake tidigare. Lösningsfilen bör vara på följande plats:

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

    Ta bort kommentarerna funktionsanropet och Ersätt platshållarvärdena (inklusive hakparenteser) med unika registrerings-ID för enheten och härledda enhetsnyckeln som du skapade.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
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
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. I portalen går du till den IoT-hubb som din simulerade enhet tilldelades och klickar på fliken **IoT-enheter**. Vid lyckad etablering av den simulerade enheten till hubben visas dess enhets-ID på bladet **IoT-enheter** med *STATUS* **aktiverad**. Du kan behöva klicka på knappen **Uppdatera** längst upp. 

    ![Enheten är registrerad på IoT-hubben](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Säkerhetsfrågor

Tänk på att det innebär att den härledda enhetsnyckel som en del av avbildningen, som inte är en rekommenderad säkerhetsåtgärd. Det här är en anledning till varför säkerhet och enkel att använda är kompromisser. 





## <a name="next-steps"></a>Nästa steg

* Läs mer Reprovisioning i [IoT Hub Device reprovisoning begrepp](concepts-device-reprovision.md) 
* [Snabbstart: Etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md)
* Läs mer avetablering i [så ta bort etableringen av enheter som har tidigare Automatisk etablering](how-to-unprovision-devices.md) 











