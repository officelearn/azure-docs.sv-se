---
title: Arbetsflöde för Windows Azure VM-arkitektur | Microsoft-dokument
description: Den här artikeln innehåller en översikt över arbetsflödesprocesserna när du distribuerar en tjänst.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162151"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Arbetsflöde för Windows Azure klassisk VM-arkitektur 
Den här artikeln innehåller en översikt över de arbetsflödesprocesser som uppstår när du distribuerar eller uppdaterar en Azure-resurs, till exempel en virtuell dator. 

> [!NOTE]
>Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: Resource Manager och classic. Den här artikeln beskriver den klassiska distributionsmodellen.

I följande diagram visas arkitekturen för Azure-resurser.

![Azure-arbetsflöde](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Grundläggande om arbetsflöde
   
**A**. RDFE / FFE är kommunikationsvägen från användaren till tyget. RDFE (RedDog Front End) är det offentligt exponerade API:et som är klientdelen till hanteringsportalen och servicehanterings-API:et som Visual Studio, Azure MMC och så vidare.  Alla förfrågningar från användaren går via RDFE. FFE (Fabric Front End) är det lager som översätter begäranden från RDFE till infrastrukturkommandon. Alla förfrågningar från RDFE går igenom FFE för att nå tygstyrenheterna.

**B.** Tygstyrenheten ansvarar för att underhålla och övervaka alla resurser i datacentret. Den kommunicerar med infrastrukturvärdagenter på fabric OS-sändningsinformation som Gäst-OS-versionen, servicepaketet, tjänstkonfigurationen och tjänsttillståndet.

**C**. Värdagenten bor på värdoperativsystemet och ansvarar för att konfigurera Gäst-OS och kommunicera med Gästagent (WindowsAzureGuestAgent) för att uppdatera rollen mot ett avsett måltillstånd och göra pulskontroller med gästagenten. Om värdagenten inte får pulsslag på 10 minuter startar värdagenten om gästoperativsystemet.

**C2**. WaAppAgent ansvarar för att installera, konfigurera och uppdatera WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent ansvarar för följande:

1. Konfigurera gästoperativsystemet inklusive brandvägg, ACL: er, LocalStorage-resurser, servicepaket och konfiguration samt certifikat.
2. Ställa in SID för användarkontot som rollen ska köras under.
3. Kommunicera rollstatus till tyget.
4. Starta WaHostBootstrapper och övervaka den för att se till att rollen är i måltillstånd.

**E**. WaHostBootstrapper ansvarar för:

1. Läsa rollkonfigurationen och starta alla lämpliga uppgifter och processer för att konfigurera och köra rollen.
2. Övervaka alla dess underordnade processer.
3. Höja statuscheck-händelsen på rollvärdprocessen.

**F**. IISConfigurator körs om rollen är konfigurerad som en fullständig IIS-webbroll (den körs inte för SDK 1.2 HWC-roller). Den ansvarar för:

1. Starta standard-IIS-tjänster
2. Konfigurera omskrivningsmodulen i webbkonfigurationen
3. Konfigurera AppPool för den konfigurerade rollen i tjänstmodellen
4. Konfigurera IIS-loggning så att den pekar på mappen DiagnosticStore LocalStorage
5. Konfigurera behörigheter och ACL:er
6. Webbplatsen finns i %roleroot%:\sitesroot\0 och AppPool pekar på den här platsen för att köra IIS. 

**G**. Startuppgifter definieras av förebilden och startas av WaHostBootstrapper. Startuppgifter kan konfigureras för att köras i bakgrunden asynkront, och värden bootstrapper startar startaktiviteten och fortsätter sedan till andra startuppgifter. Startaktiviteter kan också konfigureras för att köras i enkelt (standardläge) där värden bootstrapper väntar på att startaktiviteten ska slutföras och returnera en lyckad (0) avslutningskod innan du fortsätter till nästa startuppgift.

**H.** Dessa uppgifter är en del av SDK och definieras som plugins i rollens tjänstdefinition (.csdef). När de expanderas till startuppgifter är **DiagnosticsAgent** och **RemoteAccessAgent** unika genom att de var och en definierar två startuppgifter, en vanlig och en som har parametern **/blockStartup.** Den normala startaktiviteten definieras som en startaktivitet i bakgrunden så att den kan köras i bakgrunden medan själva rollen körs. Startaktiviteten **/blockStartup** definieras som en enkel startuppgift så att WaHostBootstrapper väntar på att den ska avslutas innan den fortsätter. **/blockStartup-aktiviteten** väntar på att den vanliga aktiviteten ska slutföras och avslutas och sedan avslutas och värd bootstrapper kan fortsätta. Detta görs så att diagnostik och RDP-åtkomst kan konfigureras innan rollprocesserna startar (detta görs via aktiviteten /blockStartup). Detta gör det också möjligt för diagnostik och RDP-åtkomst att fortsätta köras efter att värden bootstrapper har slutfört startuppgifterna (detta görs via aktiviteten Normal).

**Jag.** WaWorkerHost är standardvärdprocessen för normala arbetsroller. Den här värdprocessen är värd för alla rolls DLL-filer och startpunktskod, till exempel OnStart och Run.

**J**. WaWebHost är standardvärdprocessen för webbroller om de är konfigurerade för att använda HWC (SDK 1.2-kompatibelt värdbart webb core). Roller kan aktivera HWC-läget genom att ta bort elementet från tjänstdefinitionen (.csdef). I det här läget körs alla tjänstens kod och DLL:er från WaWebHost-processen. IIS (w3wp) används inte och det finns inga AppPools som konfigurerats i IIS-hanteraren eftersom IIS finns i WaWebHost.exe.

**K**. WaIISHost är värdprocessen för rollstartpunktskod för webbroller som använder Fullständig IIS. Den här processen läser in den första DLL som hittas som använder **klassen RoleEntryPoint** och kör koden från den här klassen (OnStart, Run, OnStop). Alla **RollMiljöhändelser** (till exempel StatusCheck och Changed) som skapas i klassen RoleEntryPoint utlöses i den här processen.

**L.** W3WP är standard-IIS-arbetsprocessen som används om rollen är konfigurerad för att använda Full IIS. Detta kör AppPool som har konfigurerats från IISConfigurator. Alla RollMiljöhändelser (till exempel StatusCheck och Changed) som skapas här tas upp i den här processen. Observera att RollMiljöhändelser kommer att starta på båda platserna (WaIISHost och w3wp.exe) om du prenumererar på händelser i båda processerna.

## <a name="workflow-processes"></a>Arbetsflödesprocesser

1. En användare gör en begäran, till exempel ladda upp ".cspkg" och ".cscfg" filer, berättar en resurs för att stoppa eller göra en konfigurationsändring, och så vidare. Detta kan göras via Azure-portalen eller ett verktyg som använder API:et för tjänsthantering, till exempel visual studiopubliceringsfunktionen. Den här begäran går till RDFE för att utföra allt prenumerationsrelaterat arbete och sedan meddela begäran till FFE. Resten av dessa arbetsflödessteg är att distribuera ett nytt paket och starta det.
2. FFE hittar rätt maskinpool (baserat på kundinmatning, till exempel tillhörighetsgrupp eller geografisk plats plus indata från tyget, till exempel maskintillgänglighet) och kommunicerar med huvudtygstyrenheten i den maskinpoolen.
3. Tygstyrenheten hittar en värd som har tillgängliga CPU-kärnor (eller snurrar upp en ny värd). Tjänstpaketet och konfigurationen kopieras till värden och infrastrukturstyrenheten kommunicerar med värdagenten på värdoperativsystemet för att distribuera paketet (konfigurera DIPs, portar, gäst-OS och så vidare).
4. Värdagenten startar gästoperativsystemet och kommunicerar med gästagenten (WindowsAzureGuestAgent). Värden skickar pulsslag till gästen för att se till att rollen arbetar mot målläget.
5. WindowsAzureGuestAgent ställer in gäst-OS (brandvägg, ACL, LocalStorage och så vidare), kopierar en ny XML-konfigurationsfil till c:\Config och startar sedan WaHostBootstrapper-processen.
6. För fullständiga IIS-webbroller startar WaHostBootstrapper IISConfigurator och talar om för den att ta bort alla befintliga AppPools för webbrollen från IIS.
7. WaHostBootstrapper läser **startaktiviteterna** från E:\RoleModel.xml och börjar köra startuppgifter. WaHostBootstrapper väntar tills alla enkla startuppgifter har slutförts och returnerat ett "framgångsmeddelande".
8. För fullständiga IIS-webbroller talar WaHostBootstrapper om att IISConfigurator ska `E:\Sitesroot\<index>`konfigurera `<index>` IIS AppPool och pekar `<Sites>` platsen på , där är ett 0-baserat index i antalet element som definierats för tjänsten.
9. WaHostBootstrapper startar värdprocessen beroende på rolltyp:
    1. **Arbetarroll:** WaWorkerHost.exe startas. WaHostBootstrapper kör metoden OnStart(). När den har returnerats börjar WaHostBootstrapper köra metoden Run() och markerar sedan rollen som klar samtidigt och placerar den i belastningsutjämningsrotationen (om InputEndpoints har definierats). WaHostBootsrapper går sedan in i en slinga för att kontrollera rollstatus.
    1. **SDK 1,2 HWC Webbroll:** WaWebHost startas. WaHostBootstrapper kör metoden OnStart(). När den har returnerats börjar WaHostBootstrapper köra metoden Run() och markerar sedan rollen som klar samtidigt och placerar den i belastningsutjämnadens rotation. WaWebHost utfärdar en uppvärmningsbegäran (GET /do.rd_runtime_init). Alla webbförfrågningar skickas till WaWebHost.exe. WaHostBootsrapper går sedan in i en slinga för att kontrollera rollstatus.
    1. **Fullständig IIS-webbroll:** AIISHost startas. WaHostBootstrapper kör metoden OnStart(). När den har returnerats börjar den köra metoden Run() och markerar sedan rollen som klar samtidigt och placeras i belastningsutjämnadrotationen. WaHostBootsrapper går sedan in i en slinga för att kontrollera rollstatus.
10. Inkommande webbbegäranden till en fullständig IIS-webbroll utlöser IIS för att starta W3WP-processen och betjäna begäran, på samma sätt som i en lokal IIS-miljö.

## <a name="log-file-locations"></a>Logga filplatser

**WindowsAzureGuestAgent**

- C:\Loggar\AppAgentRuntime.Log.  
Den här loggen innehåller ändringar i tjänsten inklusive starter, stopp och nya konfigurationer. Om tjänsten inte ändras kan du förvänta dig att se stora tidsluckor i den här loggfilen.
- C:\Loggar\WaAppAgent.Log.  
Den här loggen innehåller statusuppdateringar och pulsslagsmeddelanden och uppdateras var 2–3:e sekund.  Den här loggen innehåller en historisk vy över instansens status och talar om för dig när instansen inte var i läget Klar.
 
**WaHostBootstrapper (en)**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost (på nytt)**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaIISHost (på nytt)**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS-loggar**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows-händelseloggar**

`D:\Windows\System32\Winevt\Logs`
 



