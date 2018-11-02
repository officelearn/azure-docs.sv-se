---
title: Vanliga frågor och svar för programprestanda för Azure web apps | Microsoft Docs
description: Få svar på vanliga frågor om tillgänglighet, prestanda och programfel i funktionen Web Apps i Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 00563b93a3601ba6299c479c49375fd252841f79
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748127"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Vanliga frågor och svar för programprestanda för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om problem med programprestanda för den [Web Apps-funktionen i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Varför är min app långsamma?

Flera faktorer kan bidra med långsamma prestanda. Detaljerad felsökning finns i [Felsök prestanda för långsamma webbappar](app-service-web-troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hur felsöker jag ett scenario med hög CPU-förbrukning?

Din app i vissa situationer med hög CPU-förbrukning kan verkligen kräva mer datorresurser. I sådana fall kan du skala till en högre tjänstnivå så att programmet hämtar alla resurser som behövs. Andra gånger kan hög CPU-förbrukning orsakas av en felaktig loop eller av en kodning idé. Få inblick i vad utlösa ökade CPU-förbrukning är en process i två delar. Först skapa en process-dump, och analysera processen dumpen. Mer information finns i [samla in och analysera den här filen för hög CPU-förbrukning för Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hur felsöker jag ett scenario med hög minnesförbrukning?

Din app i vissa scenarier med hög minnesförbrukning kan verkligen kräva mer datorresurser. I sådana fall kan du skala till en högre tjänstnivå så att programmet hämtar alla resurser som behövs. Andra gånger, kan en bugg i koden orsaka en minnesläcka. En kodning idé kan också öka minnesanvändningen. Få inblick i vad utlösa extra minne förbrukningen är en process i två delar. Först skapa en process-dump, och analysera processen dumpen. Krascher Diagnoser från galleriet Azure-tillägget kan effektivt utföra båda dessa steg. Mer information finns i [samla in och analysera den här filen för tillfälligt hög minne för Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hur jag för att automatisera App Service-webbappar med hjälp av PowerShell?

Du kan använda PowerShell-cmdletar för att hantera och underhålla App Service web apps. I vårt blogginlägg [automatisera webbappar som körs i Azure App Service med hjälp av PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), beskriver vi hur du använder Azure Resource Manager-baserade PowerShell-cmdletar för att automatisera vanliga uppgifter. Det här blogginlägget har även exempelkod för olika hanteringsuppgifter för web apps. Beskrivningar och syntax för alla App Service web apps-cmdletar finns i [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hur gör jag för att visa händelseloggar för min webbapp?

Visa din webbapps händelseloggar:

1. Logga in på din [Kudu-webbplatsen](https://*yourwebsitename*.scm.azurewebsites.net).
2. I menyn, Välj **Felsökningskonsolen** > **CMD**.
3. Välj den **LogFiles** mapp.
4. Om du vill visa händelseloggar väljer du pennikonen bredvid **eventlog.xml**.
5. Kör PowerShell-cmdlet för att hämta loggarna `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hur jag för att avbilda en användarläge minnesdump av min webbapp?

Att samla in en minnesdump för användarläge på din webbapp:

1. Logga in på din [Kudu-webbplatsen](https://*yourwebsitename*.scm.azurewebsites.net).
2. Välj den **Processutforskaren** menyn.
3. Högerklicka på den **w3wp.exe** eller WebJob-processen.
4. Välj **hämta minnesdump** > **fullständig Dump**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hur visar jag processnivå information för min webbapp?

Har du två alternativ för att visa processnivå information för din webbapp:

*   På Azure Portal:
    1. Öppna den **Processutforskaren** för webbappen.
    2. Om du vill se information, Välj den **w3wp.exe** processen.
*   I Kudu-konsolen:
    1. Logga in på din [Kudu-webbplatsen](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Välj den **Processutforskaren** menyn.
    3. För den **w3wp.exe** process, väljer **egenskaper**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>När jag söker till min app, se ”fel 403 – den här webbappen har stoppats”. Hur löser jag det?

Tre villkor kan orsaka detta fel:

* Webbappen har nått faktureringsgränsen och webbplatsen har inaktiverats.
* Webbappen har stoppats i portalen.
* Webbappen har nått resurskvotgränsen som kan gälla för en kostnadsfri eller delad skala service-plan.

Vad som orsakar felet och för att lösa problemet, följer du stegen i [Web Apps: ”fel 403 – den här webbappen har stoppats”](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Var kan jag läsa mer om kvoter och begränsningar för olika App Service-planer?

Information om kvoter och begränsningar finns i [gränser för Apptjänst](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hur jag för att minska svarstiden för den första begäran efter hur lång tids inaktivitet?

Som standard inaktiveras webbappar om de är inaktiva för en angiven tidsperiod. På så sätt kan systemet kan spara resurser. Nackdelen är att svaret på den första begäran när webbappen har bort från minnet är längre, så att appen att läsa in och starta man betjänar svar. I Basic och Standard service-planer, du kan aktivera den **Always On** inställningen för att förhindra att appen alltid läses in. Detta eliminerar längre snabbare när appen är inaktiv. Ändra den **Always On** inställningen:

1. Gå till din webbapp i Azure-portalen.
2. Välj **programinställningar**.
3. För **Always On**väljer **på**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hur aktiverar jag spårning av misslyckade begäranden?

Aktivera spårning av misslyckade begäranden:

1. Gå till din webbapp i Azure-portalen.
3. Välj **alla inställningar** > **diagnostikloggar**.
4. För **spårning av misslyckade begäranden**väljer **på**.
5. Välj **Spara**.
6. På webbappsbladet, väljer **verktyg**.
7. Välj **Visual Studio Online**.
8. Om inställningen inte är **på**väljer **på**.
9. Välj **Gå**.
10. Välj **Web.config**.
11. I system.webServer, lägger du till den här konfigurationen (om du vill spela in en specifik URL):

    ```
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
12. Lägg till den här konfigurationen för att felsöka problem med långsam prestanda (om in begäran tar mer än 30 sekunder):
    ```
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
13. Ladda ned spårningarna av misslyckade begäranden, i den [portal](https://portal.azure.com)går du till din webbplats.
15. Välj **verktyg** > **Kudu** > **Gå**.
18. I menyn, Välj **Felsökningskonsolen** > **CMD**.
19. Välj den **LogFiles** mapp och välj sedan mappen med det namn som börjar med **W3SVC**.
20. Välj pennikonen för att se XML-filen.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Jag ser meddelandet ”arbetsprocess begärt återanvändning på grund av” procent ' minnesgräns ”. Hur jag för att lösa det här problemet?

Högsta tillgängliga mängden minne för en 32-bitars process (även på ett 64-bitars operativsystem) är 2 GB. Arbetsprocessen är som standard till 32-bitars i App Service (för kompatibilitet med äldre webbprogram).

Överväg att byta till 64-bitars processer så att du kan dra nytta av det extra minnet tillgängligt i din Web Worker-roll. Detta utlöser en omstart av web app, så du bör schemalägga därefter.

Observera också att en 64-bitars-miljö kräver en Basic eller Standard service-plan. Kostnadsfri och delad planer körs alltid i en 32-bitars-miljö.

Mer information finns i [konfigurera webbappar i App Service](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Varför har min timeout för begäran efter 230 sekunder?

Azure Load Balancer har en standardinställning för timeout för inaktivitet på fyra minuter. Det är vanligtvis en rimlig svar tidsgräns för en webbegäran. Om din webbapp kräver behandling i bakgrunden, bör du använda Azure WebJobs. Azure-webbappen kan anropa WebJobs och bli meddelad när behandling i bakgrunden är klar. Du kan välja mellan flera metoder för att använda WebJobs, inklusive köer och utlösare.

WebJobs är utformat för bearbetning i bakgrunden. Du kan göra så mycket bakgrunden som bearbetar som du vill ha i ett WebJob. Läs mer om WebJobs [kör bakgrundsuppgifter med WebJobs](web-sites-create-web-jobs.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core-program som finns i App Service ibland slutar svara. Hur kan jag åtgärda det här problemet?

Ett känt problem med en tidigare [Kestrel version](https://github.com/aspnet/KestrelHttpServer/issues/1182) kan orsaka en ASP.NET Core 1.0-app som finns i App Service periodvis slutar svara. Du kan också se meddelandet: ”ett fel inträffade i CGI-programmet och servern avslutades processen”.

Det här problemet löses i Kestrel version 1.0.2. Den här versionen ingår i ASP.NET Core 1.0.3 uppdateringen. Lös problemet genom att kontrollera att du uppdaterar din appberoenden för att använda Kestrel 1.0.2. Du kan också använda en av två lösningar som beskrivs i det här blogginlägget [ASP.NET Core 1.0 långsam perf utfärdar i App Service web apps](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Jag kan inte hitta mitt loggfiler i filstrukturen för min webbapp. Hur hittar jag dem?

Om du använder lokal Cache-funktionen i App Service kan påverkas mappstrukturen i mapparna LogFiles och Data för din App Service-instans. När lokal Cache används, skapas undermappar i lagring LogFiles och mappar. Undermapparna använder namngivning mönstret ”unika identifierare” + tidsstämpel. Varje undermapp motsvarar en VM-instans där webbappen körs eller har körts.

Kontrollera din App Service för att avgöra om du använder lokal Cache, **programinställningar** fliken. Om lokal Cache används, appinställningen `WEBSITE_LOCAL_CACHE_OPTION` är inställd på `Always`.

Om du inte använder lokal Cache och som har det här problemet kan du skicka en supportförfrågan.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Jag ser meddelandet ”ett försök gjordes att komma åt en socket på ett sätt som tillåts inte av åtkomstbehörigheterna”. Hur löser jag det?

Det här felet uppstår vanligen om utgående TCP-anslutningar på VM-instans är uttömd. I App Service tillämpas gränserna för det maximala antalet utgående anslutningar som kan göras för varje virtuell datorinstans. Mer information finns i [mellan VM numeriska gränser](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Det här felet kan också inträffa om du försöker komma åt en lokal adress från ditt program. Mer information finns i [lokal adressbegäranden](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Mer information om utgående anslutningar i webbappen finns i blogginlägget om [utgående anslutningar till Azure websites](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Hur använder jag Visual Studio till fjärr Felsök min App Service-webbapp?

En detaljerad genomgång som visar hur du felsöker din webbapp med Visual Studio finns i [Remote felsöka din App Service-webbapp](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
