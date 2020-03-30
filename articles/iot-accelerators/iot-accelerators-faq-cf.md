---
title: Vanliga frågor och svar om ansluten fabrikslösning – Azure | Microsoft-dokument
description: Den här artikeln besvarar de vanliga frågorna för connected factory-lösningsacceleratorn. Den innehåller länkar till GitHub-databasen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c84452ff71fa34a65b2e56ec753b68bf551c7e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826279"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Vanliga frågor och svar för accelerator för Anslutningsfabrik

Se även de allmänna [vanliga frågorna](iot-accelerators-faq.md) för IoT-lösningsacceleratorer.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Var hittar jag källkoden för lösningsacceleratorn?

Källkoden lagras i följande GitHub-databas:

* [Ansluten fabrikslösningsaccelerator](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Vad är OPC UA?

OPC Unified Architecture (UA), som släpptes 2008, är en plattformsoberoende, tjänsteorienterad interoperabilitetsstandard. OPC UA används av olika industriella system och enheter såsom industridatorer, PLC och sensorer. OPC UA integrerar funktionaliteten i OPC Classic-specifikationerna i ett utökningsbart ramverk med inbyggd säkerhet. Det är en standard som drivs av OPC Foundation. [OPC Foundation](https://opcfoundation.org/) är en ideell organisation med mer än 440 medlemmar. Organisationens mål är att använda OPC-specifikationer för att underlätta multi-leverantör, multi-plattform, säker och tillförlitlig interoperabilitet genom:

* Infrastruktur
* Specifikationer
* Teknologi
* Processer

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Varför valde Microsoft OPC UA för Connected Factory-lösningsacceleratorn?

Microsoft valde OPC UA eftersom det är en öppen, icke-proprietär, plattformsoberoende, branschkänd och beprövad standard. Det är ett krav för Industrie 4.0 (RAMI4.0) referensarkitekturlösningar som säkerställer driftskompatibilitet mellan en bred uppsättning tillverkningsprocesser och utrustning. Microsoft ser efterfrågan från sina kunder att bygga Industri 4.0-lösningar. Stöd för OPC UA hjälper till att minska barriären för kunder att uppnå sina mål och ger omedelbar affärsvärde till dem.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hur lägger jag till en offentlig IP-adress i simuleringsdasss?

Du har två alternativ för att lägga till IP-adressen:

* Använd PowerShell-skriptet `Simulation/Factory/Add-SimulationPublicIp.ps1` i [databasen](https://github.com/Azure/azure-iot-connected-factory). Skicka in distributionsnamnet som en parameter. Använd `<your username>ConnFactoryLocal`för en lokal distribution . Skriptet skriver ut IP-adressen för den virtuella datorn.

* Leta reda på resursgruppen för distributionen i Azure-portalen. Med undantag för en lokal distribution har resursgruppen det namn som du angav som lösnings- eller distributionsnamn. För en lokal distribution med hjälp av byggskriptet är `<your username>ConnFactoryLocal`namnet på resursgruppen . Lägg nu till en ny **offentlig IP-adressresurs** i resursgruppen.

> [!NOTE]
> I båda fallen, se till att du installerar de senaste patcharna genom att följa instruktionerna på [Ubuntu webbplats](https://wiki.ubuntu.com/Security/Upgrades). Håll installationen uppdaterad så länge den virtuella datorn är tillgänglig via en offentlig IP-adress.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hur tar jag bort den offentliga IP-adressen till simuleringsdass?

Du har två alternativ för att ta bort IP-adressen:

* Använd PowerShell-skriptsimuleringen/Factory/Remove-SimulationPublicIp.ps1 i [databasen](https://github.com/Azure/azure-iot-connected-factory). Skicka in distributionsnamnet som en parameter. Använd `<your username>ConnFactoryLocal`för en lokal distribution . Skriptet skriver ut IP-adressen för den virtuella datorn.

* Leta reda på resursgruppen för distributionen i Azure-portalen. Med undantag för en lokal distribution har resursgruppen det namn som du angav som lösnings- eller distributionsnamn. För en lokal distribution med hjälp av byggskriptet är `<your username>ConnFactoryLocal`namnet på resursgruppen . Ta nu bort den **offentliga IP-adressresursen** från resursgruppen.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hur loggar jag in på simuleringsdasss?

Att logga in på den virtuella simuleringen stöds bara `build.ps1` om du har distribuerat lösningen med PowerShell-skriptet i [databasen](https://github.com/Azure/azure-iot-connected-factory).

Om du har distribuerat lösningen från www.azureiotsolutions.com kan du inte logga in på den virtuella datorn. Du kan inte logga in eftersom lösenordet genereras slumpmässigt och du inte kan återställa det.

1. Lägg till en offentlig IP-adress till den virtuella datorn. Se [Hur lägger jag till en offentlig IP-adress i simuleringsdasss?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Skapa en SSH-session till den virtuella datorn med hjälp av IP-adressen för den virtuella datorn.
1. Användarnamnet som ska `docker`användas är: .
1. Vilket lösenord som ska användas beror på vilken version du använde för att distribuera:
    * För lösningar som distribuerats med skriptet build.ps1 före den `Passw0rd`1 juni 2017 är lösenordet: .
    * För lösningar som distribueras med skriptet build.ps1 efter den 1 juni `<name of your deployment>.config.user` 2017 hittar du lösenordet i filen. Lösenordet lagras i **vmadminpassword-inställningen.** Lösenordet genereras slumpmässigt vid distributionen om du `build.ps1` inte anger det med skriptparametern`-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hur stoppar och startar jag alla dockerprocesser i simuleringsdassvens en?

1. Logga in på simuleringen VM. Se [Hur loggar jag in på simuleringen VM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Om du vill kontrollera vilka `docker ps`behållare som är aktiva kör du: .
1. Om du vill stoppa `./stopsimulation`alla simuleringsbehållare kör du: .
1. Så här startar du alla simuleringsbehållare:
    * Exportera en skalvariabel med namnet **IOTHUB_CONNECTIONSTRING**. Använd värdet för **iotHubOwnerConnectionString-inställningen** i `<name of your deployment>.config.user` filen. Ett exempel:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Kör `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hur uppdaterar jag simuleringen i den virtuella datorn?

Om du har gjort några ändringar i simuleringen `build.ps1` kan du `updatedimulation` använda PowerShell-skriptet i [databasen](https://github.com/Azure/azure-iot-connected-factory) med kommandot. Det här skriptet skapar alla simuleringskomponenter, stoppar simuleringen i den virtuella datorn, laddar upp, installerar och startar dem.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hur tar jag reda på anslutningssträngen för IoT-hubben som används av min lösning?

Om du har distribuerat din lösning med `build.ps1` skriptet i [databasen](https://github.com/Azure/azure-iot-connected-factory)är anslutningssträngen värdet för **IotHubOwnerConnectionString** i `<name of your deployment>.config.user` filen.

Du kan också hitta anslutningssträngen med Azure-portalen. Leta reda på anslutningsstränginställningarna i IoT Hub-resursen i resursgruppen för distributionen.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Vilka IoT Hub-enheter använder simuleringen Connected Factory?

Själva simuleringen registrerar följande enheter:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.münchen0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* utgivare.mumbai.corp.contoso
* publisher.münchen0.corp.contoso
* utgivare.rio.corp.contoso
* publisher.seattle.corp.contoso

Med [deviceexplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) eller [IoT-tillägget för Azure CLI-verktyget](https://github.com/Azure/azure-iot-cli-extension) kan du kontrollera vilka enheter som är registrerade med IoT-hubben som lösningen använder. Om du vill använda enhetsutforskaren behöver du anslutningssträngen för IoT-hubben i distributionen. Om du vill använda IoT-tillägget för Azure CLI behöver du ditt IoT Hub-namn.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hur får jag loggdata från simuleringskomponenterna?

Alla komponenter i simuleringslogginformationen i loggfiler. Dessa filer finns i den virtuella `home/docker/Logs`datorn i mappen . Om du vill hämta loggarna kan `Simulation/Factory/Get-SimulationLogs.ps1` du använda PowerShell-skriptet i [databasen](https://github.com/Azure/azure-iot-connected-factory).

Det här skriptet måste logga in på den virtuella datorn. Du kan behöva ange autentiseringsuppgifter för inloggningen. Se [Hur loggar jag in på simuleringen VM?](#how-do-i-sign-in-to-the-simulation-vm)

Skriptet lägger till/tar bort en offentlig IP-adress till den virtuella datorn, om den ännu inte har en och tar bort den. Skriptet placerar alla loggfiler i ett arkiv och hämtar arkivet till din utvecklingsarbetsstation.

Alternativt logga in på den virtuella datorn via SSH och inspektera loggfilerna vid körning.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hur kan jag kontrollera om simuleringen skickar data till molnet?

Med kommandot [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) eller [Azure IoT CLI Extension monitor-events](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) kan du granska data som skickas till IoT Hub från vissa enheter. Om du vill använda dessa verktyg måste du känna till anslutningssträngen för IoT-hubben i distributionen. Se [Hur tar jag reda på anslutningssträngen för IoT-hubben som används av min lösning?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Kontrollera data som skickas av en av utgivarenheterna:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* utgivare.mumbai.corp.contoso
* publisher.münchen0.corp.contoso
* utgivare.rio.corp.contoso
* publisher.seattle.corp.contoso

Om du inte ser några data som skickas till IoT Hub, så finns det ett problem med simuleringen. Som ett första analyssteg bör du analysera loggfilerna för simuleringskomponenterna. Se [Hur kan jag hämta loggdata från simuleringskomponenterna?](#how-can-i-get-log-data-from-the-simulation-components) Försök sedan att stoppa och starta simuleringen och om det fortfarande inte finns några data som skickas, uppdatera simuleringen helt. Se [Hur uppdaterar jag simuleringen i den virtuella datorn?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hur aktiverar jag en interaktiv karta i min Connected Factory-lösning?

Om du vill aktivera en interaktiv karta i lösningen ansluten fabrik måste du ha ett Azure Maps-konto.

När distributionen från [www.azureiotsolutions.com](https://www.azureiotsolutions.com)lägger distributionsprocessen till ett Azure Maps-konto i resursgruppen som innehåller lösningsacceleratortjänsterna.

När du distribuerar med skriptet `build.ps1` i GitHub-databasen connected factory anger du miljövariabeln `$env:MapApiQueryKey` i byggfönstret till nyckeln till ditt Azure [Maps-konto](../azure-maps/how-to-manage-account-keys.md). Den interaktiva kartan aktiveras sedan automatiskt.

Du kan också lägga till en Azure Maps-kontonyckel i din lösningsaccelerator efter distributionen. Navigera till Azure-portalen och få åtkomst till App Service-resursen i din Connected Factory-distribution. Navigera till **Programinställningar**, där du hittar ett avsnitt **Programinställningar**. Ställ in **MapApiQueryKey** till nyckeln till [ditt Azure Maps-konto](../azure-maps/how-to-manage-account-keys.md). Spara inställningarna och navigera sedan till **Översikt** och starta om App-tjänsten.

### <a name="how-do-i-create-an-azure-maps-account"></a>Hur skapar jag ett Azure Maps-konto?

Se [Så här hanterar du ditt Azure Maps-konto och dina Azure Maps-nycklar](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Så här skaffar du nyckeln till ditt Azure Maps-konto

Se [Så här hanterar du ditt Azure Maps-konto och dina Azure Maps-nycklar](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hur aktiverar den interaktiva kartan när du felsöker lokalt?

Om du vill aktivera den interaktiva kartan medan du felsöker lokalt `local.user.config` `<yourdeploymentname>.user.config` anger du värdet för inställningen `MapApiQueryKey` i filerna och i roten för distributionen till värdet för den **QueryKey som** du kopierade tidigare.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hur använder jag en annan bild på startsidan på instrumentpanelen?

Om du vill ändra den statiska bilden som visas `WebApp\Content\img\world.jpg`io på instrumentpanelens startsida ersätter du bilden . Sedan bygga om och distribuera webapp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hur använder jag icke-OPC UA-enheter med Connected Factory?

Så här skickar du telemetridata från icke OPC UA-enheter till Connected Factory:

1. [Konfigurera en ny station i topologin ansluten fabrik](iot-accelerators-connected-factory-configure.md) i `ContosoTopologyDescription.json` filen.

1. Inta telemetridata i Connected Factory-kompatibelt JSON-format:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. Formatet `<timestamp>` på är:`2017-12-08T19:24:51.886753Z`

1. Starta om tjänsten Connected Factory App.

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Distribuera accelerator för ansluten fabrikslösning](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up)
