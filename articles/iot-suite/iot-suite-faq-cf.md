---
title: "Ansluten factory lösning vanliga frågor och svar – Azure | Microsoft Docs"
description: "Vanliga frågor och svar för IoT Suite anslutna factory"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: corywink
ms.openlocfilehash: d4cb452b34ddefc70dc1adcff0e5fead072aa16a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>Vanliga frågor och svar för IoT Suite anslutna factory förkonfigurerade lösningen

Se även allmänna [vanliga frågor och svar](iot-suite-faq.md) för IoT Suite.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>Var hittar källkoden för den förkonfigurerade lösningen?

Källkoden lagras i följande GitHub-lagringsplatsen:

* [Anslutna factory förkonfigurerade lösningen](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Vad är OPC UA?

OPC enhetlig arkitektur (UA), utgivet 2008, är en plattformsoberoende, tjänstorienterad samverkan som standard. OPC UA används av olika industriella datorer och enheter, till exempel branschen datorer, sensorer och PLCs. OPC UA integrerar funktionerna i de klassiska OPC specifikationerna i ett utökningsbart ramverk med inbyggd säkerhet. Det är en standard som styrs av OPC Foundation. Den [OPC Foundation](http://opcfoundation.org/) är en icke-kommersiell organisation med mer än 440 medlemmar. Målet för organisationen är att använda OPC specifikationer för att underlätta flera leverantörer, flera plattformar, säkert och tillförlitligt interoperabilitet via:

* Infrastruktur
* Specifikationer
* Teknologi
* Processer

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Varför Microsoft välja OPC UA för anslutna factory förkonfigurerade lösningen?

Microsoft har valt OPC UA eftersom det är en öppen plattform för icke-generiska, oberoende branschen identifieras och beprövade standard. Det är ett krav för att säkerställa samverkan mellan en bred uppsättning tillverkningsprocesser och utrustning Industrie 4.0 (RAMI4.0) referens arkitektur lösningar. Microsoft ser begäran från våra kunder att skapa Industrie 4.0 lösningar. Stöd för OPC UA hjälper dig att minska barriären för kunder att nå sina mål och ger omedelbar affärsvärde dem.

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

Om du har distribuerat en lösning från www.azureiotsuite.com kan du logga in på den virtuella datorn. Du kan inte logga in, eftersom lösenordet genereras slumpmässigt och du kan inte återställa den.

1. Lägg till en offentlig IP-adress till den virtuella datorn. Se [hur lägger jag till en offentlig IP-adress i simuleringen VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Skapa en SSH-session till den virtuella datorn med IP-adressen för den virtuella datorn.
1. Användarnamnet som ska användas är: `docker`.
1. Lösenordet som ska användas beror på vilken version som används för att distribuera:
    * Lösenordet är för lösningar som distribueras med skriptet build.ps1 före den 1 juni 2017: `Passw0rd`.
    * Lösningar som distribueras med skriptet build.ps1 efter den 1 juni 2017 kan du hitta lösenordet i den `<name of your deployment>.config.user` filen. Lösenordet lagras i den **VmAdminPassword** inställningen. Lösenordet genereras slumpmässigt vid tidpunkten för distribution om du inte anger den med hjälp av den `build.ps1` skript parametern`-VmAdminPassword`

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

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Vilka IoT Hub-enheter använder anslutna factory simuleringen?

Simuleringen self registrerar följande enheter:

* proxy.Beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.Mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.Rio.corp.contoso
* proxy.Seattle.corp.contoso
* Publisher.Beijing.corp.contoso
* Publisher.capetown.corp.contoso
* Publisher.Mumbai.corp.contoso
* Publisher.munich0.corp.contoso
* Publisher.Rio.corp.contoso
* Publisher.Seattle.corp.contoso

Med hjälp av den [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) eller [iothub explorer](https://github.com/azure/iothub-explorer) verktyg, men du kan kontrollera vilka enheter som registreras med IoT-hubben med hjälp av din lösning. Om du vill använda dessa verktyg behöver du anslutningssträngen för IoT-hubben i distributionen.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hur kan jag loggdata från simuleringen komponenter?

Alla komponenter i simuleringen logga information i till loggfiler. Dessa filer kan hittas i den virtuella datorn i mappen `home/docker/Logs`. Du kan använda PowerShell-skript för att hämta loggarna `Simulation/Factory/Get-SimulationLogs.ps1` i den [databasen](https://github.com/Azure/azure-iot-connected-factory).

Det här skriptet måste logga in på den virtuella datorn. Du kan behöva ange autentiseringsuppgifter för inloggning. Se [hur loggar jag in till simuleringen VM?](#how-do-i-sign-in-to-the-simulation-vm) att hitta autentiseringsuppgifter.

Skriptet lägger till/tar bort en offentlig IP-adress till den virtuella datorn, om det ännu inte har något och tar bort den. Skriptet placerar alla loggfiler i ett Arkiv och hämtar arkivet på utvecklingsdatorn.

Du kan också logga in på den virtuella datorn via SSH och granska loggfilerna vid körning.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hur kan jag kontrollera om simuleringen skickar data till molnet?

Med den [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) eller [iothub explorer](https://github.com/azure/iothub-explorer) verktyg, kan du granska data som skickas till IoT-hubb från vissa enheter. Om du vill använda dessa verktyg som du behöver veta anslutningssträngen för IoT-hubben i distributionen. Se [hur tar jag reda anslutningssträngen för IoT-hubb som används av min-lösning?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Granska de data som skickas av en enhet som utgivare:

* Publisher.Beijing.corp.contoso
* Publisher.capetown.corp.contoso
* Publisher.Mumbai.corp.contoso
* Publisher.munich0.corp.contoso
* Publisher.Rio.corp.contoso
* Publisher.Seattle.corp.contoso

Om du ser inga data skickas till IoT-hubben är ett problem med simuleringen. Som ett första steg analys bör du analysera loggfiler simuleringen komponenter. Se [hur kan jag loggdata från simuleringen komponenterna?](#how-can-i-get-log-data-from-the-simulation-components) Försök sedan att stoppa och starta simuleringen och om det finns fortfarande inga data skickas, uppdatera simuleringen helt. Se [hur uppdaterar jag simuleringen i den virtuella datorn?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="next-steps"></a>Nästa steg

Du kan även utforska några andra funktioner och möjligheter i de förkonfigurerade lösningarna i IoT Suite:

* [Förutsägande Underhåll förkonfigurerade lösning: översikt](iot-suite-predictive-overview.md)
* [Anslutna factory förkonfigurerade lösning: översikt](iot-suite-connected-factory-overview.md)
* [IoT-säkerhet från grunden](securing-iot-ground-up.md)