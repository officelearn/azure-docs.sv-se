---
title: Felsöka försämrade prestanda – Azure App Service | Microsoft Docs
description: Den här artikeln hjälper dig att felsöka långsam prestandaproblem med webbappar i Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: prestanda för webbappar, långsam app app som är långsam
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c0642b24fc8eefd210d5b6b58e7d0c772408166a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261049"
---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Felsöka långsam prestandaproblem med webbappar i Azure App Service
Den här artikeln hjälper dig att felsöka långsam prestandaproblem med webbappar i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Du kan alternativt kan du även skicka en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och klicka på **få Support**.

## <a name="symptom"></a>Symtom
När du bläddrar webbappen sidor belastningen långsamt och ibland timeout.

## <a name="cause"></a>Orsak
Det här problemet beror ofta på nivån programfel, till exempel:

* nätverksbegäranden som tar lång tid
* program-kod eller database-frågor som är ineffektiv
* program med hög minne/CPU
* program som kraschar på grund av ett undantag

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Felsökning kan delas in i tre olika uppgifter i sekventiell ordning:

1. [Se och övervaka programmets beteende](#observe)
2. [Samla in data](#collect)
3. [Åtgärda problemet](#mitigate)

[App Service Web Apps](app-service-web-overview.md) ger dig olika alternativ i varje steg.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Se och övervaka programmets beteende
#### <a name="track-service-health"></a>Spåra tjänstehälsa
Microsoft Azure publicizes varje gång som en försämring av tjänsten avbrott eller prestanda. Du kan spåra hälsotillståndet för tjänsten på den [Azure-portalen](https://portal.azure.com/). Mer information finns i [spåra tjänstehälsa](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Övervaka din webbapp
Det här alternativet kan du ta reda på om ditt program är har problem. I din webbapps blad klickar du på den **förfrågningar och fel** panelen. Den **mått** bladet visar alla mått som du kan lägga till.

Några av de mått som du kan övervaka för din webbapp

* Genomsnittligt arbetsminne
* Genomsnittlig svarstid
* CPU-tid
* Arbetsminne
* Begäranden

![övervaka prestanda för webbappar](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Mer information finns i:

* [Övervaka Webbappar i Azure App Service](web-sites-monitor.md)
* [Få varningsmeddelanden](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Övervaka status för web-slutpunkt
Om du kör din webbapp den **Standard** prisnivå, Web Apps kan du övervaka två slutpunkter från tre geografiska platser.

Slutpunktsövervakning konfigurerar webbtester från geo-distribuerad platser som testar svarstid och drifttid för webbadresser. Testet utför en HTTP GET-åtgärd till webb-URL för att fastställa svarstid och drifttid från varje plats. Varje konfigurerade platsen körs ett test var femte minut.

Drifttid övervakas med hjälp av HTTP-svarskoder och svarstid mäts i millisekunder. Det går inte att en övervakningstestet om HTTP-svarskoden är större än eller lika med 400 eller om svaret tar mer än 30 sekunder. En slutpunkt anses vara tillgänglig om dess övervakningstesten lyckas från de angivna platserna.

Om du vill konfigurera, se [övervaka appar i Azure App Service](web-sites-monitor.md).

Se även [att hålla Azure Web Sites upp plus Slutpunktsövervakning - med Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) ett videoklipp om slutpunktsövervakning.

#### <a name="application-performance-monitoring-using-extensions"></a>Prestandaövervakning av program med hjälp av tillägg
Du kan också övervaka programprestanda med hjälp av en *webbplatstillägg*.

Varje App Service webbapp innehåller en utökningsbar hantering-slutpunkt som gör det möjligt att en kraftfull verktygsuppsättning som distribueras som webbplatstillägg. Tillägg är: 

- Källkodsredigering som [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Hanteringsverktyg för anslutna resurser, till exempel en MySQL-databas ansluten till en webbapp.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) är en plats-tillägg som är också tillgängligt för prestandaövervakning. Om du vill använda Application Insights måste återskapa du din kod med ett SDK. Du kan också installera ett tillägg som ger åtkomst till ytterligare data. Med SDK kan du skriva kod för att övervaka användning och prestanda för din app i detalj. Mer information finns i [övervaka prestanda i webbprogram](../application-insights/app-insights-web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Samla in data
Web Apps-miljö innehåller diagnostisk funktionalitet för att logga information från både webbservern och webbprogrammet. Informationen är uppdelad i web serverdiagnostik och programdiagnostik.

#### <a name="enable-web-server-diagnostics"></a>Aktivera web server-diagnostik
Du kan aktivera eller inaktivera följande typer av loggar:

* **Detaljerad felloggning** -detaljerad information om fel för HTTP-statuskoder som indikerar ett fel (statuskod 400 eller större). Detta kan innehålla information som hjälper dig att avgöra varför servern returnerade felkoden.
* **Det gick inte att begäran om spårning av** -detaljerad information om misslyckade förfrågningar, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och den tid det tar i varje komponent. Detta kan vara användbart om du vill förbättra prestanda för webbappar och isolera vad som orsakar ett specifikt HTTP-fel.
* **Web Server-loggning** -Information om hur du använder W3C utökat loggfilsformat för HTTP-transaktioner. Detta är användbart när du fastställer övergripande webbprogrammått, till exempel antalet begäranden som hanteras eller hur många begäranden som kommer från en specifik IP-adress.

#### <a name="enable-application-diagnostics"></a>Aktivera programdiagnostik
Det finns flera alternativ för att samla in prestandadata för programmet från Webbappar, profil ditt program direkt från Visual Studio eller ändra din programkod för att logga in mer information och spårningar. Du kan välja alternativ baserat på hur mycket åtkomst som du måste programmet och vad du observerade från övervakningen verktyg.

##### <a name="use-application-insights-profiler"></a>Använd Application Insights Profiler
Du kan aktivera Application Insights Profiler att börja samla in detaljerade prestandaspårningar. Du kan komma åt spårningar som avbildas upp till fem dagar sedan när du behöver undersöka problem har inträffat tidigare. Du kan välja det här alternativet så länge som du har åtkomst till webbappens Application Insights-resurs på Azure-portalen.

Application Insights Profiler innehåller statistik om svarstid för varje webbanrop och spårningar som visar vilken kodrad orsakas långsamma svar. Ibland App Service-appen är långsam eftersom vissa kod inte skrivs på ett effektivt sätt. Exempel är sekventiella kod som kan köras i parallella program och oönskade databasen låskonkurrens. Ta bort dessa flaskhalsar i koden ökar appens prestanda, men de är svåra att identifiera utan att ställa in avancerade spårningar och loggar. Spårningar som samlas in av Application Insights Profiler kan identifiera rader med kod som saktar ned programmet och lösa denna utmaning för App Service-appar.

 Mer information finns i [Profiling live Azure web apps med Application Insights](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Använda Fjärrprofilering
I Azure App Service kan Web Apps, API Apps och WebJobs via fjärranslutning profileras. Välj det här alternativet om du har åtkomst till webbappresursen och du vet hur du återskapa problemet, eller om du vet det exakta tidsintervallet prestandaproblemet händer.

Remote Profiling är användbart om CPU-användningen för processen är hög och processer körs långsammare än förväntat eller svarstiden för HTTP-begäranden är högre än normalt, du via fjärranslutning kan profilera din process och få CPU sampling anropsstackar för att analysera processen aktivitet och kod frekvent sökvägar.

Mer information finns i [fjärrprofiler i Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Ställa in diagnostikspår manuellt
Om du har åtkomst till webb-programmets källkod kan du samla in information som genereras av ett webbprogram i programdiagnostik. ASP.NET-program kan använda den `System.Diagnostics.Trace` klassen för att logga information till programloggen för diagnostik. Dock måste du ändra koden och distribuera programmet igen. Den här metoden rekommenderas om din app körs på en testmiljö.

Detaljerade anvisningar om hur du konfigurerar ditt program för loggning finns i [aktivera diagnostikloggning för webbappar i Azure App Service](web-sites-enable-diagnostic-log.md).

#### <a name="use-the-diagnostics-tool"></a>Använda diagnostikverktyget för
App Service tillhandahåller en intelligent och interaktiv upplevelse för att hjälpa dig att felsöka ditt webbprogram med krävs ingen konfiguration. När du stöter på problem med ditt webbprogram, påpeka diagnostikverktyget fel att guida dig till rätt information för att enkelt och snabbt felsöka och lösa problemet.

Om du vill få åtkomst till App Service-diagnostik, navigera till din App Service-app eller en App Service Environment i den [Azure-portalen](https://portal.azure.com). I det vänstra navigeringsfönstret klickar du på **diagnostisera och lösa problem**.

#### <a name="use-the-kudu-debug-console"></a>Använda Kudu-Felsökningskonsolen
Web Apps levereras med ett Felsökningskonsolen som du kan använda för felsökning, utforska, ladda upp filer, samt JSON-slutpunkter för att hämta information om din miljö. Den här konsolen kallas den *Kudu-konsolen* eller *instrumentpanelen SCM* för din webbapp.

Du kan komma åt den här instrumentpanelen genom att gå till länken **https://&lt;ditt appnamn >.scm.azurewebsites.net/**.

Några av de saker som Kudu tillhandahåller är:

* miljöinställningar för ditt program
* loggström
* diagnostiska dump
* Felsök konsolen där du kan köra Powershell-cmdlets och grundläggande DOS-kommandon.

En annan bra funktion i Kudu är att, om ditt program som utlöste första chansen-undantag, du kan använda Kudu och verktyget SysInternals Procdump skapa minne minnesdumpar. Dessa minnesdumpar är ögonblicksbilder av processen och ofta kan hjälpa dig att felsöka mer komplicerade problem med ditt webbprogram.

Läs mer om funktioner som är tillgängliga i Kudu [Azure DevOps-verktyg som du bör känna till om](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Åtgärda problemet
#### <a name="scale-the-web-app"></a>Skala webbappen
I Azure App Service kan för bättre prestanda och dataflöde, du justera skalan där du kör ditt program. Skala upp en webbapp omfattar två relaterade åtgärder: Om du ändrar din App Service-plan till en högre prisnivå och konfigurera vissa inställningar när du har växlat till högre prisnivå.

Läs mer om att skala [skala en webbapp i Azure App Service](web-sites-scale.md).

Dessutom kan du välja att köra programmet i fler än en instans. Skala ut inte bara får du fler funktioner för bearbetning, men ger dig även vissa delar av feltolerans. Om processen stängs av på en instans, andra instanser att fortsätta att hantera begäranden.

Du kan ange skalning för att vara manuell eller automatisk.

#### <a name="use-autoheal"></a>Använd AutoHeal
AutoHeal återanvänder arbetsprocessen för din app baserat på dina inställningar (till exempel konfigurationsändringar, begäranden, Minnesbaserad gränser eller den tid som behövs för att utföra en begäran). I de flesta fall, är omarbetning av processen det snabbaste sättet att komma tillrätta med problem. Även om du kan alltid starta om webbappen från direkt i Azure-portalen, gör AutoHeal det automatiskt åt dig. Allt du behöver göra är att lägga till vissa utlösare i rot web.config för din webbapp. De här inställningarna skulle fungera på samma sätt även om ditt program inte är en .net-app.

Mer information finns i [automatisk återställning Azure Web Sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Starta om webbappen
Starta om är ofta det enklaste sättet att återställa efter engångshändelser. På den [Azure-portalen](https://portal.azure.com/), på din webbapps blad, har du alternativ för att stoppa eller starta om din app.

 ![Starta om webbprogram för att lösa problem med prestanda](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Du kan också hantera webbappen med hjälp av Azure Powershell. Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).
