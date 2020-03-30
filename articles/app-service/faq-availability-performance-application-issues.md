---
title: Vanliga frågor om programprestanda
description: Få svar på vanliga frågor om tillgänglighet, prestanda och programproblem i Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 433f5885c7f057226e78c4ae57e03d7619004d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259869"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Vanliga frågor om programprestanda för webbappar i Azure

> [!NOTE]
> Vissa av nedanstående riktlinjer kanske bara fungerar på Windows eller Linux App Services. Linux App Services körs till exempel i 64-bitarsläge som standard.
>

Den här artikeln innehåller svar på vanliga frågor och svar (Vanliga frågor) om problem med programmets prestanda för [funktionen Webbappar](https://azure.microsoft.com/services/app-service/web/)i Azure App Service .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Varför är min app långsam?

Flera faktorer kan bidra till att göra appen långsam. Detaljerade felsökningssteg finns i [Felsöka långsamma webbappprestanda](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hur felsöker jag ett scenario med hög processorförbrukning?

I vissa scenarier med hög processorförbrukning kan din app verkligen kräva mer datorresurser.I så fall överväg skalning till en högre tjänstnivå så att programmet får alla resurser som behövs. Andra gånger kan hög CPU-förbrukning orsakas av en felaktig loop eller av en kodningspraxis. Att få insikt i vad som utlöser ökad CPU-förbrukning är en tvådelad process. Skapa först en processdump och analysera sedan processdumpen. Mer information finns i [Fånga in och analysera en dumpfil för hög CPU-förbrukning för Webbappar](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hur felsöker jag ett scenario med hög minnesförbrukning?

I vissa scenarier med hög minnesförbrukning kan din app verkligen kräva mer datorresurser.I så fall överväg skalning till en högre tjänstnivå så att programmet får alla resurser som behövs. Andra gånger kan ett fel i koden orsaka en minnesläcka. En kodningspraxis kan också öka minnesförbrukningen.Att få insikt i vad som utlöser hög minnesförbrukning är en process i två delar. Skapa först en processdump och analysera sedan processdumpen. Crash Diagnoser från Azure Site Extension Gallery kan effektivt utföra båda dessa steg. Mer information finns i [Fånga och analysera en dumpfil för intermittent högt minne för Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hur automatiserar jag App Service-webbappar med PowerShell?

Du kan använda PowerShell-cmdletar för att hantera och underhålla App Service-webbappar. I vårt blogginlägg [Automatisera webbappar som finns i Azure App Service med hjälp av PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/)beskriver vi hur du använder Azure Resource Manager-baserade PowerShell-cmdlets för att automatisera vanliga uppgifter. Blogginlägget har också exempelkod för olika webbapphanteringsuppgifter. Beskrivningar och syntax för alla appar för App Service finns i [Az.Websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hur visar jag min webbapps händelseloggar?

Så här visar du händelseloggarna för webbappen:

1. Logga in på din [Kudu-webbplats.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Välj **Felsök konsol** > **CMD**på menyn .
3. Markera mappen **LogFiles.**
4. Om du vill visa händelseloggar väljer du pennikonen bredvid **eventlog.xml**.
5. Om du vill hämta loggarna kör `Save-AzureWebSiteLog -Name webappname`du PowerShell-cmdleten .

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hur fångar jag en minnesdump i användarläge för min webbapp?

Så här fångar du en minnesdump i användarläge för din webbapp:

1. Logga in på din [Kudu-webbplats.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Välj **menyn Process Explorer.**
3. Högerklicka på **w3wp.exe-processen** eller WebJob-processen.
4. Välj **Hämta minnesdump** > **full dump**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hur visar jag information på processnivå för min webbapp?

Du har två alternativ för att visa information på processnivå för din webbapp:

*   På Azure Portal:
    1. Öppna **Process Explorer** för webbappen.
    2. Om du vill se informationen väljer du **w3wp.exe-processen.**
*   I Kudu-konsolen:
    1. Logga in på din [Kudu-webbplats.](https://*yourwebsitename*.scm.azurewebsites.net)
    2. Välj **menyn Process Explorer.**
    3. För **w3wp.exe-processen** väljer du **Egenskaper**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>När jag bläddrar till min app ser jag "Fel 403 - Den här webbappen stoppas". Hur gör jag för att lösa det?

Tre villkor kan orsaka detta fel:

* Webbappen har nått en faktureringsgräns och din webbplats har inaktiverats.
* Webbappen har stoppats i portalen.
* Webbappen har nått en resurskvotgräns som kan gälla för en tjänstplan för kostnadsfri eller delad skala.

Följ stegen i [Webbappar](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/)för att se vad som orsakar felet och lösa problemet.

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Var kan jag läsa mer om kvoter och begränsningar för olika App Service-planer?

Information om kvoter och begränsningar finns i [Begränsningar för App-tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hur minskar jag svarstiden för den första begäran efter inaktiv tid?

Som standard tas webbappar bort om de är inaktiva under en viss tidsperiod. På så sätt kan systemet spara resurser. Nackdelen är att svaret på den första begäran efter webbappen tas bort är längre, så att webbappen kan läsa in och börja servera svar. I Grundläggande och Standard serviceplaner kan du aktivera inställningen **Alltid på** för att hålla appen alltid laddad. Detta eliminerar längre laddningstider efter att appen är inaktiv. Så här ändrar du inställningen **Alltid på:**

1. Gå till din webbapp i Azure-portalen.
2. Välj **konfiguration**
3. Välj **Allmänna inställningar**.
4. För **Alltid På**väljer du **På**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hur aktiverar jag spårning av misslyckade begäranden?

Så här aktiverar du spårning av misslyckade begäranden:

1. Gå till din webbapp i Azure-portalen.
3. Välj **Alla inställningar** > **Diagnostikloggar**.
4. För **Spårning av misslyckade begäranden**väljer du **På**.
5. Välj **Spara**.
6. Välj **Verktyg**på webbappbladet .
7. Välj **Visual Studio Online**.
8. Om inställningen inte är **På**väljer du **På**.
9. Välj **Gå**.
10. Välj **Web.config**.
11. I system.webServer lägger du till den här konfigurationen (för att samla in en specifik URL):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Om du vill felsöka problem med långsam prestanda lägger du till den här konfigurationen (om hämtningsbegäran tar mer än 30 sekunder):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Om du vill hämta spårade misslyckade begäranden går du till din webbplats i [portalen.](https://portal.azure.com)
15. Välj **Verktyg** > **Kudu** > **Go**.
18. Välj **Felsök konsol** > **CMD**på menyn .
19. Markera mappen **LogFiles** och markera sedan mappen med ett namn som börjar med **W3SVC**.
20. Om du vill visa XML-filen markerar du pennikonen.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Meddelandet "Arbetsprocess begärd återvinning på grund av gränsen för procentminne". Hur åtgärdar jag det här problemet?

Den maximala tillgängliga mängden minne för en 32-bitarsprocess (även på ett 64-bitars operativsystem) är 2 GB. Som standard är arbetsprocessen inställd på 32-bitars i App Service (för kompatibilitet med äldre webbprogram).

Överväg att byta till 64-bitarsprocesser så att du kan dra nytta av det extra minne som finns tillgängligt i webbarbetsmappen. Detta utlöser en omstart av webbappen, så schemalägg därefter.

Observera också att en 64-bitarsmiljö kräver en basic- eller standardserviceplan. Kostnadsfria och delade planer körs alltid i en 32-bitarsmiljö.

Mer information finns [i Konfigurera webbappar i App Service](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Varför tar min begäran time out efter 230 sekunder?

Azure Load Balancer har en standard inaktiv timeout-inställning på fyra minuter. Detta är i allmänhet en rimlig tidsfrist för svar för en webbbegäran. Om webbappen kräver bakgrundsbearbetning rekommenderar vi att du använder Azure WebJobs. Azure-webbappen kan anropa WebJobs och meddelas när bakgrundsbearbetningen är klar. Du kan välja mellan flera metoder för att använda WebJobs, inklusive köer och utlösare.

WebJobs är avsedd för bakgrundsbearbetning. You can do as much background processing as you want in a WebJob. Mer information om WebJobs finns i [Köra bakgrundsuppgifter med WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core-program som finns i App Service slutar ibland svara. Hur löser jag problemet?

Ett känt problem med en tidigare [Kestrel-version](https://github.com/aspnet/KestrelHttpServer/issues/1182) kan leda till att en ASP.NET Core 1.0-app som finns i App Service ibland slutar svara. Det här meddelandet kan också visas: "Det angivna CGI-programmet påträffade ett fel och servern avslutade processen."

Det här problemet är åtgärdat i Kestrel version 1.0.2. Den här versionen ingår i uppdateringen ASP.NET Core 1.0.3. Lös problemet genom att uppdatera appberoendena så att du använder Kestrel 1.0.2. Du kan också använda en av två lösningar som beskrivs i blogginlägget [ASP.NET Core 1.0 långsam perf-problem i App Service-webbappar](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Jag hittar inte mina loggfiler i webbappens filstruktur. Hur hittar jag dem?

Om du använder funktionen Lokal cache i App Service påverkas mappstrukturen för logfiles och datamappar för App Service-instansen. När lokal cache används skapas undermappar i mapparna lagringsloggfiler och data. Undermapparna använder namngivningsmönstret "unik identifierare" + tidsstämpel. Varje undermapp motsvarar en VM-instans där webbappen körs eller har körts.

Kontrollera fliken Inställningar för **apptjänstprogram** för att avgöra om du använder lokal cache. Om Lokal cache används är `WEBSITE_LOCAL_CACHE_OPTION` appinställningen `Always`inställd på .

Om du inte använder lokal cache och har det här problemet skickar du en supportbegäran.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Jag ser meddelandet "Ett försök gjordes att komma åt en socket på ett sätt som är förbjudet av dess åtkomstbehörigheter." Hur gör jag för att lösa det?

Det här felet uppstår vanligtvis om de utgående TCP-anslutningarna på VM-instansen är uttömda. I App Service tillämpas gränser för det maximala antalet utgående anslutningar som kan göras för varje VM-instans. Mer information finns i [Numeriska gränser för flera virtuella datorer](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Det här felet kan också uppstå om du försöker komma åt en lokal adress från ditt program. Mer information finns i [Lokala adressförfrågningar](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Mer information om utgående anslutningar i webbappen finns i blogginlägget om [utgående anslutningar till Azure-webbplatser](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Hur använder jag Visual Studio för att fjärrsã¶k felsöka min App Service-webbapp?

En detaljerad genomgång som visar hur du felsöker webbappen med Visual Studio finns i [Fjärrfelsöka apptjänstens webbapp](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
