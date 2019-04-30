---
title: Arbetsflöde för Windows Azure VM-arkitektur | Microsoft Docs
description: Den här artikeln innehåller en översikt över arbetsflödesprocesserna när du distribuerar en tjänst.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480758"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Arbetsflöde för Windows Azure klassiska VM-arkitektur 
Den här artikeln innehåller en översikt över arbetsflödesprocesser som uppstår när du skapar eller uppdaterar en Azure-resurs, till exempel en virtuell dator. 

> [!NOTE]
>Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen.

Följande diagram visar arkitekturen för Azure-resurser.

![Azure-arbetsflöde](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Grunderna i arbetsflödet
   
**EN**. RDFE / FFE är kommunikationsvägen från användaren i infrastrukturen. RDFE (RedDog Front End) är det offentliga API: T som är klientdelen till hanteringsportalen och Service Management API som Visual Studio, Azure MMC och så vidare.  Alla förfrågningar från användaren gå igenom RDFE. FFE (Fabric klientdel) är lagret som översätter begäranden från RDFE i fabric-kommandon. Alla förfrågningar från RDFE gå igenom FFE att nå domänkontrollanterna för infrastrukturen.

**B**. Infrastrukturkontrollanten ansvarar för att underhålla och övervaka alla resurser i datacentret. Den kommunicerar med fabric host-agenterna på fabric OS skicka information till som gäst-OS-version, servicepaket, tjänstkonfiguration och tjänsttillstånd.

**C**. Värdagenten ligger på värd-OSsystem och ansvarar för att ställa in gäst-OS och kommunikationen med Gästagenten (WindowsAzureGuestAgent) för att uppdatera roll mot ett avsedda målet tillstånd och gör pulsslag kontrollerar med gästagenten. Om Värdagenten inte får pulsslag svar i 10 minuter startar Värdagenten om Gästoperativsystemet.

**C2**. WaAppAgent ansvarar för att installera, konfigurera och uppdatera WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent ansvarar för följande:

1. Konfigurera gäst-OS-brandväggen, ACL: er, LocalStorage resurser, servicepaket och konfiguration och certifikat. Hur du konfigurerar SID för det användarkonto som rollen kommer att köras under.
2. Kommunikationen sker Rollstatus i infrastrukturen.
3. Startar WaHostBootstrapper och övervaka den för att se till att rollen är i tillståndet för målet.

**E**. WaHostBootstrapper ansvarar för att:

1. Läser rollkonfiguration av och startar alla lämpliga uppgifter och processer för att konfigurera och kör rollen.
2. Övervakning av alla dess underordnade processer.
3. Aktivering av StatusCheck händelsen på värdprocess för rollen.

**F**. IISConfigurator körs om rollen är konfigurerad som en fullständig IIS web-roll (det inte kan köras för SDK 1.2 programtillägget för hysningsbar Webbinstans roller). Den är ansvarig för:

1. Startar standard IIS-tjänsterna
2. Konfigurera modulen för omarbetning i web-konfiguration
3. Konfigurera AppPool för den konfigurerade rollen i tjänstemodellen
4. Konfigurera IIS-loggning så att den pekar till mappen DiagnosticStore LocalStorage
5. Konfigurera behörigheter och ACL: er
6. Webbplatsen finns i % roleroot%:\sitesroot\0 och apppool pekar på den här platsen att köra IIS. 

**G**. Startåtgärder definieras i modellen för rollen och startas av WaHostBootstrapper. Startåtgärder kan konfigureras för att köras i bakgrunden asynkront och värd-startprogram startar startåtgärden och fortsätt sedan till andra startåtgärder. Startåtgärder kan också konfigureras för att köras med enkel (standard) som värd-startprogram väntar startåtgärd för att slutföras och returnerar en slutkod för lyckade (0) innan du fortsätter till nästa startåtgärden.

**H**. Dessa uppgifter är en del av SDK: N och definieras som plugin-program i rollens tjänstdefinitionen (.csdef). När utökats till startåtgärder, den **DiagnosticsAgent** och **RemoteAccessAgent** är unika eftersom de definiera två startåtgärder, en ordinarie och som har en **/blockStartup** parametern. Normal startåtgärden definieras som en bakgrundsaktivitet Start så att den kan köras i bakgrunden medan rollen själva körs. Den **/blockStartup** startåtgärd definieras som en enkel startåtgärd så att WaHostBootstrapper väntar tills den för att avsluta innan du fortsätter. Den **/blockStartup** uppgift väntar regelbundna åtgärden har slutförts initierar och sedan avslutas och Tillåt värden startprogram att fortsätta. Det gör du så att diagnostik- och RDP-åtkomst kan konfigureras före rollen processer (detta görs via /blockStartup aktiviteten). Detta kan också diagnostik- och RDP-åtkomst kan fortsätta köras efter värd-startprogram har slutförts startåtgärder (detta görs via Normal aktivitet).

**JAG**. WaWorkerHost är standard värdprocessen för normal worker-roller. Den här värdprocess är värd för alla rollens DLL-filer och posten punkt kod, till exempel OnStart och kör.

**J**. WaWebHost är standard värdprocessen för web-roller om de är konfigurerade för att använda den SDK 1.2-kompatibla Hysningsbar Web Core (programtillägget för hysningsbar Webbinstans). Roller kan aktivera programtillägget för hysningsbar Webbinstans-läge genom att ta bort elementet från tjänstdefinitionen (.csdef). I det här läget kan köra alla service koden och DLL-filer från WaWebHost-processen. IIS (w3wp) används inte och det finns inga AppPools som konfigureras i IIS-hanteraren, eftersom IIS finns innanför WaWebHost.exe.

**K**. WaIISHost är värdprocessen för rollen post punkt koden för web-roller som använder fullständig IIS. Den här processen läser in första DLL-filen som hittas som använder den **RoleEntryPoint** klassen och kör koden från den här klassen (OnStart, kör, OnStop). Alla **RoleEnvironment** händelser (till exempel StatusCheck och ändrade) som skapas i klassen RoleEntryPoint aktiveras i den här processen.

**L**. W3wp är den standard IIS-arbetsprocess som används om rollen är konfigurerad för att använda fullständiga IIS. Den här raden kör programpoolen som konfigureras från IISConfigurator. Eventuella RoleEnvironment händelser (till exempel StatusCheck och ändrade) som skapas här aktiveras i den här processen. Observera att RoleEnvironment händelser utlöses på båda platserna (WaIISHost och w3wp.exe) om du prenumererar på händelser i båda processer.

## <a name="workflow-processes"></a>Arbetsflödesprocesser

1. En användare skickar en begäran, till exempel ladda upp .cspkg och .cscfg-filer, om en resurs för att stoppa eller gör en konfigurationsändring och så vidare. Detta kan göras via Azure-portalen eller ett verktyg som använder Service Management-API, till exempel publicera med Visual Studio-funktionen. Den här begäran går till RDFE att göra alla de prenumeration-relaterade fungerar och sedan kommunicera begäran till FFE. Resten av de här stegen är att distribuera ett nytt paket och starta den.
2. FFE hittar rätt datorpoolen (baserat på kundernas feedback sådana, i tillhörighetsgrupp eller geografisk plats och indata från infrastrukturen, till exempel datortillgänglighet) och kommunicerar med master infrastrukturkontrollanten i datorpoolen.
3. Infrastrukturkontrollanten hittar en värd som har tillgängliga CPU-kärnor (eller varv upp en ny värd). Tjänstpaket och konfiguration har kopierats till värden och infrastrukturkontrollanten kommunicerar med värdagenten på gästoperativsystem att distribuera paketet (Konfigurera dalar, portar, gästoperativsystem och så vidare).
4. Värdagenten startar gäst-OS och kommunicerar med gästagenten (WindowsAzureGuestAgent). Värden skickar pulsslag till gästen för att kontrollera att rollen fungerar mot dess målstatusen.
5. WindowsAzureGuestAgent ställer in gäst-OS (brandvägg, ACL: er, LocalStorage och så vidare), kopierar en ny XML-konfigurationsfil till c:\Config och startar sedan processen WaHostBootstrapper.
6. För fullständig IIS webbroller WaHostBootstrapper startar IISConfigurator och beordrar den att ta bort alla befintliga AppPools för webbrollen från IIS.
7. WaHostBootstrapper läser den **Start** uppgifter från E:\RoleModel.xml och börjar köras startåtgärder. WaHostBootstrapper väntar tills alla enkla startåtgärder har slutförts och returnerade ett ”lyckades”-meddelande.
8. För fullständig IIS webbroller WaHostBootstrapper talar om IISConfigurator att konfigurera IIS-programpoolen och pekar platsen till `E:\Sitesroot\<index>`, där `<index>` är en 0 baserat index för antalet <Sites> element har definierats för tjänsten.
9. WaHostBootstrapper börjar värdprocessen beroende på vilken rolltyp:
    1. **Arbetsroll**: WaWorkerHost.exe har startats. WaHostBootstrapper kör OnStart()-metoden. När den returnerar WaHostBootstrapper börjar att köra metoden Run() samtidigt markerar du rollen som redo och placerar dem i belastningsutjämnarens rotation (om InputEndpoints har definierats). WaHostBootsrapper hamnar sedan i en slinga av kontrollerar Rollstatus för.
    1. **SDK 1.2 genom hysningsbar Webbinstans Web rollen**: WaWebHost har startats. WaHostBootstrapper kör OnStart()-metoden. När den returnerar börjar att köra metoden Run() WaHostBootstrapper och markerar sedan rollen som redo samtidigt och placerar dem i belastningsutjämnarens rotation. WaWebHost skickar en begäran för värma upp (GET-/do.rd_runtime_init). Alla begäranden skickas till WaWebHost.exe. WaHostBootsrapper hamnar sedan i en slinga av kontrollerar Rollstatus för.
    1. **Fullständig IIS Web-roll**: aIISHost har startats. WaHostBootstrapper kör OnStart()-metoden. När den returnerar den börjar att köra metoden Run() och markerar samtidigt rollen som redo och placerar dem i belastningsutjämnarens rotation. WaHostBootsrapper hamnar sedan i en slinga av kontrollerar Rollstatus för.
10. Inkommande webbegäranden till en fullständig IIS web rollen utlösare IIS för att starta W3WP-processen och svara på begäran, på samma sätt som den skulle ha gjort i en lokal IIS-miljö.

## <a name="log-file-locations"></a>Loggfilens platser

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Den här loggfilen innehåller ändringar i tjänsten inklusive startar, stoppar och nya konfigurationer. Om tjänsten inte ändras, du kan förvänta dig att se stora luckor tid i den här loggfilen.
- C:\Logs\WaAppAgent.Log.  
Den här loggfilen innehåller statusuppdateringar och pulsslag meddelanden och uppdateras var 2 – 3: e sekund.  Den här loggfilen innehåller en historisk översikt över statusen för instansen och meddelar när instansen var inte i färdigt tillstånd.
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid >.<role> \WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<distributions-ID >.<role> \WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**IIS-loggar**

C:\Resources\Directory\<guid >.<role>. DiagnosticStore\LogFiles\W3SVC1
 
**Windows-händelseloggar**

D:\Windows\System32\Winevt\Logs
 



