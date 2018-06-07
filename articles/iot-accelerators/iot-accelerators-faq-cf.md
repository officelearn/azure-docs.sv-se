---
title: Ansluten Factory lösning vanliga frågor och svar – Azure | Microsoft Docs
description: Vanliga frågor om anslutna Factory solution accelerator
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: dbdd9c70fc135561eb0e60e5932e446bcfa3759b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627509"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Vanliga frågor om anslutna Factory solution accelerator

Se även allmänna [vanliga frågor och svar](iot-accelerators-faq.md) för IoT-Lösningsacceleratorer.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Var hittar källkoden för solution accelerator?

Källkoden lagras i följande GitHub-lagringsplatsen:

* [Anslutna Factory solution accelerator](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Vad är OPC UA?

OPC enhetlig arkitektur (UA), utgivet 2008, är en plattformsoberoende, tjänstorienterad samverkan som standard. OPC UA används av olika industriella datorer och enheter, till exempel branschen datorer, sensorer och PLCs. OPC UA integrerar funktionerna i de klassiska OPC specifikationerna i ett utökningsbart ramverk med inbyggd säkerhet. Det är en standard som styrs av OPC Foundation. Den [OPC Foundation](http://opcfoundation.org/) är en icke-kommersiell organisation med mer än 440 medlemmar. Målet för organisationen är att använda OPC specifikationer för att underlätta flera leverantörer, flera plattformar, säkert och tillförlitligt interoperabilitet via:

* Infrastruktur
* Specifikationer
* Teknologi
* Processer

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Varför Microsoft välja OPC UA för anslutna Factory solution accelerator?

Microsoft har valt OPC UA eftersom det är en öppen plattform för icke-generiska, oberoende branschen identifieras och beprövade standard. Det är ett krav för att säkerställa samverkan mellan en bred uppsättning tillverkningsprocesser och utrustning Industrie 4.0 (RAMI4.0) referens arkitektur lösningar. Microsoft ser begäran från sina kunder att skapa Industrie 4.0 lösningar. Stöd för OPC UA hjälper dig att minska barriären för kunder att nå sina mål och ger omedelbar affärsvärde dem.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hur lägger jag till en offentlig IP-adress i simuleringen VM?

Du har två alternativ för att lägga till IP-adressen:

* Använd PowerShell-skriptet `Simulation/Factory/Add-SimulationPublicIp.ps1` i den [databasen](https://github.com/Azure/azure-iot-connected-factory). Ange distributionsnamnet på din som en parameter. En lokal distribution använda `<your username>ConnFactoryLocal`. Skriptet skriver ut IP-adressen för den virtuella datorn.

* Leta upp resursgruppen för distributionen i Azure-portalen. Förutom en lokal distribution har resursgruppen namn som du angav som lösning eller distribution. För en lokal distribution med skriptet build, namnet på resursgruppen är `<your username>ConnFactoryLocal`. Lägg nu till en ny **offentliga IP-adressen** resurs till resursgruppen.

> [!NOTE]
> Kontrollera i båda fallen kan du installera de senaste korrigeringarna genom att följa anvisningarna på den [Ubuntu webbplats](https://wiki.ubuntu.com/Security/Upgrades). Kontinuerligt installationen för så länge som den virtuella datorn kan nås via en offentlig IP-adress.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hur tar jag bort simuleringen VM offentliga IP-adressen?

Du har två alternativ för att ta bort IP-adressen:

* Använd PowerShell script Simulation/Factory/Remove-SimulationPublicIp.ps1 av den [databasen](https://github.com/Azure/azure-iot-connected-factory). Ange distributionsnamnet på din som en parameter. En lokal distribution använda `<your username>ConnFactoryLocal`. Skriptet skriver ut IP-adressen för den virtuella datorn.

* Leta upp resursgruppen för distributionen i Azure-portalen. Förutom en lokal distribution har resursgruppen namn som du angav som lösning eller distribution. För en lokal distribution med skriptet build, namnet på resursgruppen är `<your username>ConnFactoryLocal`. Nu ta bort den **offentliga IP-adressen** resurs från resursgruppen.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hur loggar jag in till simuleringen VM?

Logga in på simuleringen VM stöds endast om du har distribuerat din lösning med hjälp av PowerShell-skriptet `build.ps1` i den [databasen](https://github.com/Azure/azure-iot-connected-factory).

Om du har distribuerat en lösning från www.azureiotsolutions.com kan du logga in på den virtuella datorn. Du kan inte logga in, eftersom lösenordet genereras slumpmässigt och du kan inte återställa den.

1. Lägg till en offentlig IP-adress till den virtuella datorn. Se [hur lägger jag till en offentlig IP-adress i simuleringen VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Skapa en SSH-session till den virtuella datorn med IP-adressen för den virtuella datorn.
1. Användarnamnet som ska användas är: `docker`.
1. Lösenordet som ska användas beror på vilken version som används för att distribuera:
    * Lösenordet är för lösningar som distribueras med skriptet build.ps1 före den 1 juni 2017: `Passw0rd`.
    * Lösningar som distribueras med skriptet build.ps1 efter den 1 juni 2017 kan du hitta lösenordet i den `<name of your deployment>.config.user` filen. Lösenordet lagras i den **VmAdminPassword** inställningen. Lösenordet genereras slumpmässigt vid tidpunkten för distribution om du inte anger den med hjälp av den `build.ps1` skript parametern `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hur jag för att stoppa och starta alla docker processer i simuleringen VM?

1. Logga in på simuleringen VM. Se [hur loggar jag in till simuleringen VM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Om du vill kontrollera vilka behållare är aktiva, kör: `docker ps`.
1. Om du vill stoppa alla simuleringen behållare, kör: `./stopsimulation`.
1. Så här startar alla simuleringen behållare:
    * Exportera en shell variabel med namnet **IOTHUB_CONNECTIONSTRING**. Använd värdet för den **IotHubOwnerConnectionString** i den `<name of your deployment>.config.user` filen. Exempel:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Kör `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hur uppdaterar simuleringen i den virtuella datorn?

Om du har gjort några ändringar i simuleringen, kan du använda PowerShell-skriptet `build.ps1` i den [databasen](https://github.com/Azure/azure-iot-connected-factory) med hjälp av den `updatedimulation` kommando. Det här skriptet skapar simuleringen-komponenter, stoppar simuleringen i den virtuella datorn, överför, installerar och startar dem.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hur tar jag reda anslutningssträngen för IoT-hubb som används av min-lösning?

Om du har distribuerat en lösning med den `build.ps1` skript i den [databasen](https://github.com/Azure/azure-iot-connected-factory), anslutningssträngen är värdet för **IotHubOwnerConnectionString** i den `<name of your deployment>.config.user` filen.

Du kan också hitta anslutningssträngen med Azure-portalen. Leta upp strängen anslutningsinställningarna i IoT-hubb-resurs i resursgruppen för din distribution.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Vilka IoT Hub-enheter använder anslutna Factory simuleringen?

Simuleringen self registrerar följande enheter:

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

Med hjälp av den [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) eller [IoT-tillägget för Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) verktyg, men du kan kontrollera vilka enheter som registreras med IoT-hubben med hjälp av din lösning. Om du vill använda enheten explorer, behöver du anslutningssträngen för IoT-hubben i distributionen. Om du vill använda IoT-tillägg för Azure CLI 2.0 måste IoT Hub-namn.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hur kan jag loggdata från simuleringen komponenter?

Alla komponenter i simuleringen logga information i till loggfiler. Dessa filer kan hittas i den virtuella datorn i mappen `home/docker/Logs`. Du kan använda PowerShell-skript för att hämta loggarna `Simulation/Factory/Get-SimulationLogs.ps1` i den [databasen](https://github.com/Azure/azure-iot-connected-factory).

Det här skriptet måste logga in på den virtuella datorn. Du kan behöva ange autentiseringsuppgifter för inloggning. Se [hur loggar jag in till simuleringen VM?](#how-do-i-sign-in-to-the-simulation-vm) att hitta autentiseringsuppgifter.

Skriptet lägger till/tar bort en offentlig IP-adress till den virtuella datorn, om det ännu inte har något och tar bort den. Skriptet placerar alla loggfiler i ett Arkiv och hämtar arkivet på utvecklingsdatorn.

Du kan också logga in på den virtuella datorn via SSH och granska loggfilerna vid körning.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hur kan jag kontrollera om simuleringen skickar data till molnet?

Med den [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) eller [iothub explorer](https://github.com/azure/iothub-explorer) verktyg, kan du granska data som skickas till IoT-hubb från vissa enheter. Om du vill använda dessa verktyg som du behöver veta anslutningssträngen för IoT-hubben i distributionen. Se [hur tar jag reda anslutningssträngen för IoT-hubb som används av min-lösning?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Granska de data som skickas av en enhet som utgivare:

* publisher.beijing.corp.contoso
* Publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* Publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Om du ser inga data skickas till IoT-hubben är ett problem med simuleringen. Som ett första steg analys bör du analysera loggfiler simuleringen komponenter. Se [hur kan jag loggdata från simuleringen komponenterna?](#how-can-i-get-log-data-from-the-simulation-components) Försök sedan att stoppa och starta simuleringen och om det finns fortfarande inga data skickas, uppdatera simuleringen helt. Se [hur uppdaterar jag simuleringen i den virtuella datorn?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hur aktiverar en interaktiv karta i min anslutna Factory-lösning?

Om du vill aktivera en interaktiv karta i din lösning för anslutna fabriken, måste du ha en befintlig Bing Maps API för Enterprise-plan.

När du distribuerar från [www.azureiotsolutions.com](http://www.azureiotsolutions.com), distributionsprocessen verifierar att prenumerationen har en aktiverad Bing Maps API för företagsplan och distribuerar automatiskt en interaktiv karta till anslutna fabriken. Om detta inte är fallet kan du fortfarande aktivera en interaktiv karta i distributionen på följande sätt:

När du distribuerar med den `build.ps1` skriptet i den anslutna Factory GitHub-lagringsplats och du har en Bing Maps API för företagsplan genom att ange miljövariabeln `$env:MapApiQueryKey` i fönstret build till Frågenyckeln för planen. Interaktiv karta aktiveras sedan automatiskt.

Om du inte har en Bing Maps API för företagsplan distribuera lösningen för anslutna Factory från [www.azureiotsolutions.com](http://www.azureiotsolutions.com) eller med hjälp av den `build.ps1` skript. Lägg sedan till en Bing Maps API för företagsplan till din prenumeration enligt beskrivningen i [hur skapar jag en Bing Maps API för företag-konto?](#how-do-i-create-a-bing-maps-api-for-enterprise-account). Leta upp Frågenyckeln för det här kontot som beskrivs i [skaffa Bing Maps API för Enterprise QueryKey](#how-to-obtain-your-bing-maps-api-for-enterprise-querykey) och spara den här nyckeln. Gå till Azure-portalen och komma åt resursen Apptjänst i distributionen anslutna fabriken. Gå till **programinställningar**, där du hittar ett avsnitt **appinställningar**. Ange den **MapApiQueryKey** i frågan nyckeln som du fick. Spara inställningarna och gå sedan till **översikt** och starta om tjänsten App.

### <a name="how-do-i-create-a-bing-maps-api-for-enterprise-account"></a>Hur skapar jag en Bing Maps API för företag-konto

Du kan få ett kostnadsfritt *interna transaktioner nivå 1 Bing Maps för Enterprise* plan. Men du kan bara lägga till två energischeman till en Azure-prenumeration. Om du inte har en Bing Maps API för företag-konto kan du skapa en i Azure-portalen genom att klicka på **+ skapa en resurs för**. Sök sedan efter **Bing Maps API för företag** och följ anvisningarna för att skapa den.

![Bing-nyckel](./media/iot-accelerators-faq-cf/bing.png)

### <a name="how-to-obtain-your-bing-maps-api-for-enterprise-querykey"></a>Skaffa Bing Maps API för Enterprise QueryKey

När du har skapat din Bing Maps API för företagsplan, lägga till en Bing Maps för företagsresurs i resursgruppen för lösningen anslutna Factory i Azure-portalen.

1. Navigera till den resursgrupp som innehåller dina Bing Maps API för företagsplan i Azure-portalen.

1. Klicka på **alla inställningar**, sedan **nyckelhantering**.

1. Det finns två nycklar: **MasterKey** och **QueryKey**. Kopiera den **QueryKey** värde.

1. Den nyckel som hämtas av den `build.ps1` skript kan du ange miljövariabeln `$env:MapApiQueryKey` i PowerShell-miljö för att den **QueryKey** av planen. Build-skriptet sedan automatiskt lägger till värdet inställningarna för App Service.

1. Kör en lokal eller moln distribution med den `build.ps1` skript.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hur aktiverar interaktiv karta när du felsöker lokalt?

Ange värdet för inställningen för att aktivera interaktiv karta när du felsöker lokalt, `MapApiQueryKey` i filer `local.user.config` och `<yourdeploymentname>.user.config` i roten för din distribution till värdet för den **QueryKey** du kopierade tidigare.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hur använder jag en annan bild på startsidan för min instrumentpanel

Om du vill ändra statiska bilden visas i/o startsidan på instrumentpanelen ersätter bilden `WebApp\Content\img\world.jpg`. Sedan återskapa och distribuera WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hur använder icke OPC UA enheter med anslutna Factory?

Skicka telemetridata från icke OPC UA enheter till fabriksinställningarna ansluten:

1. [Konfigurera en ny station i topologin anslutna Factory](iot-accelerators-connected-factory-configure.md) i den `ContosoTopologyDescription.json` filen.

1. Mata in telemetridata i ansluten Factory kompatibel JSON-format:

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

1. Starta om Apptjänst anslutna fabriken.

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Anslutna Factory solution accelerator översikt](iot-accelerators-connected-factory-overview.md)
* [IoT-säkerhet från grunden](securing-iot-ground-up.md)
