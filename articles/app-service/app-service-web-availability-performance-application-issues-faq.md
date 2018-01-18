---
title: "Programprestanda vanliga frågor och svar för Azure-webbappar | Microsoft Docs"
description: "Få svar på vanliga frågor om tillgänglighet, prestanda och problem i funktionen Web Apps i Azure App Service."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 926005eace0acde27e26619a2787368ba0954351
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Programprestanda vanliga frågor och svar för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om problem med prestanda för den [funktionen Web Apps i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Varför är min långsam appen?

Flera faktorer kan bidra med långsamma prestanda. För detaljerad felsökning, se [Felsök långsam web app prestanda](app-service-web-troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hur felsöker jag en hög CPU-förbrukning scenario?

I vissa hög CPU-förbrukning, kan din app verkligen kräva mer datorresurser. I så fall bör du skalning till en högre tjänstnivå så att programmet hämtar alla resurser som behövs. Andra tider hög CPU-användning kan bero på en felaktig loop eller kodning praxis. Få inblick i vad utlösa ökade CPU-användning är en process i två steg. Först skapar en processdump och analysera processdump. Mer information finns i [samla in och analysera en dumpfil för hög CPU-användning för Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hur felsöker ett scenario med hög minnesförbrukning?

I vissa scenarier med hög minnesförbrukning kräva appen verkligen mer datorresurser. I så fall bör du skalning till en högre tjänstnivå så att programmet hämtar alla resurser som behövs. Ett fel i koden kan orsaka en minnesläcka andra tider. Kodning praxis kan också öka minnesanvändningen. Få inblick i vad utlösa höga minnesområdet förbrukningen är en process i två steg. Först skapar en processdump och analysera processdump. Krasch Diagnoser från galleriet Azure-tillägget kan effektivt utföra båda dessa steg. Mer information finns i [samla in och analysera en dumpfil för återkommande höga minnesområdet för Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hur jag för att automatisera App Service web apps med hjälp av PowerShell?

Du kan använda PowerShell-cmdlets för att hantera och underhålla App Service web apps. I vår blogginlägget [automatisera webbprogram finns i Azure App Service med hjälp av PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), vi beskriver hur du använder Azure Resource Manager-baserade PowerShell-cmdlets för att automatisera vanliga uppgifter. Blogginlägget har också exempelkod för olika hanteringsuppgifter för web apps. Beskrivningar och syntaxen för alla App Service web apps-cmdletar finns [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hur visar jag min webbprogrammet händelseloggar

Visa händelseloggar för ditt webbprogram:

1. Logga in på ditt [Kudu webbplats](https://*yourwebsitename*.scm.azurewebsites.net).
2. Välj på menyn **Felsökningskonsolen** > **CMD**.
3. Välj den **loggfiler** mapp.
4. Om du vill visa händelseloggar väljer på pennikonen bredvid **eventlog.xml**.
5. Kör PowerShell-cmdlet för att hämta loggarna `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hur jag för att avbilda en användarläge minnesdump för webbappen?

Att samla in en användarläge minnesdump av ditt webbprogram:

1. Logga in på ditt [Kudu webbplats](https://*yourwebsitename*.scm.azurewebsites.net).
2. Välj den **Processutforskaren** menyn.
3. Högerklicka på den **w3wp.exe** eller Webbjobb-processen.
4. Välj **hämta minnesdump** > **fullständig Dump**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hur visar jag processnivå info för webbappen

Har du två alternativ för att visa processnivå information för ditt webbprogram:

*   På Azure Portal:
    1. Öppna den **Processutforskaren** för webbprogrammet.
    2. Om du vill se ytterligare information, Välj den **w3wp.exe** process.
*   I konsolen Kudu:
    1. Logga in på ditt [Kudu webbplats](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Välj den **Processutforskaren** menyn.
    3. För den **w3wp.exe** process, Välj **egenskaper**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>När jag bläddrar till min app finns ”felet 403 – det här webbprogrammet har stoppats”. Hur lösa problemet?

Tre villkoren kan orsaka detta fel:

* Webbprogrammet har nått gränsen för fakturering och webbplatsen har inaktiverats.
* Webbprogrammet har stoppats på portalen.
* Webbprogrammet har nått en resursgräns kvot som kan tillämpas på en gratis eller delad skala service-plan.

Vad som orsakar felet och för att lösa problemet, följer du stegen i [Web Apps: ”fel 403 – det här webbprogrammet har stoppats”](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Var kan jag läsa mer om kvoter och gränser för olika programtjänstplaner?

Information om kvoter och begränsningar finns [gränser för Apptjänst](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hur jag för att minska svarstiden för den första begäranden efter inaktivitet?

Som standard inaktiveras webbappar om de är inaktiv under en angiven tidsperiod. På så sätt kan systemet kan spara resurser. Nackdelen är att svaret på den första begäranden när webbappen har inaktiverats längre att tillåta webbprogram för att läsa in och starta som man betjänar svar. I Basic och Standard serviceplaner kan du aktivera den **alltid på** inställningen för att hålla appen alltid läses in. Detta eliminerar längre inläsningstiden när appen är inaktiv. Så här ändrar du den **alltid på** inställningen:

1. Gå till din webbapp i Azure-portalen.
2. Välj **programinställningar**.
3. För **alltid på**väljer **på**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hur aktiverar jag spårning av misslyckade begäranden?

Om du vill aktivera spårning av misslyckade begäranden:

1. Gå till din webbapp i Azure-portalen.
3. Välj **alla inställningar** > **diagnostik loggar**.
4. För **spårning av misslyckade begäranden**väljer **på**.
5. Välj **Spara**.
6. På bladet webbapp, Välj **verktyg**.
7. Välj **Visual Studio Online**.
8. Om inställningen inte är **på**väljer **på**.
9. Välj **Gå**.
10. Välj **Web.config**.
11. System.webServer, Lägg till den här konfigurationen (för att avbilda en specifik URL):

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
12. Lägg till den här konfigurationen felsökning av problem med låga prestanda (om sparandet begäran tar mer än 30 sekunder):
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
13. Hämta misslyckade begäranden i den [portal](https://portal.azure.com), gå till din webbplats.
15. Välj **verktyg** > **Kudu** > **Gå**.
18. Välj på menyn **Felsökningskonsolen** > **CMD**.
19. Välj den **loggfiler** mapp och välj sedan mappen med ett namn som börjar med **W3SVC**.
20. Om du vill visa XML-filen, Välj på pennikonen.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Visas meddelandet ”arbetsprocess begärt återvinning på grund av ' procent” minnesgräns ”. Hur åtgärdar problemet?

Den största tillgängliga mängden minne för en 32-bitarsprocess (även på ett 64-bitars operativsystem) är 2 GB. Arbetsprocessen är som standard till 32-bitars i App Service (för kompatibilitet med äldre webbprogram).

Överväg att byta till 64-bitars processer, så du kan dra nytta av det extra minnet tillgängligt i Web Worker-rollen. Detta utlöser en web app omstart, så schemalägga därefter.

Observera också att en 64-bitars miljö kräver en serviceplan Basic eller Standard. Frigör och delade planer köras alltid i en 32-bitars-miljö.

Mer information finns i [konfigurera webbappar i App Service](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-240-seconds"></a>Varför har min begäran-timeout efter 240 sekunder?

Azure belastningsutjämnare har en standardinställning för timeout för inaktivitet fyra minuter. Detta är vanligtvis en rimlig svar tidsgräns för en webbegäran. Om ditt webbprogram kräver behandling i bakgrunden, bör du använda Azure WebJobs. Azure-webbappen kan anropa WebJobs och aviseras när behandling i bakgrunden är klar. Du kan välja mellan flera metoder för att använda WebJobs, inklusive köer och utlösare.

WebJobs är avsedd för behandling i bakgrunden. Du kan göra så mycket bakgrund bearbetning av som du vill använda i ett Webbjobb. Läs mer om WebJobs [kör bakgrundsaktiviteter med WebJobs](web-sites-create-web-jobs.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core program som finns i App Service ibland slutar svara. Hur kan jag åtgärda det här problemet?

Ett känt problem med en tidigare [Kestrel version](https://github.com/aspnet/KestrelHttpServer/issues/1182) kan orsaka en ASP.NET Core 1.0-app som finns i App Service periodvis slutar svara. Du kan också se meddelandet: ”ett fel uppstod i CGI-programmet och servern avslutades processen”.

Det här problemet löses i Kestrel version 1.0.2. Den här versionen ingår i ASP.NET Core 1.0.3 uppdateringen. Lös problemet genom att kontrollera att du uppdaterar din app beroenden för att använda Kestrel 1.0.2. Du kan också använda en av två lösningar som beskrivs i blogginlägget [ASP.NET Core 1.0 långsam prestanda problem i App Service web apps](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Jag kan inte hitta min loggfiler i webbappen filstruktur. Hur hittar jag dem?

Om du använder den lokala cachefunktionen i App Service påverkas mappstrukturen i mapparna loggfiler och Data för din App Service-instans. När lokal Cache används skapas undermappar i mappar och lagring av loggfiler. Undermapparna använder namngivning mönstret ”UID” + tidsstämpel. Varje undermapp motsvarar en VM-instans som webbappen körs eller har körts.

Ta reda på om du använder lokalt cacheminne, kontrollera din Apptjänst **programinställningar** fliken. Om lokal Cache används, appen inställningen `WEBSITE_LOCAL_CACHE_OPTION` är inställd på `Always`.

Om du inte använder lokal Cache och har det här problemet kan du skicka en supportbegäran.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Visas meddelandet ”ett försök gjordes att komma åt en socket på ett sätt som tillåts inte av åtkomstbehörigheterna”. Hur lösa problemet?

Det här felet uppstår vanligen om utgående TCP-anslutningar på VM-instans är slut. I App Service tillämpas begränsningar för maximalt antal utgående anslutningar som kan göras för varje VM-instans. Mer information finns i [mellan VM numeriska gränser](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Det här felet kan också inträffa om du försöker komma åt en lokal adress från ditt program. Mer information finns i [lokal adressbegäranden](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Mer information om utgående anslutningar i ditt webbprogram finns i bloggposten om [utgående anslutningar till Azure websites](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Hur använder jag Visual Studio till fjärransluten debug min App Service webbapp?

En detaljerad genomgång som visar hur du felsöker ditt webbprogram med hjälp av Visual Studio finns i [Remote felsöka din Apptjänst-webbapp](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
