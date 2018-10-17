---
title: Anslutna fabrikslösningen vanliga frågor och svar – Azure | Microsoft Docs
description: Vanliga frågor om lösningsaccelerator ansluten fabrik
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e9e88fc9aa3aad902c140ac176e31571b9e55ee3
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353749"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Vanliga frågor om ansluten fabrik lösningsaccelerator

Se även allmänna [vanliga frågor och svar](iot-accelerators-faq.md) för IoT-Lösningsacceleratorer.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Var hittar jag källkoden för solution accelerator

Källkoden lagras i följande GitHub-lagringsplatsen:

* [Ansluten fabrik lösningsaccelerator](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Vad är OPC UA?

OPC Unified arkitektur (UA), ut i 2008, är en plattformsoberoende, tjänst-orienterade samverkan som standard. OPC UA används av olika industriella system och enheter, till exempel bransch datorer, PLCs och sensorer. OPC UA integrerar funktionerna i de klassiska till OPC specifikationerna ett utökningsbart ramverk med inbyggd säkerhet. Det är en standard som drivs av OPC Foundation. Den [OPC Foundation](http://opcfoundation.org/) är en icke-vinstdrivande organisation med fler än 440 medlemmar. Syftet med organisationen är att använda OPC-specifikationer för att underlätta flera leverantörer, flera plattformar, säker och tillförlitlig samverkan via:

* Infrastruktur
* Specifikationer
* Teknologi
* Processer

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Varför Microsoft valde OPC UA för ansluten fabrik lösningsaccelerator?

Microsoft har valt OPC UA eftersom det är en öppen plattform, icke-exklusiva, oberoende känns igen av branschen och beprövade standard. Det är ett krav för att säkerställa interoperabilitet mellan en bred uppsättning tillverkningsprocesser och utrustning Industrie 4.0 (RAMI4.0) referens arkitektur lösningar. Microsoft uppmärksammat ökat behov hos sina kunder att skapa lösningar för industri 4.0. Stöd för OPC UA hjälper dig att minska hinder för kunder att uppnå sina mål och ger omedelbar affärsvärde till dem.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hur lägger jag till en offentlig IP-adress i simuleringen VM?

Du har två alternativ för att lägga till IP-adress:

* Använd PowerShell-skript `Simulation/Factory/Add-SimulationPublicIp.ps1` i den [databasen](https://github.com/Azure/azure-iot-connected-factory). Skicka in ditt namn som en parameter. En lokal distribution, använda `<your username>ConnFactoryLocal`. Skriptet skriver ut IP-adressen för den virtuella datorn.

* Leta upp resursgruppen för din distribution i Azure-portalen. Förutom en lokal distribution har resursgruppen namn som du angav som lösning eller distribution. För en lokal distribution med build-skriptet, namnet på resursgruppen är `<your username>ConnFactoryLocal`. Lägg nu till en ny **offentliga IP-adressen** resursen till resursgruppen.

> [!NOTE]
> Kontrollera i vilket fall måste du installera de senaste uppdateringarna genom att följa anvisningarna i den [Ubuntu webbplats](https://wiki.ubuntu.com/Security/Upgrades). Kontinuerligt installationen för så länge som den virtuella datorn kan nås via en offentlig IP-adress.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hur tar jag bort simuleringen VM offentliga IP-adressen?

Du har två alternativ för att ta bort IP-adress:

* Använd PowerShell script Simulation/Factory/Remove-SimulationPublicIp.ps1 av den [databasen](https://github.com/Azure/azure-iot-connected-factory). Skicka in ditt namn som en parameter. En lokal distribution, använda `<your username>ConnFactoryLocal`. Skriptet skriver ut IP-adressen för den virtuella datorn.

* Leta upp resursgruppen för din distribution i Azure-portalen. Förutom en lokal distribution har resursgruppen namn som du angav som lösning eller distribution. För en lokal distribution med build-skriptet, namnet på resursgruppen är `<your username>ConnFactoryLocal`. Nu ta bort den **offentliga IP-adressen** resurs från resursgruppen.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hur loggar jag in till simuleringen VM?

Logga in på simuleringen VM stöds endast om du har distribuerat din lösning med hjälp av PowerShell-skriptet `build.ps1` i den [databasen](https://github.com/Azure/azure-iot-connected-factory).

Om du har distribuerat en lösning från www.azureiotsolutions.com kan logga du inte in till den virtuella datorn. Du kan inte logga in, eftersom lösenordet genereras slumpmässigt och du kan inte återställa den.

1. Lägg till en offentlig IP-adress till den virtuella datorn. Se [hur lägger jag till en offentlig IP-adress i simuleringen VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Skapa en SSH-session till den virtuella datorn med IP-adressen för den virtuella datorn.
1. Användarnamnet som ska användas är: `docker`.
1. Lösenordet som ska användas beror på vilken version som du använde för att distribuera:
    * För lösningar som distribuerats med hjälp av build.ps1 skript före den 1 juni 2017, lösenordet är: `Passw0rd`.
    * För lösningar som distribuerats med hjälp av build.ps1 skriptet efter den 1 juni 2017, kan du hitta lösenordet i den `<name of your deployment>.config.user` filen. Lösenordet lagras i den **VmAdminPassword** inställningen. Lösenordet genereras slumpmässigt vid tidpunkten för distribution om du inte anger den med hjälp av den `build.ps1` skript parametern `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hur jag för att stoppa och starta alla docker-processer i simuleringen VM?

1. Logga in på simuleringen VM. Se [hur loggar jag in till simuleringen VM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Om du vill kontrollera vilka behållare som är aktiva, kör: `docker ps`.
1. Om du vill stoppa alla simulering av behållare, kör: `./stopsimulation`.
1. Starta alla simulering behållare:
    * Exportera en gränssnittsvariabel med namnet **IOTHUB_CONNECTIONSTRING**. Använd värdet för den **IotHubOwnerConnectionString** i den `<name of your deployment>.config.user` filen. Exempel:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Kör `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hur uppdaterar jag simulering i den virtuella datorn?

Om du har gjort några ändringar i simuleringen, kan du använda PowerShell-skriptet `build.ps1` i den [databasen](https://github.com/Azure/azure-iot-connected-factory) med hjälp av den `updatedimulation` kommando. Det här skriptet skapar alla simuleringskomponenter, stoppas simuleringen i den virtuella datorn, laddar upp, installerar och startar dem.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hur tar jag reda på anslutningssträngen för IoT hub som används av min lösning?

Om du har distribuerat din lösning med den `build.ps1` skript i den [databasen](https://github.com/Azure/azure-iot-connected-factory), anslutningssträngen är värdet för **IotHubOwnerConnectionString** i den `<name of your deployment>.config.user` filen.

Du kan också hitta anslutningssträngen med hjälp av Azure portal. Leta upp inställningarna för anslutningssträngen i IoT Hub-resurs i resursgruppen för din distribution.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Vilka IoT Hub-enheter använder ansluten fabrik simuleringen?

Simuleringen själv registrerar följande enheter:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* Publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* Publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Med hjälp av den [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) eller [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) verktyg, du kan kontrollera vilka enheter som är registrerade med IoT hub med hjälp av din lösning. Om du vill använda enhetsutforskare, behöver du anslutningssträngen för IoT-hubben i distributionen. För att använda IoT-tillägget för Azure CLI, behöver du namnet på din IoT-hubb.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hur får jag loggdata från simuleringskomponenter?

Alla komponenter i simuleringen logga information in loggfiler. Dessa filer kan hittas i den virtuella datorn i mappen `home/docker/Logs`. Du kan använda PowerShell-skript för att hämta loggarna `Simulation/Factory/Get-SimulationLogs.ps1` i den [databasen](https://github.com/Azure/azure-iot-connected-factory).

Det här skriptet måste logga in på den virtuella datorn. Du kan behöva ange autentiseringsuppgifter för inloggningen. Se [hur loggar jag in till simuleringen VM?](#how-do-i-sign-in-to-the-simulation-vm) att hitta autentiseringsuppgifterna.

Skriptet lägger till/tar bort en offentlig IP-adress till den virtuella datorn om den ännu inte har någon och tar bort den. Skriptet placerar alla loggfiler i ett Arkiv och hämtar arkivet till utvecklingsdatorn.

Du kan också logga in på den virtuella datorn via SSH och granska loggfilerna vid körning.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hur kan jag kontrollera om simuleringen skickar data till molnet?

Med den [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) eller [Azure IoT CLI-tillägg övervaka-händelser](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) kommandot, du kan granska de data som skickas till IoT Hub från vissa enheter. Om du vill använda de här verktygen behöver du anslutningssträngen för IoT-hubb i din distribution. Se [hur tar jag reda på anslutningssträngen för IoT hub som används av min lösning?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Granska de data som skickas av en av enheterna som utgivare:

* publisher.beijing.corp.contoso
* Publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* Publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Om du ser inga data skickas till IoT Hub är ett problem med simuleringen. Som ett första steg för analys bör du analysera loggfiler simulering komponenter. Se [hur får jag loggdata från simuleringskomponenter?](#how-can-i-get-log-data-from-the-simulation-components) Prova sedan att stoppa och starta simuleringen och om det fortfarande finns inga data skickas, uppdatera simuleringen helt. Se [hur uppdaterar jag simulering i den virtuella datorn?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hur aktiverar jag en interaktiv karta i lösningen för ansluten fabrik?

Du måste ha ett Azure Maps-konto om du vill aktivera en interaktiv karta i lösningen för ansluten fabrik.

När du distribuerar från [www.azureiotsolutions.com](http://www.azureiotsolutions.com), distributionsprocessen lägger till ett Azure Maps-konto i resursgruppen som innehåller solution accelerator-tjänster.

När du distribuerar med hjälp av den `build.ps1` skript i ansluten fabrik GitHub-lagringsplatsen uppsättningen miljövariabeln `$env:MapApiQueryKey` i fönstret bygga den [nyckeln för ditt Azure Maps-konto](../azure-maps/how-to-manage-account-keys.md). Den interaktiva kartan aktiveras sedan automatiskt.

Du kan också lägga till en Azure Maps-kontonyckel utvecklingsacceleratorn efter distributionen. Gå till Azure-portalen och åtkomst till App Service-resursen i distributionen ansluten fabrik. Gå till **programinställningar**, hittar du ett avsnitt **programinställningar**. Ange den **MapApiQueryKey** till den [nyckeln för ditt Azure Maps-konto](../azure-maps/how-to-manage-account-keys.md). Spara inställningarna och gå sedan till **översikt** och starta om App Service.

### <a name="how-do-i-create-a-azure-maps-account"></a>Hur skapar jag en Azure Maps-konto?

Se, [så här hanterar du ditt Azure Maps-konto och dina nycklar](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Hur du hämtar din nyckel för Azure Maps-konto

Se, [så här hanterar du ditt Azure Maps-konto och dina nycklar](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hur aktiverar den interaktiva kartan när du felsöker lokalt?

Om du vill aktivera den interaktiva kartan när du felsöker lokalt, ange värdet för inställningen `MapApiQueryKey` i filerna `local.user.config` och `<yourdeploymentname>.user.config` i roten för din distribution till värdet för den **QueryKey** du kopierade tidigare.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hur kan jag använda en annan avbildning på startsidan för instrumentpanelen?

Ändra statisk avbildningen visas i/o öppnas startsidan för instrumentpanelen, ersätta den `WebApp\Content\img\world.jpg`. Sedan återskapa och distribuera om Webbappen.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hur kan jag använda icke OPC UA-enheter med ansluten fabrik?

Skicka telemetridata från icke OPC UA enheter till ansluten fabrik:

1. [Konfigurera en ny station i topologin av ansluten fabrik](iot-accelerators-connected-factory-configure.md) i den `ContosoTopologyDescription.json` filen.

1. Mata in telemetridata i ansluten fabrik kompatibla JSON-format:

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

1. Formatet för `<timestamp>` är: `2017-12-08T19:24:51.886753Z`

1. Starta om App Service-ansluten fabrik.

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Distribuera lösningsaccelerator för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up)
