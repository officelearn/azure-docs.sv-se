---
title: Felsöka programinsikter i ett Java-webbprojekt
description: Felsökningsguide – övervakning av aktiva Java-appar med Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 04e98938bc5dd17816ae873f122073212275a414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657188"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Felsökning och vanliga frågor och svar för Application Insights för Java
Frågor eller problem med [Azure Application Insights i Java?][java] Här följer några tips.

## <a name="build-errors"></a>Byggfel
**I Eclipse eller Intellij Idea, när du lägger till Application Insights SDK via Maven eller Gradle, får jag bygga eller kontrollsumma valideringsfel.**

* Om `<version>` beroendeelementet använder ett mönster med jokertecken (t.ex. `<version>[2.0,)</version>` (Maven) `version:'2.0.+'`eller (Gradle) kan `2.0.1`du prova att ange en viss version i stället som . Se [viktig information](https://github.com/Microsoft/ApplicationInsights-Java/releases) för den senaste versionen.

## <a name="no-data"></a>Inga data
**Jag har lagt till Application Insights och kört min app, men jag har aldrig sett data i portalen.**

* Vänta en minut och klicka på Uppdatera. Diagrammen uppdateras regelbundet, men du kan också uppdatera manuellt. Uppdateringsintervallet beror på diagrammets tidsintervall.
* Kontrollera att du har en instrumenteringsnyckel definierad i filen ApplicationInsights.xml (i resursmappen i projektet) eller konfigurerad som miljövariabel.
* Kontrollera att det `<DisableTelemetry>true</DisableTelemetry>` inte finns någon nod i XML-filen.
* I brandväggen kan du behöva öppna TCP-portar 80 och 443 för att utgående trafik ska dc.services.visualstudio.com. Se hela [listan över brandväggsund undantag](../../azure-monitor/app/ip-addresses.md)
* Titta på tjänststatusöversikten på Starttavlan för Microsoft Azure. Om det finns några varningsindikationer väntar du tills de har återvänt till OK och stänger och öppnar sedan programbladet Application Insights igen.
* [Aktivera loggning](#debug-data-from-the-sdk) genom `<SDKLogger />` att lägga till ett element under rotnoden i filen ApplicationInsights.xml (i mappen resurser i projektet) och sök efter poster som föregicks med AI: INFO/WARN/ERROR för misstänkta loggar. 
* Kontrollera att den korrekta ApplicationInsights.xml-filen har lästs in av Java SDK genom att titta på konsolens utdatameddelanden för en "Konfigurationsfil har hittats" uttalande.
* Om konfigurationsfilen inte hittas kontrollerar du utdatameddelandena för att se var konfigurationsfilen söks efter och kontrollera att ApplicationInsights.xml finns på en av dessa sökplatser. Som en tumregel kan du placera config-filen nära Application Insights SDK JARs. Till exempel: i Tomcat skulle detta innebära mappen WEB-INF/classes. Under utvecklingen kan du placera ApplicationInsights.xml i resursmappen i webbprojektet.
* Titta också på [GitHub problem sida](https://github.com/Microsoft/ApplicationInsights-Java/issues) för kända problem med SDK.
* Se till att använda samma version av Application Insights core-, web-, agent- och loggningsappenders för att undvika problem med versionskonflikter.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Jag brukade se data, men det har slutat
* Kontrollera [statusbloggen](https://blogs.msdn.com/b/applicationinsights-status/).
* Har du nått din månatliga kvot av datapunkter? Öppna Inställningar/Kvot och Prissättning för att ta reda på. Om så är fallet kan du uppgradera din plan eller betala för ytterligare kapacitet. Se [prissystemet](https://azure.microsoft.com/pricing/details/application-insights/).
* Har du nyligen uppgraderat din SDK? Se till att det bara finns unika SDK-burkar i projektkatalogen. Det bör inte finnas två olika versioner av SDK närvarande.
* Tittar du på rätt AI-resurs? Matcha iKey för ditt program till den resurs där du förväntar dig telemetri. De borde vara likadana.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Jag ser inte all data jag förväntar mig
* Öppna sidan Användning och uppskattad kostnad och kontrollera om [sampling](../../azure-monitor/app/sampling.md) är i drift. (100% överföring innebär att provtagning inte är i drift.) Tjänsten Application Insights kan ställas in så att den bara accepterar en bråkdel av den telemetri som kommer från din app. Detta hjälper dig att hålla dig inom din månatliga kvot av telemetri.
* Har du aktiverat SDK-sampling? Om ja, skulle data tas prov med den hastighet som anges för alla tillämpliga typer.
* Kör du en äldre version av Java SDK? Från och med version 2.0.1 har vi infört feltoleransmekanism för att hantera intermittenta nätverks- och backend-fel samt databeständighet på lokala enheter.
* Får du strypt på grund av överdriven telemetri? Om du aktiverar INFO-loggning visas ett loggmeddelande "Appen begränsas". Vår nuvarande gräns är 32k telemetri objekt / sekund.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java-agenten kan inte fånga in beroendedata
* Har du konfigurerat Java-agent genom att följa [Konfigurera Java Agent?](java-agent.md)
* Kontrollera att både java-agentburken och FILEN AI-Agent.xml placeras i samma mapp.
* Kontrollera att beroendet du försöker samla in automatiskt stöds för automatisk insamling. För närvarande stöder vi bara MySQL, MsSQL, Oracle DB och Azure Cache for Redis dependency collection.

## <a name="no-usage-data"></a>Inga användningsdata
**Jag ser data om begäranden och svarstider, men ingen sidvisning, webbläsare eller användardata.**

Du har konfigurerat appen för att skicka telemetri från servern. Nu är ditt nästa steg att [ställa in dina webbsidor för att skicka telemetri från webbläsaren][usage].

Alternativt, om din klient är en app i en [telefon eller annan enhet,][platforms]kan du skicka telemetri därifrån.

Använd samma instrumenteringsnyckel för att konfigurera både klient- och servertelemetri. Data visas i samma Application Insights-resurs och du kan korrelera händelser från klient och server.


## <a name="disabling-telemetry"></a>Inaktivera telemetri
**Hur inaktiverar jag telemetrisamling?**

I kod:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Eller**

Uppdatera ApplicationInsights.xml (i resursmappen i projektet). Lägg till följande under rotnoden:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Med XML-metoden måste du starta om programmet när du ändrar värdet.

## <a name="changing-the-target"></a>Ändra målet
**Hur ändrar jag vilken Azure-resurs mitt projekt skickar data till?**

* [Hämta instrumenteringsnyckeln för den nya resursen.][java]
* Om du har lagt till Programstatistik i projektet med Hjälp av Azure Toolkit for Eclipse högerklickar du på webbprojektet, väljer **Azure**, **Konfigurera programstatistik**och ändrar nyckeln.
* Om du hade konfigurerat instrumenteringsnyckeln som miljövariabel uppdaterar du värdet för miljövariabeln med ny iKey.
* Annars uppdaterar du nyckeln i ApplicationInsights.xml i resursmappen i projektet.

## <a name="debug-data-from-the-sdk"></a>Felsöka data från SDK

**Hur kan jag ta reda på vad SDK gör?**

Om du vill ha mer information om `<SDKLogger/>` vad som händer i API:et lägger du till under rotnoden i konfigurationsfilen ApplicationInsights.xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Du kan också instruera loggern att skriva ut till en fil:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Vår Boot Starter

Om du vill aktivera SDK-loggning med Spring Boot Apps med `application.properties` Application Insights Spring Boot Starter lägger du till följande i filen:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

eller skriva ut till standardfel:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java-agenten

Så här aktiverar du JVM-agentloggning uppdatera [filen AI-Agent.xml](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Egenskaper för Java-kommandorad
_Sedan version 2.4.0_

Så här aktiverar du loggning med kommandoradsalternativ utan att ändra konfigurationsfiler:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

eller skriva ut till standardfel:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Startskärmen för Azure
**Jag tittar på [Azure-portalen](https://portal.azure.com). Säger kartan något om min app?**

Nej, det visar hälsan hos Azure-servrar runt om i världen.

*Hur hittar jag data om min app från Starttavlan för Azure(startskärmen).*

Förutsatt att du [konfigurerar appen för Application Insights][java]klickar du på Bläddra, väljer Programstatistik och väljer den appresurs som du skapade för appen. För att komma dit snabbare i framtiden kan du fästa appen på starttavlan.

## <a name="intranet-servers"></a>Intranätservrar
**Kan jag övervaka en server i intranätet?**

Ja, förutsatt att servern kan skicka telemetri till Application Insights-portalen via det offentliga internet.

I brandväggen kan du behöva öppna TCP-portar 80 och 443 för att utgående trafik ska dc.services.visualstudio.com och f5.services.visualstudio.com.

## <a name="data-retention"></a>Datakvarhållning
**Hur länge lagras data i portalen? Är den säker?**

Se [Lagring av data och sekretess][data].

## <a name="debug-logging"></a>Felsökningsloggning
Application Insights `org.apache.http`använder . Detta flyttas inom Application Insights kärnburkar `com.microsoft.applicationinsights.core.dependencies.http`under namnområdet . Detta gör det möjligt för Application Insights `org.apache.http` att hantera scenarier där olika versioner av samma finns i en kodbas.

>[!NOTE]
>Om du aktiverar LOGGning på FELSÖKNINGsnivå för alla namnområden i appen, kommer den `org.apache.http` att `com.microsoft.applicationinsights.core.dependencies.http`hedras av alla körmoduler, inklusive omdöpta som . Application Insights kommer inte att kunna tillämpa filtrering för dessa samtal eftersom logganropet görs av Apache-biblioteket. FELSÖKNINGsnivåloggning producerar en betydande mängd loggdata och rekommenderas inte för instanser av liveproduktion.


## <a name="next-steps"></a>Nästa steg
**Jag har konfigurerat Application Insights för min Java-serverapp. Vad mer kan jag göra?**

* [Övervaka webbsidornas tillgänglighet][availability]
* [Användning av webbsida för bildskärm][usage]
* [Spåra användning och diagnostisera problem i enhetsapparna][platforms]
* [Skriva kod för att spåra användning av din app][track]
* [Samla in diagnostikloggar][javalogs]

## <a name="get-help"></a>Få hjälp
* [Stackspill](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Lämna in ett problem på GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

