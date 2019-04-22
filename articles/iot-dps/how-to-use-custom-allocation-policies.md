---
title: Hur du använder anpassade allokeringsprinciper med Azure IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Hur du använder anpassade allokeringsprinciper med Azure IoT Hub Device Provisioning-tjänsten
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: f0eb2f7358e8fb1564275e1de510f302d2eef90b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500948"
---
# <a name="how-to-use-custom-allocation-policies"></a>Hur du använder anpassade allokeringsprinciper


En anpassad allokeringsprincip ger dig större kontroll över hur enheter tilldelas till en IoT-hubb. Detta åstadkoms med hjälp av anpassad kod i en [Azure Function](../azure-functions/functions-overview.md) tilldela enheter till en IoT-hubb. Device provisioning-tjänst anropar Azure Function-koden med alla relevanta uppgifter om enheten och registrering. Funktionskoden körs och returnerar IoT-hubinformationen som används för att etablera enheten.

Med hjälp av anpassade allokeringsprinciper definiera egna allokeringsprinciper när de principer som tillhandahålls av Device Provisioning-tjänsten inte uppfyller kraven för ditt scenario.

Till exempel kanske du vill undersöka certifikatet med hjälp av en enhet under etableringen och tilldela enheten till en IoT-hubb baserat på en certifikat-egenskap. Du kan kanske har information som lagras i en databas för dina enheter och vill söka i databasen för att avgöra vilken IoT-hubb som en enhet ska tilldelas till.


Den här artikeln visar en anpassad princip med hjälp av en Azure-funktion som skrivits i C#. Två nya IoT-hubbar skapas som representerar en *Contoso brödrostar Division* och en *Contoso termisk pumpar Division*. Enheter som begär etablering måste ha en registrerings-ID med något av följande suffix ska godkännas för etablering:

- **-contoso-tstrsd-007**: Contoso brödrostar Division
- **-contoso-hpsd-088**: Contoso termisk pumpar Division

Enheterna som ska etableras baserat på en av dessa nödvändiga suffix på registrering-ID. Dessa enheter kommer simuleras med hjälp av en allokering exempel som ingår i den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

Du utför följande steg i den här artikeln:

* Använda Azure CLI för att skapa två Contoso division IoT-hubbar (**Contoso brödrostar Division** och **Contoso termisk pumpar Division**)
* Skapa en ny gruppregistrering med en Azure-funktion för den anpassade allokeringsprincipen
* Skapa enhetsnycklar för två enheten simuleringar.
* Konfigurera utvecklingsmiljön för Azure IoT C SDK
* Simulera enheter för att se att de har etablerats enligt exempelkoden för den anpassade allokeringsprincipen


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Slutförandet av den [konfigurera IoT Hub Device Provisioning-tjänsten med Azure portal](./quick-setup-auto-provision.md) Snabbstart.
* Visual Studio 2015 eller [Visual Studio 2017](https://www.visualstudio.com/vs/) med arbetsbelastningen [”Desktop development with C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) (Skrivbordsutveckling med C++) aktiverad.
* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Skapa två avdelningar IoT-hubbar

I det här avsnittet ska du använda Azure Cloud Shell för att skapa två nya IoT-hubbar som representerar den **Contoso brödrostar Division** och **Contoso termisk pumpar division**.

1. Använda Azure Cloud Shell för att skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az-group-create) kommando. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

    I följande exempel skapas en resursgrupp med namnet *contoso-oss-resource-group* i den *eastus* region. Du rekommenderas att du använder den här gruppen för alla resurser som skapats i den här artikeln. Den här metoden gör Rensa enklare när du är klar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Använda Azure Cloud Shell för att skapa den **Contoso brödrostar Division** IoT hub med den [az iot hub skapa](/cli/azure/iot/hub#az-iot-hub-create) kommando. IoT-hubben ska läggas till *contoso-oss-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-brödrostar-hub-1098* i den *eastus* plats. Du måste använda en egen unik hubbnamn. Skapa dina egna suffix i hubbnamnet i stället för **1098**. Kodexempel för anpassade allokeringsprincipen kräver `-toasters-` i hubbnamnet.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Det här kommandot kan ta några minuter att slutföra.

3. Använda Azure Cloud Shell för att skapa den **Contoso termisk pumpar Division** IoT hub med den [az iot hub skapa](/cli/azure/iot/hub#az-iot-hub-create) kommando. Den här IoT-hubben läggs också till *contoso-oss-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-heatpumps-hub-1098* i den *eastus* plats. Du måste använda en egen unik hubbnamn. Skapa dina egna suffix i hubbnamnet i stället för **1098**. Kodexempel för anpassade allokeringsprincipen kräver `-heatpumps-` i hubbnamnet.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Det här kommandot kan också ta ett par minuter att slutföra.




## <a name="create-the-enrollment"></a>Skapa registreringen

I det här avsnittet skapar du en ny grupp för registrering som använder den anpassade allokeringsprincipen. För enkelhetens skull använder den här artikeln [symmetriska nyckelattestering](concepts-symmetric-key-attestation.md) med registreringen. Överväg att använda för en säkrare lösningen [X.509-certifikat attestering](concepts-security.md#x509-certificates) med en certifikatkedja.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna Device Provisioning Service-instans.

2. Välj den **hantera registreringar** fliken och klicka sedan på den **Lägg till grupp för registrering** längst upp på sidan. 

3. På **lägga till Registreringsgruppen**, anger du följande information och klicka på den **spara** knappen.

    **Gruppnamn**: Ange **contoso-anpassad-allokerade-enheter**.

    **Typ av attestering**: Välj **symmetrisk nyckel**.

    **Generera nycklar automatiskt**: Den här kryssrutan bör redan vara markerad.

    **Välj hur du vill tilldela enheter till hubs**: Välj **anpassad (använda Azure-funktion)**.

    ![Lägg till grupp för registrering av anpassade allokering för symmetrisk nyckelattestering](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. På **lägga till Registreringsgruppen**, klickar du på **länka en ny IoT hub** länka båda dina nya avdelningar IoT-hubbar. 

    Du måste köra det här steget för båda dina avdelningar IoT-hubbar.

    **Prenumeration**: Om du har flera prenumerationer väljer du den prenumeration där du skapade avdelningar IoT-hubbar.

    **IoT hub**: Välj en av de avdelningar nav som du skapade.

    **Princip för**: Välj **iothubowner**.

    ![Länka avdelningar IoT-hubbar med etableringstjänsten](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. På **lägga till Registreringsgruppen**, när båda avdelningar IoT-hubbar har länkats, du måste välja dem som IoT Hub-grupp för registreringsgruppen enligt nedan:

    ![Skapa avdelningar hub-grupp för registrering](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. På **lägga till Registreringsgruppen**, rulla ned till den **väljer Azure Function** och klicka **skapa en ny funktionsapp**.

7. På **Funktionsapp** sidan som öppnas anger du följande inställningar för din nya funktion och klicka på Skapa **skapa**:

    **Appnamn**: Ange ett unikt funktionsappens namn. **Contoso-function-app-1098** visas som ett exempel.

    **Resursgrupp**: Välj **Använd befintlig** och **contoso-oss-resource-group** att hålla alla resurser som skapats i den här artikeln tillsammans.

    **Application Insights**: Du kan stänga av den här övningen detta.

    ![Skapa funktionsappen](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Gå tillbaka till din **lägga till Registreringsgruppen** kontrollerar din nya funktionsapp. Du kan behöva välja prenumeration för att uppdatera listan över funktionen igen.

    När din nya funktionsapp är markerad, klickar du på **skapa en ny funktion**.

    ![Skapa funktionsappen](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    din nya funktionsapp öppnas.

9. På din funktionsapp klickar du på om du vill skapa en ny funktion

    ![Skapa funktionsappen](./media/how-to-use-custom-allocation-policies/new-function.png)

    Använda standardinställningarna för den nya funktionen för att skapa en ny **Webhook + API** med hjälp av den **CSharp** språk. Klicka på **skapa den här funktionen**.

    Detta skapar en ny C#-funktion som heter **HttpTriggerCSharp1**.

10. Ersätt Koden för den nya C#-funktionen med följande kod och klicka på **spara**:    

    ```C#
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. Gå tillbaka till din **lägga till Registreringsgruppen** sidan och kontrollera att den nya funktionen är markerad. Du kan behöva välja igen appen att uppdatera listan med funktioner.

    När den nya funktionen är markerad, klickar du på **spara** att spara grupp för registrering.

    ![Slutligen Spara grupp för registrering](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. När du har sparat registreringen, öppna den igen och anteckna den **primärnyckel**. Du måste spara registreringen först om du vill att nyckeln som skapas. Den här nyckeln används för att generera unika enhetsnycklar för simulerade enheter senare.


## <a name="derive-unique-device-keys"></a>Härled unika enhetsnycklar

I det här avsnittet skapar du två unikt enhets-nycklar. En nyckel ska användas för en simulerad toaster-enhet. Den andra nyckeln används för en simulerad termisk pump-enhet.

Om du vill generera enhetsnyckeln som du ska använda den **primärnyckel** du antecknade tidigare för att beräkna den [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) av enheten registrerings-ID för varje enhet och konvertera resultatet i Base64-format. Mer information om hur du skapar härledda enhetsnycklar med registreringsgrupper finns i avsnittet grupp registreringar av [symmetriska nyckelattestering](concepts-symmetric-key-attestation.md).

Använd de följande två enhetsregistreringen-ID för det här exemplet i den här artikeln och beräkna en enhetsnyckel för både enheter. Båda registrerings-ID: N har ett giltigt suffix att arbeta med kodexempel för anpassade allokeringsprincipen:

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation, kan du använda openssl för att generera enhetsnycklar härledda som visas i följande exempel.

1. Ersätt värdet för **nyckel** med den **primärnyckel** du antecknade tidigare.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


#### <a name="windows-based-workstations"></a>Windows-baserade arbetsstationer

Om du använder en Windows-arbetsstation, kan du använda PowerShell för att generera härledda enhetsnyckeln som du ser i följande exempel.

1. Ersätt värdet för **nyckel** med den **primärnyckel** du antecknade tidigare.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


De simulerade enheterna använda härledda enhetsnycklar med varje registrerings-ID för att utföra symmetriska nyckelattestering.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du en utvecklingsmiljö som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). SDK innehåller exempelkod för den simulerade enheten. Den här simulerade enheten försöker etablera under enhetens startsekvens.

Det här avsnittet är riktade mot en Windows-arbetsstation. En Linux-exempel finns i konfigurationsprocessen för de virtuella datorerna i [hur man etablerar för flera innehavare](how-to-provision-multitenant.md).

1. Ladda ned den [CMake-buildsystemet](https://cmake.org/download/).

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




## <a name="simulate-the-devices"></a>Simulera enheterna

I det här avsnittet ska du uppdaterar ett etablering exempel med namnet **prov\_dev\_klienten\_exempel** finns i Azure IoT C SDK du skapade tidigare. 

Den här exempelkoden simulerar en startsekvens för enheten som skickar en begäran om etablering till din instans av Device Provisioning-tjänsten. Startsekvens medför att toaster-enheten ska identifieras och tilldelad till IoT hub med hjälp av anpassade allokeringsprincipen.

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

6. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt). 


#### <a name="simulate-the-contoso-toaster-device"></a>Simulera Contoso toaster-enhet

1. Om du vill simulera toaster-enhet, hitta anropet till `prov_dev_set_symmetric_key_info()` i **prov\_dev\_klienten\_sample.c** som har kommenterats bort.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Ta bort kommentarerna funktionsanropet och Ersätt platshållarvärdena (inklusive hakparenteser) med toaster registrerings-ID och härledda enhetsnyckeln som du skapade tidigare. Nyckelvärdet **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** visas nedan ges endast som ett exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    Spara filen.

2. I Visual Studio-menyn väljer du **Felsökning** > **Starta utan felsökning** för att köra lösningen. I meddelandet för att omkompilera projektet klickar du på **Ja**, för att omkompilera projektet innan du kör.

    Följande utdata är ett exempel på simulerade toaster-enhet har startas och ansluta till etablering tjänstinstansen som ska tilldelas till IoT-hubb brödrostar av anpassade allokeringsprincipen:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Simulera Contoso termisk pump enhet

1. Uppdatera för att simulera enheten termisk pump anropet till `prov_dev_set_symmetric_key_info()` i **prov\_dev\_klienten\_sample.c** igen med den termiska pump registrerings-ID och härledda enhetsnyckel du skapade tidigare . Nyckelvärdet **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** visas nedan ges också bara som ett exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    Spara filen.

2. I Visual Studio-menyn väljer du **Felsökning** > **Starta utan felsökning** för att köra lösningen. I meddelandet för att omkompilera projektet klickar du på **Ja**, för att omkompilera projektet innan du kör.

    Följande utdata är ett exempel på simulerade termisk pump enheten har startas och ansluta till etablering tjänstinstansen som ska tilldelas till Contoso termisk pumpar IoT hub med anpassade allokeringsprincipen:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>Felsökning av anpassade allokeringsprinciper

I följande tabell visar förväntad scenarier och resultat felkoder som kan uppstå. Använd den här tabellen för att felsöka anpassad princip Allokeringsfel med Azure Functions.


| Scenario | Registrering av resultatet från Provisioning-tjänsten | Etablering SDK-resultat |
| -------- | --------------------------------------------- | ------------------------ |
| Webhooken returnerar 200 OK med 'iotHubHostName' inställt på ett giltigt värdnamn för IoT hub | Resultat: Tilldelad  | SDK: N returnerar PROV_DEVICE_RESULT_OK tillsammans med hub information |
| Webhooken returnerar 200 OK med 'iotHubHostName' i svaret, men att en tom sträng eller null | Resultat: Misslyckad<br><br> Felkod: CustomAllocationIotHubNotSpecified (400208) | SDK: N returnerar PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhooken returnerar 401 Ej behörig | Resultat: Misslyckad<br><br>Felkod: CustomAllocationUnauthorizedAccess (400209) | SDK: N returnerar PROV_DEVICE_RESULT_UNAUTHORIZED |
| En enskild registrering har skapats för att inaktivera enheten | Resultat: Disabled | SDK: N returnerar PROV_DEVICE_RESULT_DISABLED |
| Webhooken returnerar felkod > = 429 | DPS-dirigering kommer att försöka igen flera gånger. Återförsöksprincipen är för närvarande:<br><br>&nbsp;&nbsp;– Antal nya försök: 10<br>&nbsp;&nbsp;-Första intervall: 1s<br>&nbsp;&nbsp;-Öka: 9s | SDK: N ska ignorera felet och skicka en annan get-statusmeddelande inom den angivna tiden |
| Webhooken returnerar alla andra statuskod | Resultat: Misslyckad<br><br>Felkod: CustomAllocationFailed (400207) | SDK: N returnerar PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med resurser som skapats i den här artikeln kan du lämna dem. Om du inte tänker fortsätta att använda resursen kan du använda följande steg för att ta bort alla resurser som skapats av den här artikeln för att undvika onödiga avgifter.

De här stegen förutsätter att alla resurser som du skapade i den här artikeln som finns beskrivet i samma resursgrupp med namnet **contoso-oss-resource-group**.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Ta bort resursgruppen med namnet:

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

2. I den **filtrera efter namn...**  textrutan skriver namnet på resursen gruppen som innehåller dina resurser, **contoso-oss-resource-group**. 

3. Till höger av din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp igen för att bekräfta och klicka sedan på **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

- Läs mer Reprovisioning i [reprovisioning koncept för IoT Hub-enhet](concepts-device-reprovision.md) 
- Läs mer avetablering i [så ta bort etableringen av enheter som har tidigare Automatisk etablering](how-to-unprovision-devices.md) 











