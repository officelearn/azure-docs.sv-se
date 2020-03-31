---
title: Etablera äldre enheter med symmetriska nycklar – Azure IoT Hub Device Provisioning Service
description: Så här använder du symmetriska nycklar för att etablera äldre enheter med DPS-instansen (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434720"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Etablera äldre enheter med hjälp av symmetriska nycklar

Ett vanligt problem med många äldre enheter är att de ofta har en identitet som består av en enda information. Den här identitetsinformationen är vanligtvis en MAC-adress eller ett serienummer. Äldre enheter kanske inte har ett certifikat, TPM eller någon annan säkerhetsfunktion som kan användas för att identifiera enheten på ett säkert sätt. Enhetsetableringstjänsten för IoT-hubben innehåller symmetrisk nyckelbelysning. Symmetrisk nyckeltysstation kan användas för att identifiera en enhet som baseras på information som MAC-adressen eller ett serienummer.

Om du enkelt kan installera en [maskinvarusäkerhetsmodul (HSM)](concepts-security.md#hardware-security-module) och ett certifikat kan det vara ett bättre tillvägagångssätt för att identifiera och etablera dina enheter. Eftersom den metoden kan tillåta dig att kringgå uppdatering av koden som distribueras till alla dina enheter, och du skulle inte ha en hemlig nyckel inbäddad i enhetens bild.

Den här artikeln förutsätter att varken en HSM eller ett certifikat är ett genomförbart alternativ. Det antas dock att du har någon metod för att uppdatera enhetskod för att använda enhetsetableringstjänsten för att etablera dessa enheter. 

Den här artikeln förutsätter också att enhetsuppdateringen sker i en säker miljö för att förhindra obehörig åtkomst till huvudgruppsnyckeln eller den härledda enhetsnyckeln.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns i informationen om att [etablera för flera innehavare](how-to-provision-multitenant.md).

> [!NOTE]
> Provet som används i den här artikeln är skrivet i C. Det finns också en [C# enhet som etablerar symmetrisk nyckel prov](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) tillgänglig. Om du vill använda det här exemplet hämtar eller klonar du [azure-iot-samples-csharp-databasen](https://github.com/Azure-Samples/azure-iot-samples-csharp) och följer instruktionerna i exempelkoden. Du kan följa instruktionerna i den här artikeln om du vill skapa en symmetrisk nyckelregistreringsgrupp med hjälp av portalen och för att hitta primära och sekundära nycklar för ID-scope och registreringsgrupp som behövs för att köra exemplet. Du kan också skapa enskilda registreringar med hjälp av exemplet.

## <a name="overview"></a>Översikt

Ett unikt registrerings-ID kommer att definieras för varje enhet baserat på information som identifierar den enheten. Mac-adressen eller ett serienummer.

En registreringsgrupp som använder [symmetrisk nyckeltysstik](concepts-symmetric-key-attestation.md) kommer att skapas med enhetsetableringstjänsten. Registreringsgruppen innehåller en grupphuvudnyckel. Huvudnyckeln används för att hash varje unikt registrerings-ID för att producera en unik enhetsnyckel för varje enhet. Enheten kommer att använda den härledda enhetsnyckeln med sitt unika registrerings-ID för att intyga med enhetsetableringstjänsten och tilldelas en IoT-hubb.

Enhetskoden som visas i den här artikeln följer samma mönster som [Snabbstart: Etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md). Koden simulerar en enhet med hjälp av ett exempel från [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Den simulerade enheten intygar med en registreringsgrupp i stället för en enskild registrering som visas i snabbstarten.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Krav

* Slutförande av tjänsten [Konfigurera IoT Hub Device Provisioning med snabbstarten för Azure-portalen.](./quick-setup-auto-provision.md)

Följande förutsättningar är för en Windows-utvecklingsmiljö. För Linux eller macOS finns i lämpligt avsnitt i [Förbereda din utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i SDK-dokumentationen.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 med [arbetsbelastningen "Skrivbordsutveckling med C++"](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) aktiverat. Visual Studio 2015 och Visual Studio 2017 stöds också.

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du en utvecklingsmiljö som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

SDK innehåller exempelkoden för den simulerade enheten. Den här simulerade enheten försöker etablera under enhetens startsekvens.

1. Ladda ner [CMake bygga systemet](https://cmake.org/download/).

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Leta reda på taggnamnet för den [senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av SDK.

3. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-databasen. Använd taggen som du hittade i föregående `-b` steg som värde för parametern:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

4. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. Kör följande kommandon från `azure-iot-sdk-c` katalogen:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Kör följande kommando som skapar en version av SDK:t som är specifik för plattformen i din utvecklingsklient. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen. 

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


## <a name="create-a-symmetric-key-enrollment-group"></a>Skapa en symmetrisk nyckelregistreringsgrupp

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna instansen för enhetsetableringstjänst.

2. Välj fliken **Hantera registreringar** och klicka sedan på knappen **Lägg till registreringsgrupp** högst upp på sidan. 

3. I **Gruppen Lägg till registrering**anger du följande information och klickar på knappen **Spara.**

   - **Gruppnamn**: Ange **mylegacydevices**.

   - **Attestation typ:** Välj **symmetrisk nyckel**.

   - **Generera nycklar automatiskt**: Markera den här kryssrutan.

   - **Välj hur du vill tilldela enheter till hubbar:** Välj **statisk konfiguration** så att du kan tilldela till en viss hubb.

   - **Välj de IoT-hubbar som den här gruppen kan tilldelas:** Välj ett av dina nav.

     ![Lägga till registreringsgrupp för symmetrisk nyckeltörsstation](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. När du har sparat din registrering genereras **primärnyckeln** och **sekundärnyckel** och läggs till registreringsposten. Din symmetriska nyckelregistreringsgrupp visas som **mylegacydevices** under kolumnen *Gruppnamn* på fliken *Registreringsgrupper.* 

    Öppna registreringen och kopiera värdet för din genererade **primärnyckel**. Den här nyckeln är huvudgruppnyckeln.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Välj ett unikt registrerings-ID för enheten

Ett unikt registrerings-ID måste definieras för att identifiera varje enhet. Du kan använda MAC-adressen, serienumret eller någon unik information från enheten. 

I det här exemplet använder vi en kombination av en MAC-adress och serienummer som utgör följande sträng för ett registrerings-ID.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Skapa ett unikt registrerings-ID för din enhet. Giltiga tecken är gemener alfanumeriska och streck ('-').


## <a name="derive-a-device-key"></a>Härleda en enhetsnyckel 

Om du vill generera enhetsnyckeln använder du grupphuvudnyckeln för att beräkna ett [HMAC-SHA256-format](https://wikipedia.org/wiki/HMAC) för det unika registrerings-ID:t för enheten och konverterar resultatet till Base64-format.

Ta inte med grupphuvudnyckeln i enhetskoden.


#### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation kan du använda openssl för att generera din härledda enhetsnyckel som visas i följande exempel.

Ersätt värdet **för KEY** med **primärnyckeln** som du noterade tidigare.

Ersätt värdet **för REG_ID** med ditt registrerings-ID.

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

Om du använder en Windows-baserad arbetsstation kan du använda PowerShell för att generera den härledda enhetsnyckeln enligt följande exempel.

Ersätt värdet **för KEY** med **primärnyckeln** som du noterade tidigare.

Ersätt värdet **för REG_ID** med ditt registrerings-ID.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Enheten använder den härledda enhetsnyckeln med ditt unika registrerings-ID för att utföra symmetrisk nyckeltysst med registreringsgruppen under etableringen.



## <a name="create-a-device-image-to-provision"></a>Skapa en enhetsavbildning för att etablera

I det här avsnittet uppdaterar du ett etableringsexempel med namnet **prov\_dev-klientexempel\_\_** som finns i Azure IoT C SDK som du har konfigurerat tidigare. 

Den här exempelkoden simulerar en enhetsstartsekvens som skickar etableringsbegäran till din enhetsetableringstjänstinstans. Startsekvensen gör att enheten identifieras och tilldelas till IoT-hubben som du konfigurerade i registreringsgruppen.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Öppna lösningsfilen **azure_iot_sdks.sln** som genererades genom att köra CMake tidigare i Visual Studio. Lösningsfilen bör vara på följande plats:

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

    Avkommentera funktionsanropet och ersätt platshållarvärdena (inklusive vinkelparenteserna) med det unika registrerings-ID:t för enheten och den härledda enhetsnyckeln som du har genererat.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Spara filen.

7. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt). 

8. På Visual Studio-menyn väljer du > **Felsökningsstart utan** felsökning för att köra lösningen. **Debug** I meddelandet för att omkompilera projektet klickar du på **Ja**, för att omkompilera projektet innan du kör.

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

9. I portalen navigerar du till IoT-hubben som den simulerade enheten tilldelades och klickar på fliken **IoT-enheter.** Vid en lyckad etablering av den simulerade till navet visas dess enhets-ID på bladet **IoT-enheter,** med *STATUS* som **aktiverat**. Du kan behöva klicka på knappen **Uppdatera** längst upp. 

    ![Enheten är registrerad på IoT-hubben](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Säkerhetsproblem

Tänk på att detta lämnar den härledda enhetsnyckeln inkluderad som en del av avbildningen, vilket inte är en rekommenderad säkerhetspraxis. Detta är en anledning till att säkerhet och användarvänlighet är kompromisser. 





## <a name="next-steps"></a>Nästa steg

* Mer ometablering finns i [IoT Hub Device reetableing concepts](concepts-device-reprovision.md) 
* [Snabbstart: Etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md)
* Mer information om avetablering finns i [Så här avetablering av enheter som tidigare var automatiskt etablerade](how-to-unprovision-devices.md) 











