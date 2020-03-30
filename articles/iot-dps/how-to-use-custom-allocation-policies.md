---
title: Anpassade allokeringsprinciper med Azure IoT Hub Device Provisioning Service
description: Så här använder du anpassade allokeringsprinciper med Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 87ffca1957d4ec449753f1966ed05cf3948f5ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453946"
---
# <a name="how-to-use-custom-allocation-policies"></a>Så här använder du anpassade allokeringsprinciper

En anpassad allokeringsprincip ger dig större kontroll över hur enheter tilldelas till en IoT-hubb. Detta åstadkoms genom att använda anpassad kod i en [Azure-funktion](../azure-functions/functions-overview.md) för att tilldela enheter till en IoT-hubb. Enhetsetableringstjänsten anropar din Azure-funktionskod som ger all relevant information om enheten och registreringen. Din funktionskod körs och returnerar IoT-hubbinformationen som används för att etablera enheten.

Genom att använda anpassade allokeringsprinciper definierar du dina egna allokeringsprinciper när principerna som tillhandahålls av enhetsetableringstjänsten inte uppfyller kraven i ditt scenario.

Du kanske till exempel vill undersöka certifikatet som en enhet använder under etableringen och tilldela enheten till en IoT-hubb baserat på en certifikategenskap. Eller kanske du har information som lagras i en databas för dina enheter och behöver fråga databasen för att avgöra vilken IoT-hubb en enhet ska tilldelas.

Den här artikeln visar en anpassad allokeringsprincip med hjälp av en Azure-funktion skriven i C#. Två nya IoT-hubbar skapas som representerar en *Contoso Toasters Division* och en *Contoso Heat Pumps Division*. Enheter som begär etablering måste ha ett registrerings-ID med något av följande suffix som ska godkännas för etablering:

* **-contoso-tstrsd-007:** Contoso Brödrostar Division
* **-contoso-hpsd-088**: Contoso värmepumpar Division

Enheterna etableras baserat på ett av dessa nödvändiga suffix på registrerings-ID: et. Dessa enheter simuleras med hjälp av ett etableringsexempel som ingår i [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

Du utför följande steg i den här artikeln:

* Använd Azure CLI för att skapa två Contoso-division IoT-hubbar **(Contoso Toasters Division** och **Contoso Heat Pumps Division**)
* Skapa en ny gruppregistrering med hjälp av en Azure-funktion för den anpassade allokeringsprincipen
* Skapa enhetsnycklar för två enhetssimuleringar.
* Konfigurera utvecklingsmiljön för Azure IoT C SDK
* Simulera enheterna och kontrollera att de är etablerade enligt exempelkoden i den anpassade allokeringsprincipen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Följande förutsättningar är för en Windows-utvecklingsmiljö. För Linux eller macOS finns i lämpligt avsnitt i [Förbereda din utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i SDK-dokumentationen.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 med [arbetsbelastningen "Skrivbordsutveckling med C++"](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) aktiverat. Visual Studio 2015 och Visual Studio 2017 stöds också.

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Skapa etableringstjänsten och två divisionella IoT-hubbar

I det här avsnittet använder du Azure Cloud Shell för att skapa en etableringstjänst och två IoT-hubbar som representerar **Contoso Toasters Division** och **Division Contoso Heat Pumps**.

> [!TIP]
> De kommandon som används i den här artikeln skapar etableringstjänsten och andra resurser på platsen i västra USA. Vi rekommenderar att du skapar dina resurser i den region som ligger närmast dig som stöder enhetsetableringstjänsten. Du kan se en lista med tillgängliga platser genom att köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, eller genom att gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka efter ”enhetsetableringstjänst”. I kommandon kan platser anges antingen i ett ord- eller flerordsformat. till exempel: westus, västra USA, västra USA, etc. Värdet är inte skiftlägeskänsligt. Om du använder flera ord för att ange platsen skriver du värdet inom citattecken; till exempel `-- location "West US"`.
>

1. Använd Azure Cloud Shell för att skapa en resursgrupp med kommandot [az group create.](/cli/azure/group#az-group-create) En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

    I följande exempel skapas en resursgrupp med namnet *contoso-us-resource-group* i *regionen Westus.* Vi rekommenderar att du använder den här gruppen för alla resurser som skapas i den här artikeln. Den här metoden kommer att göra det lättare att städa upp när du är klar.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Använd Azure Cloud Shell för att skapa en enhetsetableringstjänst med kommandot [az iot dps create.](/cli/azure/iot/dps#az-iot-dps-create) Etableringstjänsten läggs till *i contoso-us-resource-group*.

    I följande exempel skapas en etableringstjänst med namnet *contoso-table-service-1098* på *westus-platsen.* Du måste använda ett unikt tjänstnamn. Gör upp ditt eget suffix i tjänstnamnet i stället för **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Det kan ta några minuter att slutföra det här kommandot.

3. Använd Azure Cloud Shell för att skapa Division IoT-hubben **För Contoso-brödrosten** Med kommandot [az iot hub create.](/cli/azure/iot/hub#az-iot-hub-create) IoT-hubben läggs till *i contoso-us-resource-group*.

    I följande exempel skapas en IoT-hubb med namnet *contoso-toasters-hub-1098* på *westus-platsen.* Du måste använda ett unikt navnamn. Gör upp ditt eget suffix i navnamnet i stället för **1098**. Exempelkoden för den anpassade `-toasters-` allokeringsprincipen kräver i hubbnamnet.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Det kan ta några minuter att slutföra det här kommandot.

4. Använd Azure Cloud Shell för att skapa division IoT-hubben **för Contoso Värmepumpar** med kommandot [az iot hub create.](/cli/azure/iot/hub#az-iot-hub-create) Den här IoT-hubben läggs också till *i contoso-us-resource-group*.

    I följande exempel skapas ett IoT-nav med namnet *contoso-heatpumps-hub-1098* på *westus-platsen.* Du måste använda ett unikt navnamn. Gör upp ditt eget suffix i navnamnet i stället för **1098**. Exempelkoden för den anpassade `-heatpumps-` allokeringsprincipen kräver i hubbnamnet.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Det kan ta några minuter att slutföra det här kommandot.

## <a name="create-the-custom-allocation-function"></a>Skapa den anpassade allokeringsfunktionen

I det här avsnittet skapar du en Azure-funktion som implementerar din anpassade allokeringsprincip. Den här funktionen avgör vilken divisional IoT-hubb en enhet ska registreras på baserat på om dess registrerings-ID innehåller strängen **-contoso-tstrsd-007** eller **-contoso-hpsd-088**. Det ställer också in enhetens ursprungliga tillstånd baserat på om enheten är en brödrost eller en värmepump.

1. Logga in på [Azure-portalen](https://portal.azure.com). På startsidan väljer du **+ Skapa en resurs**.

2. Skriv "Funktionsapp" i sökrutan *Sök på Marketplace.* Välj **Funktionsapp**i listrutan och välj sedan **Skapa**.

3. På sidan **Skapa funktionsapp** under fliken Grunderna anger du följande inställningar för den nya **funktionsappen** och väljer **Granska + skapa:**

    **Resursgrupp:** Välj **gruppen contoso-us-resource-om** du vill behålla alla resurser som skapas i den här artikeln tillsammans.

    **Funktionsappnamn**: Ange ett unikt funktionsappnamn. I det här exemplet används **contoso-function-app-1098**.

    **Publicera**: Kontrollera att **koden** är markerad.

    **Runtime Stack:** Välj **.NET Core** i listrutan.

    **Region**: Välj samma region som resursgruppen. I det här exemplet används **västra USA**.

    > [!NOTE]
    > Som standard är Application Insights aktiverat. Application Insights är inte nödvändigt för den här artikeln, men det kan hjälpa dig att förstå och undersöka eventuella problem som du stöter på med den anpassade allokeringen. Om du vill kan du inaktivera Programstatistik genom att välja fliken **Övervakning** och sedan välja **Nej** för **Aktivera programinsikter**.

    ![Skapa en Azure-funktionsapp som är värd för den anpassade allokeringsfunktionen](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. På sidan **Sammanfattning** väljer du **Skapa** för att skapa funktionsappen. Distributionen kan ta flera minuter. När den är klar väljer du **Gå till resurs**.

5. I den vänstra rutan på sidan **+** Översikt **för** funktionsappen väljer du bredvid **Funktioner** för att lägga till en ny funktion.

    ![Lägga till en funktion i funktionsappen](./media/how-to-use-custom-allocation-policies/create-function.png)

6. På **sidan Azure Functions for .NET – komma igång** väljer du panelen **Använd en** DISTRIBUTIONSMILJÖ för steget VÄLJ **EN DISTRIBUTIONSMILJÖ** och väljer sedan **Fortsätt**.

    ![Välj portalutvecklingsmiljö](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. Välj panelen **Webhook + API** för steget **SKAPA en funktion** på nästa sida och välj sedan **Skapa**. En funktion med namnet **HttpTrigger1** skapas och portalen visar innehållet i **code-filen run.csx.**

8. Referens krävs Nuget paket. För att skapa den första enhetstvillingen använder den anpassade allokeringsfunktionen klasser som definieras i två Nuget-paket som måste läsas in i värdmiljön. Med Azure Functions refereras Nuget-paket med hjälp av en *function.host-fil.* I det här steget sparar och laddar du upp en *function.host-fil.*

    1. Kopiera följande rader till din favoritredigerare och spara filen på datorn som *function.host*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. Expandera fliken **Visa filer** till höger i fönstret i funktionen **HttpTrigger1.**

        ![Öppna visa filer](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Välj **Ladda upp,** bläddra till **filen function.proj** och välj **Öppna** för att ladda upp filen.

        ![Välj uppladdningsfil](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Ersätt koden för **funktionen HttpTrigger1** med följande kod och välj **Spara:**

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Skapa registreringen

I det här avsnittet ska du skapa en ny registreringsgrupp som använder den anpassade allokeringsprincipen. För enkelhetens skull använder den här artikeln [symmetrisk nyckeltysstik](concepts-symmetric-key-attestation.md) med registreringen. För en säkrare lösning, överväg att använda [X.509 certifikat attestation](concepts-security.md#x509-certificates) med en kedja av förtroende.

1. Öppna etableringstjänsten fortfarande på [Azure-portalen.](https://portal.azure.com)

2. Välj **Hantera registreringar** i den vänstra rutan och välj sedan knappen **Lägg till registreringsgrupp** högst upp på sidan.

3. I **Lägg till registreringsgrupp**anger du följande information och väljer knappen **Spara.**

    **Gruppnamn**: Ange **contoso-custom-allokerade enheter**.

    **Attestation typ:** Välj **symmetrisk nyckel**.

    **Generera nycklar automatiskt**: Den här kryssrutan bör redan vara markerad.

    **Välj hur du vill tilldela enheter till hubbar:** Välj **Anpassad (Använd Azure-funktion)**.

    ![Lägga till anpassad allokeringsregistreringsgrupp för symmetrisk nyckeltysstik](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. I **Lägg till registreringsgrupp**väljer du **Länka en ny IoT-hubb** för att länka båda dina nya divisionella IoT-hubbar.

    Kör det här steget för båda dina divisionella IoT-hubbar.

    **Prenumeration:** Om du har flera prenumerationer väljer du den prenumeration där du skapade divisionella IoT-hubbar.

    **IoT-hubb:** Välj en av de divisionsnav som du har skapat.

    **Åtkomstprincip:** Välj **iothubowner**.

    ![Länka divisionella IoT-hubbar med etableringstjänsten](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. På **Lägg till registreringsgrupp**måste du välja dem som IoT Hub-grupp för registreringsgruppen som visas nedan:

    ![Skapa divisionsnavgruppen för registreringen](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. I **Lägg till registreringsgrupp**bläddrar du ned till avsnittet **Välj Azure-funktion** och väljer den funktionsapp som du skapade i föregående avsnitt. Välj sedan den funktion som du skapade och välj Spara för att spara registreringsgruppen.

    ![Välj funktionen och spara registreringsgruppen](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. När du har sparat registreringen öppnar du den igen och noterar **primärnyckeln**. Du måste spara registreringen först för att nycklarna ska kunna genereras. Den här nyckeln används för att generera unika enhetsnycklar för simulerade enheter senare.

## <a name="derive-unique-device-keys"></a>Härleda unika enhetsnycklar

I det här avsnittet skapar du två unika enhetsnycklar. En nyckel används för en simulerad brödrostenhet. Den andra nyckeln kommer att användas för en simulerad värmepump enhet.

Om du vill generera enhetsnyckeln använder du den **primära nyckeln** som du noterade tidigare för att beräkna [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) för enhetsregistrerings-ID för varje enhet och konvertera resultatet till Base64-format. Mer information om hur du skapar härledda enhetsnycklar med registreringsgrupper finns i avsnittet gruppregistreringar i [Symmetric key attestation](concepts-symmetric-key-attestation.md).

I exemplet i den här artikeln använder du följande två enhetsregistrerings-ID:er och beräknar en enhetsnyckel för båda enheterna. Båda registrerings-ID:na har ett giltigt suffix som ska fungera med exempelkoden för den anpassade allokeringsprincipen:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation kan du använda openssl för att generera dina härledda enhetsnycklar som visas i följande exempel.

1. Ersätt värdet **för KEY** med **primärnyckeln** som du noterade tidigare.

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

### <a name="windows-based-workstations"></a>Windows-baserade arbetsstationer

Om du använder en Windows-baserad arbetsstation kan du använda PowerShell för att generera den härledda enhetsnyckeln enligt följande exempel.

1. Ersätt värdet **för KEY** med **primärnyckeln** som du noterade tidigare.

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

De simulerade enheterna använder de härledda enhetsnycklarna med varje registrerings-ID för att utföra symmetrisk nyckeltysstation.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du utvecklingsmiljön som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). SDK innehåller exempelkoden för den simulerade enheten. Den här simulerade enheten försöker etablera under enhetens startsekvens.

Det här avsnittet är inriktat mot en Windows-baserad arbetsstation. Ett Linux-exempel finns i inrättandet av de virtuella datorerna i [Hur du etablerar för multitenancy](how-to-provision-multitenant.md).

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

    Om `cmake` du inte hittar din C++-kompilator kan du få byggfel när du kör kommandot. Om det händer kan du prova att köra kommandot i [kommandotolken](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)i Visual Studio .

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

I det här avsnittet uppdaterar du ett etableringsexempel med namnet **prov\_dev-klientexempel\_\_** i Azure IoT C SDK som du har konfigurerat tidigare.

Den här exempelkoden simulerar en enhetsstartsekvens som skickar etableringsbegäran till din enhetsetableringstjänstinstans. Startsekvensen gör att brödrosten identifieras och tilldelas IoT-hubben med hjälp av den anpassade allokeringsprincipen.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Öppna lösningsfilen **azure_iot_sdks.sln** som genererades genom att köra CMake tidigare i Visual Studio. Lösningsfilen bör vara på följande plats:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

### <a name="simulate-the-contoso-toaster-device"></a>Simulera contoso-brödrosten

1. Om du vill simulera brödrosten `prov_dev_set_symmetric_key_info()` hittar du anropet till i **prov\_dev\_client\_sample.c** som kommenteras ut.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Avkommentera funktionsanropet och ersätt platshållarvärdena (inklusive vinkelparenteserna) med brödrostregistrerings-ID:t och den härledda enhetsnyckeln som du skapade tidigare. Nyckelvärdet **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** som visas nedan ges endast som exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Spara filen.

2. På Visual Studio-menyn väljer du > **Felsökningsstart utan** felsökning för att köra lösningen. **Debug** I uppmaningen om att återskapa projektet väljer du **Ja**, för att återskapa projektet innan du kör.

    Följande utdata är ett exempel på att den simulerade brödrostenordningen har startat upp och anslutit till etableringstjänstinstansen som ska tilldelas ioT-hubben brödrostar av den anpassade allokeringsprincipen:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulera Contoso värmepump enhet

1. Om du vill simulera värmepumpsenheten `prov_dev_set_symmetric_key_info()` uppdaterar du anropet till i **prov\_dev\_client\_sample.c** igen med registrerings-ID för värmepump och härledd enhetsnyckel som du genererade tidigare. Nyckelvärdet **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** som visas nedan ges också endast som exempel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Spara filen.

2. På Visual Studio-menyn väljer du > **Felsökningsstart utan** felsökning för att köra lösningen. **Debug** I uppmaningen om att återskapa projektet väljer du **Ja** för att återskapa projektet innan du kör.

    Följande utdata är ett exempel på att den simulerade värmepumpsenheten har startat upp och anslutit till etableringstjänstinstansen som ska tilldelas IoT-hubben för Contoso-värmepumpar av den anpassade allokeringsprincipen:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Felsöka anpassade allokeringsprinciper

I följande tabell visas förväntade scenarier och resultatfelkoder som du kan få. Använd den här tabellen för att felsöka anpassade allokeringsprincipfel med dina Azure-funktioner.

| Scenario | Registreringsresultat från etableringstjänsten | Etablera SDK-resultat |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook returnerar 200 OK med "iotHubHostName" inställt på ett giltigt IoT-hubbvärdnamn | Resultatstatus: Tilldelad  | SDK returnerar PROV_DEVICE_RESULT_OK tillsammans med hubbinformation |
| Webhooken returnerar 200 OK med "iotHubHostName" som finns i svaret, men inställd på en tom sträng eller null | Resultatstatus: Misslyckades<br><br> Felkod: CustomAllocationIotHubNotspecified (400208) | SDK returnerar PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Den webhook returnerar 401 Obehörig | Resultatstatus: Misslyckades<br><br>Felkod: CustomAllocationUnauthorizedAccess (400209) | SDK returnerar PROV_DEVICE_RESULT_UNAUTHORIZED |
| En enskild registrering har skapats för att inaktivera enheten | Resultatstatus: Inaktiverad | SDK returnerar PROV_DEVICE_RESULT_DISABLED |
| Webhooken returnerar felkod >= 429 | DPS orkestrering kommer att försöka igen ett antal gånger. Principen för återförsök är för närvarande:<br><br>&nbsp;&nbsp;- Försök igen: 10<br>&nbsp;&nbsp;- Första intervallet: 1s<br>&nbsp;&nbsp;- Ökning: 9s | SDK ignorerar felet och skickar ett annat hämta statusmeddelande under den angivna tiden |
| Webhooken returnerar någon annan statuskod | Resultatstatus: Misslyckades<br><br>Felkod: CustomAllocationFailed (400207) | SDK returnerar PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med de resurser som skapas i den här artikeln kan du lämna dem. Om du inte planerar att fortsätta använda resurserna använder du följande steg för att ta bort alla resurser som har skapats i den här artikeln för att undvika onödiga avgifter.

Stegen här förutsätter att du har skapat alla resurser i den här artikeln enligt anvisningarna i samma resursgrupp med namnet **contoso-us-resource-group**.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Så här tar du bort resursgruppen efter namn:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

2. I **textrutan Filter efter namn...** skriver du namnet på resursgruppen som innehåller dina resurser, **contoso-us-resource-group**. 

3. Till höger om resursgruppen i resultatlistan väljer du **...** sedan **Ta bort resursgrupp**.

4. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på resursgruppen igen för att bekräfta och välj sedan **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

* Mer ometablering finns i [IoT Hub Device reetableing concepts](concepts-device-reprovision.md) 
* Mer information om avetablering finns i [Så här avetablering av enheter som tidigare var automatisk etablering](how-to-unprovision-devices.md) 
