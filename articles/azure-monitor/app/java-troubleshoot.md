---
title: Felsöka Application Insights i ett Java-webbprojekt
description: Fel söknings guide – övervakning av direktsända Java-appar med Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: a26302b0c0b4361fe3e7aae6aba798f433c72ade
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742183"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Felsökning och vanliga frågor och svar för Application Insights för Java
Är du av frågor eller problem med [Azure Application Insights i Java][java]? Här följer några tips.

## <a name="build-errors"></a>Build-fel
**I Sol förmörkelse eller IntelliJ-idén, när jag lägger till Application Insights SDK via maven eller Gradle, får jag skapa eller kontroll Summa verifierings fel.**

* Om beroende `<version>` elementet använder ett mönster med jokertecken (t. ex. Maven eller (Gradle) `version:'2.0.+'`), försök att ange `2.0.1`en särskild version i stället. `<version>[2.0,)</version>` Se [viktig information](https://github.com/Microsoft/ApplicationInsights-Java/releases) för den senaste versionen.

## <a name="no-data"></a>Ingen data
**Jag har lagt till Application Insights lyckades och körde appen, men jag har aldrig sett data i portalen.**

* Vänta en minut och klicka på Uppdatera. Diagrammen uppdateras regelbundet, men du kan också uppdatera manuellt. Uppdaterings intervallet beror på diagrammets tidsintervall.
* Kontrol lera att du har en Instrumentation-nyckel som har definierats i filen ApplicationInsights. XML (i mappen resurser i projektet) eller konfigurerat som miljö variabel.
* Kontrol lera att det inte `<DisableTelemetry>true</DisableTelemetry>` finns någon nod i XML-filen.
* I brand väggen kan du behöva öppna TCP-portarna 80 och 443 för utgående trafik till dc.services.visualstudio.com. Se den [fullständiga listan över brand Väggs undantag](../../azure-monitor/app/ip-addresses.md)
* I Microsoft Azure start-tavlan tittar du på tjänst status kartan. Om det finns några aviserings indikationer väntar du tills de har kommit tillbaka till OK och stänger sedan och öppnar bladet Application Insights program igen.
* [Aktivera loggning](#debug-data-from-the-sdk) genom att lägga till `<SDKLogger />` ett element under rotnoden i filen ApplicationInsights. XML (i mappen resurser i projektet) och Sök efter poster som föregås av AI: INFO/WARN/fel för misstänkta loggar. 
* Kontrol lera att rätt ApplicationInsights. XML-fil har lästs in av Java SDK genom att titta på konsolens utgående meddelanden för en "konfigurations fil har hittats"-instruktionen.
* Om det inte går att hitta konfigurations filen kontrollerar du meddelandena för att se var konfigurations filen genomsöks och kontrollerar att ApplicationInsights. xml finns på någon av dessa Sök platser. Som en tumregel kan du placera konfigurations filen nära Application Insights SDK-jar v7. Exempel: i Tomcat kan detta innebära mappen WEB-INF/classes. Under utvecklingen kan du placera ApplicationInsights. xml i mappen resurser i ditt webb projekt.
* Titta även på [sidan med GitHub-problem](https://github.com/Microsoft/ApplicationInsights-Java/issues) för kända problem med SDK: n.
* Se till att använda samma version av Application Insights Core, webb, agent och logg tillägg för att undvika eventuella problem med versions konflikter.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Jag har använt för att se data, men har stoppats
* Kontrol lera [status bloggen](https://blogs.msdn.com/b/applicationinsights-status/).
* Har du nått din månads kvot för data punkter? Öppna inställningar/kvot och priser för att ta reda på det. I så fall kan du uppgradera planen eller betala för ytterligare kapacitet. Se [pris Planen](https://azure.microsoft.com/pricing/details/application-insights/).
* Har du nyligen uppgraderat din SDK? Kontrol lera att det bara finns unika SDK-jar v7 i projekt katalogen. Det finns inte två olika versioner av SDK.
* Tittar du på rätt AI-resurs? Matcha iKey för ditt program till den resurs där du förväntar dig telemetri. De bör vara identiska.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Jag ser inte alla data jag förväntar mig
* Öppna sidan användning och uppskattad kostnad och kontrol [](../../azure-monitor/app/sampling.md) lera om samplingen är i drift. (100% överföring innebär att samplingen inte är i drift.) Application Insightss tjänsten kan ställas in så att den endast accepterar en bråkdel av Telemetrin som kommer från din app. Detta hjälper dig att hålla dig inom din månatliga kvot för telemetri.
* Har SDK-sampling Aktiver ATS? Om ja, kommer data att samplas enligt den angivna frekvensen för alla tillämpliga typer.
* Kör du en äldre version av Java SDK? Från och med version 2.0.1 har vi infört fel tolerans mekanism för att hantera tillfälliga nätverks-och Server dels fel samt data beständighet på lokala enheter.
* Får du en begränsning på grund av överdriven telemetri? Om du aktiverar informations loggning visas ett meddelande om att "appen är begränsad". Vår aktuella gräns är 32k för telemetri-objekt per sekund.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java-agenten kan inte avbilda beroende data
* Har du konfigurerat Java agent genom att följa [Konfigurera Java](java-agent.md) -agenten?
* Se till att både The Java agent jar och filen AI-Agent. XML placeras i samma mapp.
* Se till att det beroende som du försöker att automatiskt samla in stöds för automatisk insamling. För närvarande stöder vi bara MySQL, MsSQL, Oracle DB och Azure cache för Redis-beroende samling.
* Använder du JDK 1,7 eller 1,8? För närvarande har vi inte stöd för beroende insamling i JDK 9.

## <a name="no-usage-data"></a>Inga användnings data
**Jag ser information om begär Anden och svars tider, men ingen sid visning, webbläsare eller användar data.**

Du har konfigurerat appen så att du kan skicka telemetri från servern. Nu är nästa steg att [Konfigurera dina webb sidor för att skicka telemetri från webbläsaren][usage].

Alternativt, om din klient är en app på en [telefon eller en annan enhet][platforms], kan du skicka telemetri därifrån.

Använd samma Instrumentation-nyckel för att konfigurera både din klient-och Server telemetri. Data visas i samma Application Insights resurs och du kan korrelera händelser från klient och server.


## <a name="disabling-telemetry"></a>Inaktivera telemetri
**Hur kan jag Inaktivera telemetri-samlingen?**

I kod:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**eller**

Uppdatera ApplicationInsights. XML (i mappen resurser i projektet). Lägg till följande under rotnoden:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Med XML-metoden måste du starta om programmet när du ändrar värdet.

## <a name="changing-the-target"></a>Ändra målet
**Hur kan jag ändra vilken Azure-resurs mitt projekt skickar data till?**

* [Hämta Instrumentation-nyckeln för den nya resursen.][java]
* Om du har lagt till Application Insights i projektet med Azure Toolkit for Eclipse högerklickar du på ditt webb projekt, väljer **Azure**, **konfigurerar Application Insights**och ändrar nyckeln.
* Om du har konfigurerat Instrumentation-nyckeln som miljö variabel uppdaterar du värdet för miljövariabeln med New iKey.
* Annars uppdaterar du nyckeln i ApplicationInsights. xml i mappen resurser i projektet.

## <a name="debug-data-from-the-sdk"></a>Felsöka data från SDK

**Hur kan jag ta reda på vad SDK gör?**

Om du vill ha mer information om vad som händer i API: `<SDKLogger/>` et lägger du till under rotnoden i konfigurations filen ApplicationInsights. xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Du kan också instruera loggen att skicka utdata till en fil:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Start starter för våren

Lägg till följande i `application.properties` filen om du vill aktivera SDK-loggning med våren Boot Apps med Application Insights våren Boot starter:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

eller Skriv ut till standard fel:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java-agent

Så här aktiverar du JVM-agent loggning uppdatera [filen AI-agent. XML](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Egenskaper för Java-kommando rads
_Sedan version 2.4.0_

Aktivera loggning med kommando rads alternativ, utan att ändra konfigurationsfiler:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

eller Skriv ut till standard fel:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Start skärmen för Azure
**Jag tittar på [Azure Portal](https://portal.azure.com). Vet kartan mig något om min app?**

Nej, den visar hälsan för Azure-servrar runtom i världen.

*Hur hittar jag data om min app från Azure start-tavlan (Start skärmen)?*

Förutsatt att du [konfigurerar appen för Application Insights][java]klickar du på Bläddra, väljer Application Insights och väljer den app-resurs som du har skapat för din app. För att det ska gå snabbare i framtiden kan du fästa appen på Start kortet.

## <a name="intranet-servers"></a>Intranät servrar
**Kan jag övervaka en server på intranätet?**

Ja, förutsatt att servern kan skicka telemetri till Application Insights-portalen via det offentliga Internet.

I brand väggen kan du behöva öppna TCP-portarna 80 och 443 för utgående trafik till dc.services.visualstudio.com och f5.services.visualstudio.com.

## <a name="data-retention"></a>Datakvarhållning
**Hur länge sparas data i portalen? Är det säkert?**

Se [data kvarhållning och sekretess][data].

## <a name="debug-logging"></a>Fel söknings loggning
Application Insights använder `org.apache.http`. Detta finns i Application Insights Core-jar v7 under namn området `com.microsoft.applicationinsights.core.dependencies.http`. Detta gör att Application Insights kan hantera scenarier där olika versioner av samma `org.apache.http` finns i en kodbas.

>[!NOTE]
>Om du aktiverar loggning av fel söknings nivå för alla namn områden i appen, kommer den att följas av alla moduler som `org.apache.http` körs, inklusive omdöpt till. `com.microsoft.applicationinsights.core.dependencies.http` Application Insights kan inte använda filtrering för dessa anrop eftersom logg anropet görs av Apache-biblioteket. Loggning av fel SÖKNINGs nivå ger en stor mängd loggdata och rekommenderas inte för direktsända produktions instanser.


## <a name="next-steps"></a>Nästa steg
**Jag har konfigurerat Application Insights för My Java Server-appen. Vad mer kan jag göra?**

* [Övervaka tillgängligheten för dina webb sidor][availability]
* [Övervaka webb sidans användning][usage]
* [Spåra användning och diagnostisera problem i dina enhets program][platforms]
* [Skriv kod för att spåra användningen av din app][track]
* [Samla in diagnostikloggar][javalogs]

## <a name="get-help"></a>Få hjälp
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Fil ett problem på GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

