---
title: Felsöka Application Insights i ett Java-webbprojekt
description: Felsökningsguide för - övervakning av live Java-appar med Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6b3205603b91077ca2c3226dcb78589de37d15cf
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Felsökning och vanliga frågor och svar för Application Insights för Java
Frågor eller problem med [Azure Application Insights i Java][java]? Här följer några tips.

## <a name="build-errors"></a>Skapa fel
**I Eclipse eller Intellij Idea, när du lägger till Application Insights SDK via Maven eller Gradle, visas build eller kontrollsumma verifieringsfel.**

* Om beroendet <version> elementet använder ett mönster med jokertecken (t.ex. (Maven) `<version>[2.0,)</version>` eller (Gradle) `version:'2.0.+'`), pröva en specifik version i stället som `2.0.1`. Finns det [viktig information](https://github.com/Microsoft/ApplicationInsights-Java/releases) för den senaste versionen.

## <a name="no-data"></a>Inga data
**Jag har lagts till Application Insights och körde min app, men aldrig sett data i portalen.**

* Vänta en minut och klicka på Uppdatera. Diagrammen uppdatera själva regelbundet, men du kan också uppdatera manuellt. Intervall för uppdatering är beroende av tidsintervallet i diagrammet.
* Kontrollera att du har en instrumentation nyckel definieras i filen ApplicationInsights.xml (i mappen resurser i ditt projekt) eller konfigureras som miljövariabeln.
* Kontrollera att det finns inga `<DisableTelemetry>true</DisableTelemetry>` noden i xml-filen.
* Du kan behöva öppna TCP-portarna 80 och 443 för utgående trafik till dc.services.visualstudio.com i brandväggen. Finns det [fullständig lista över brandväggsundantag](app-insights-ip-addresses.md)
* Starta board i Microsoft Azure, titta på tjänstkartan status. Om det finns vissa uppgifter som avisering, vänta tills de har återgått till OK och Stäng och öppna bladet din Application Insights-programmet igen.
* Aktiverar loggning till konsolfönstret IDE genom att lägga till en `<SDKLogger />` rotnoden i ApplicationInsights.xml-filen (i mappen resurser i ditt projekt) och Sök efter poster som inleds med AI-element: INFO/Varna/fel för misstänkt loggar.
* Kontrollera att rätt ApplicationInsights.xml fil har har lästs in av Java-SDK genom att titta på konsolens Utdatameddelanden för en ”konfigurationsfilen har har hittat”-instruktion.
* Om konfigurationsfilen inte finns, kontrollera utdata meddelandena som finns där konfigurationsfilen genomsöks efter och se till att ApplicationInsights.xml finns i något av dessa platser. Du kan placera konfigurationsfilen nära den Application Insights SDK JAR: er som en tumregel. Exempel: i Tomcat, detta betyder att mappen WEB-INF/klasser. Du kan placera ApplicationInsights.xml i mappen resurser i ditt webbprojekt under utveckling.
* Kontrollera också titta på [GitHub utfärdar sidan](https://github.com/Microsoft/ApplicationInsights-Java/issues) för kända problem med SDK.
* Se till att använda samma version av Application Insights kärnor, webb-, agent och loggning appenders för att undvika eventuella version konflikt problem.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Jag för att se data, men den har stoppats
* Kontrollera den [status blogg](http://blogs.msdn.com/b/applicationinsights-status/).
* Har du uppnått den månatliga kvoten för datapunkter? Öppna inställningar/kvoten och priser för att ta reda på. I så fall, kan du uppgradera din plan eller betala för extra kapacitet. Finns det [priser schemat](https://azure.microsoft.com/pricing/details/application-insights/).
* Du nyss har uppgraderat din SDK? Se till att endast unika SDK burkar finns i projektkatalogen. Det får inte vara två olika versioner av SDK finns.
* Tittar på rätt AI-resurs? Kontrollera matcha iKey för programmet till den resurs där du förväntar dig telemetri. De ska vara samma.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Alla data som jag förväntade visas inte
* Öppna användningen och uppskattade kostnaden sidan och kontrollera om [provtagning](app-insights-sampling.md) är i drift. (100% transmission innebär att provtagning inte är i drift.) Application Insights-tjänsten kan ställas in att godkänna en bråkdel av telemetri som tas emot från din app. Detta gör att inom din månatliga kvoten av telemetri. 
* Har du SDK provtagning aktiverad? Om Ja, skulle data samlas in vid den angivna för alla tillämpliga typer.
* Kör du en äldre version av Java SDK? Från och med version 2.0.1, har vi introducerade feltolerans mekanism för att hantera återkommande nätverk och backend-fel samt-datapersistence på lokala enheter.
* Har du komma begränsats på grund av mycket telemetri? Om du aktiverar loggning INFO, en logg visas meddelandet ”App begränsas”. Vår aktuella gränsen är 32 kB telemetri objekt/sekund.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java-agenten kan inte hämta beroendedata
* Har du konfigurerat Java-agenten genom att följa [konfigurera agenten för Java](app-insights-java-agent.md) ?
* Kontrollera att både java agent jar och filen AI-Agent.xml placeras i samma mapp.
* Se till att beroendet som du försöker att automatiskt samla stöds för automatisk insamling. Vi stöder för närvarande endast MySQL, MsSQL, Oracle-databas och samling för Redis-Cache-beroendet.
* Du använder JDK 1.7 eller 1.8? För närvarande stöder vi inte beroende samling i JDK 9.

## <a name="no-usage-data"></a>Inga användningsdata
**Information om begäranden och svarstider, men inga vyn sida, webbläsare eller användardata visas.**

Du konfigurerat din app att skicka telemetri från servern. Nu är din nästa steg att [konfigurera webbsidorna för att skicka telemetri från webbläsaren][usage].

Alternativt, om klienten är en app i en [telefonnummer eller andra][platforms], du kan skicka telemetri därifrån. 

Använd samma instrumentation nyckel om du vill ställa in telemetri för både klient och server. Informationen som visas i samma Application Insights-resursen och du kommer att kunna matcha händelser från klienten och servern.


## <a name="disabling-telemetry"></a>Inaktivera telemetri
**Hur inaktiverar jag telemetri samlingen?**

I koden:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Or** 

Uppdatera ApplicationInsights.xml (i mappen resurser i ditt projekt). Lägg till följande under rotnoden:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

XML-metoden, måste du starta om programmet när du ändrar värdet.

## <a name="changing-the-target"></a>Ändra målet
**Hur kan jag ändra vilka Azure-resurs projektet skickar data till?**

* [Hämta instrumentation nyckeln för den nya resursen.][java]
* Om du har lagt till Application Insights i ditt projekt med hjälp av Azure-verktygen för Eclipse högerklickar du på webbprojektet, Välj **Azure**, **konfigurera Application Insights**, och ändra nyckeln.
* Om du har konfigurerat nyckeln Instrumentation som miljövariabeln uppdatera värdet för miljövariabeln med nya iKey.
* Annars uppdatera nyckeln i ApplicationInsights.xml i mappen resurser i ditt projekt.

## <a name="debug-data-from-the-sdk"></a>Felsöka data från SDK

**Hur kan jag reda på vad SDK gör?**

Lägg till om du vill ha mer information om vad som händer i API: et `<SDKLogger/>` under rotnoden ApplicationInsights.xml konfigurationsfil.

Du kan också instruera loggaren till utdata till en fil:

```XML

    <SDKLogger type="FILE">
      <enabled>True</enabled>
      <UniquePrefix>JavaSDKLog</UniquePrefix>
    </SDKLogger>
```

Filer kan hittas `%temp%\javasdklogs` eller `java.io.tmpdir` vid Tomcat-server.


## <a name="the-azure-start-screen"></a>Azure startskärmen
**Jag tittar på [Azure-portalen](https://portal.azure.com). Kartans berätta något om min app?**

Nej, den visar hälsotillståndet för Azure-servrar runtom i världen.

*Från Azure start-kort (startsidan) hur hittar jag information om min app?*

Om du [konfigurera din app för Application Insights][java], klicka på Bläddra, Välj Application Insights och välj den app-resurs som du skapade för din app. För att få kan det snabbare i framtiden, du fästa din app på start-planen.

## <a name="intranet-servers"></a>Intranätservrar
**Övervakar jag en server i intranätet?**

Ja, under förutsättning att servern kan skicka telemetri till Application Insights-portalen via det offentliga internet. 

Du kan behöva öppna TCP-portarna 80 och 443 för utgående trafik till dc.services.visualstudio.com och f5.services.visualstudio.com i brandväggen.

## <a name="data-retention"></a>Datakvarhållning
**Hur länge sparas data i portalen? Är det säkra?**

Se [datalagring och sekretess][data].

## <a name="debug-logging"></a>Felsökningsloggning
Application Insights använder `org.apache.http`. Detta har flyttats i Application Insights core burkar i namnområdet `com.microsoft.applicationinsights.core.dependencies.http`. Detta gör Application Insights att hantera scenarier där olika versioner av samma `org.apache.http` finns i en kodbas. 

>[!NOTE]
>Om du aktiverar felsökningsloggning för nivån för alla namnområden i appen, den kan användas av alla verkställande moduler, inklusive `org.apache.http` ändras namnet till `com.microsoft.applicationinsights.core.dependencies.http`. Application Insights kommer inte att kunna använda filtrering för dessa anrop eftersom loggen anropet görs av Apache-biblioteket. Nivån felsökningsloggning ger mycket av loggdata och rekommenderas inte för live produktion instanser.


## <a name="next-steps"></a>Nästa steg
**Jag konfigurera Application Insights för Min server Java-app. Vad kan jag göra?**

* [Övervaka tillgängligheten för webbsidor][availability]
* [Övervaka användningen av webbsida][usage]
* [Spåra användning och diagnostisera problem i dina enhetsappar][platforms]
* [Skriva kod för att spåra användningen av din app][track]
* [Samla in diagnostikloggar][javalogs]

## <a name="get-help"></a>Få hjälp
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* [Filen ett problem på GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md

