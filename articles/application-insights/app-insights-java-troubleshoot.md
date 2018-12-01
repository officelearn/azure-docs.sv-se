---
title: Felsöka Application Insights i en Java-webbprojekt
description: Felsökningsguide för – övervakning live Java-appar med Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: e39fb82796c8e6b09f624b2030ff6266bd4189f1
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720430"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Felsökning och vanliga frågor och svar för Application Insights för Java
Frågor eller problem med [Azure Application Insights i Java][java]? Här följer några tips.

## <a name="build-errors"></a>Fel i bygge
**I Eclipse och Intellij Idea, när du lägger till Application Insights SDK via Maven eller Gradle får jag på utvecklingsfel eller valideringsfel.**

* Om beroendet <version> elementet använder ett mönster med jokertecken (t.ex. (Maven) `<version>[2.0,)</version>` eller (Gradle) `version:'2.0.+'`), försök att ange en viss version i stället som `2.0.1`. Se den [viktig](https://github.com/Microsoft/ApplicationInsights-Java/releases) för den senaste versionen.

## <a name="no-data"></a>Inga data
**Jag har lagts till Application Insights och kört min app, men jag har aldrig sett data i portalen.**

* Vänta en minut och klicka på Uppdatera. Diagrammen uppdateras automatiskt med jämna mellanrum, men du kan också uppdatera manuellt. Uppdateringsintervall beror på vilket tidsintervall i diagrammet.
* Kontrollera att du har en instrumenteringsnyckel definieras i filen ApplicationInsights.xml (i resursmappen i ditt projekt) eller konfigureras som miljövariabel.
* Kontrollera att det finns inga `<DisableTelemetry>true</DisableTelemetry>` noden i xml-filen.
* I brandväggen, kan du behöva öppna TCP-portarna 80 och 443 för utgående trafik till dc.services.visualstudio.com. Se den [fullständig lista över brandväggsundantag](app-insights-ip-addresses.md)
* I Microsoft Azure börjar tavla, titta på statusen tjänstkarta. Om det finns några aviseringar indikationer, vänta tills de har kommit tillbaka till OK och stänger och öppnar ditt programblad i Application Insights.
* Aktivera loggning till konsolfönstret IDE genom att lägga till en `<SDKLogger />` elementet under rotnoden i ApplicationInsights.xml-fil (i resursmappen i ditt projekt) och söka efter poster som inleds med AI: fel/information/Varna för misstänkt loggar.
* Kontrollera att rätt ApplicationInsights.xml-fil har har lästs in av Java-SDK genom att titta på konsolens utdata-meddelanden för en ”konfigurationsfilen har har hittats”-instruktion.
* Om konfigurationsfilen inte finns, kontrollera Utdatameddelanden för att se där konfigurationsfilen genomsöks efter och se till att ApplicationInsights.xml finns i en av platserna sökning. Du kan placera konfigurationsfilen nära den Application Insights SDK JAR: er som en tumregel. Till exempel: på Tomcat, skulle detta innebära att mappen WEB-INF/klasser. Du kan placera ApplicationInsights.xml i resursmappen i ditt projekt under utveckling.
* Ta också en titt på [GitHub-ärenden](https://github.com/Microsoft/ApplicationInsights-Java/issues) för kända problem med SDK: N.
* Se till att använda samma version av Application Insights core, webb-, agenten och loggning appenders för att undvika eventuella version konflikt problem.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Jag använde för att se data, men den har stoppats
* Kontrollera den [status blogg](https://blogs.msdn.com/b/applicationinsights-status/).
* Har du nått din månatliga kvot för datapunkter? Öppna inställningar/kvoter och priser för att ta reda på. I så fall kan du uppgradera din plan eller betala för ytterligare kapacitet. Se den [priser schema](https://azure.microsoft.com/pricing/details/application-insights/).
* Har du nyligen uppgraderat din SDK? Se till att endast unika SDK JAR-filer finns i projektkatalogen. Det bör inte finnas två olika versioner av SDK: N finns.
* Tittar du på rätt AI-resurs? . Matcha nyckeln för ditt program till den resurs där du förväntar dig telemetri. De bör vara identiska.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Jag ser inte alla data som jag förväntar mig
* Öppna användningen och beräknad kostnad sida och kontrollera om [sampling](app-insights-sampling.md) är i drift. (100% transmission innebär att sampling inte är i drift). Application Insights-tjänsten kan ställas in att godkänna en bråkdel av telemetrin som tas emot från din app. Detta gör att inom din månatliga kvot för telemetri. 
* Har du SDK Sampling aktiverad? Om Ja, skulle data samplas priset som specificeras för alla tillämpliga typer.
* Kör du en äldre version av Java SDK? Från och med version 2.0.1, har vi lagt fault tolerance mekanism för att hantera tillfälliga nätverket och backend-fel samt datapersistence på lokala enheter.
* Du hämtar begränsas på grund av hög telemetri? Om du aktiverar loggning av information, en logg visas meddelandet ”appen begränsas”. Våra aktuella gränsen är 32 k telemetri objekt/sekund.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java-agenten kan inte hämta data för programberoende
* Har du konfigurerat Java-agenten genom att följa [konfigurera Java-agenten](app-insights-java-agent.md) ?
* Kontrollera att både java-agenten jar och AI-Agent.xml-filen är placerade i samma mapp.
* Se till att beroendet som du försöker automatiskt collect stöds för automatisk insamling. Vi stöder för närvarande endast MySQL, MsSQL, Oracle DB och Redis Cache beroende samling.
* Använder du JDK 1.7 eller 1.8? För närvarande stöder inte vi beroende samling i JDK 9.

## <a name="no-usage-data"></a>Inga användningsdata
**Jag ser data om förfrågningar och svarstider, men inga Sidvisning, webbläsare eller användardata.**

Du konfigurera har din app för att skicka telemetri från servern. Nästa steg är att [ställa in dina webbsidor för att skicka telemetri från webbläsaren][usage].

Du kan också om klienten är en app i en [telefon eller annan enhet][platforms], skickar du telemetri därifrån. 

Använda samma instrumenteringsnyckeln för att ställa in telemetri för både klient- och server. Data kommer att visas i samma Application Insights-resursen och du kommer att kunna korrelera händelser från klienten och servern.


## <a name="disabling-telemetry"></a>Inaktivera telemetri
**Hur kan jag inaktivera insamling av enhetstelemetri?**

I koden:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**eller** 

Uppdatera ApplicationInsights.xml (i resursmappen i ditt projekt). Lägg till följande under rotnoden:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Med XML-metoden kan behöva du starta om programmet när du ändrar värdet.

## <a name="changing-the-target"></a>Ändra mål
**Hur ändrar jag mitt projekt skickar data till vilken Azure-resurs?**

* [Hämta instrumenteringsnyckeln för den nya resursen.][java]
* Om du har lagt till Application Insights i projektet med hjälp av Azure Toolkit för Eclipse högerklickar du på ditt projekt, Välj **Azure**, **konfigurera Application Insights**, och ändra nyckeln.
* Om du har konfigurerat Instrumenteringsnyckeln som miljövariabel uppdatera värdet för miljövariabeln med nya iKey.
* Annars kan uppdatera nyckeln i ApplicationInsights.xml i resursmappen i ditt projekt.

## <a name="debug-data-from-the-sdk"></a>Felsök data från SDK

**Hur tar jag reda på vad SDK: N gör?**

Om du vill ha mer information om vad som händer i API: et kan du lägga till `<SDKLogger/>` under rotnoden i filen ApplicationInsights.xml konfiguration.

Du kan också instruera loggaren att spara till en fil:

```XML

    <SDKLogger type="FILE">
      <enabled>True</enabled>
      <UniquePrefix>JavaSDKLog</UniquePrefix>
    </SDKLogger>
```

Filer kan hittas `%temp%\javasdklogs` eller `java.io.tmpdir` vid Tomcat-servern.


## <a name="the-azure-start-screen"></a>Azure startskärmen
**Jag tittar på [Azure-portalen](https://portal.azure.com). Kartan berätta något om mitt program?**

Nej, den visar hälsotillståndet för Azure-servrar runtom i världen.

*Från Azure start-tavlan (startskärmen) hur hittar jag data om min app?*

Förutsatt att du [konfigurera din app för Application Insights][java], klicka på Bläddra, Välj Application Insights och välj den app du skapade för din app. Om du vill ha kan det snabbare i framtiden du fästa din app på start-planen.

## <a name="intranet-servers"></a>Intranätservrar
**Kan jag övervaka en server i intranätet?**

Ja, under förutsättning att servern kan skicka telemetri till Application Insights-portalen via offentliga internet. 

Du kan behöva öppna TCP-portarna 80 och 443 för utgående trafik till dc.services.visualstudio.com och f5.services.visualstudio.com i din brandvägg.

## <a name="data-retention"></a>Datakvarhållning
**Hur länge sparas data i portalen? Är den säker?**

Se [kvarhållning av Data och sekretess][data].

## <a name="debug-logging"></a>Felsökningsloggning
Application Insights använder `org.apache.http`. Detta har flyttats i Application Insights core JAR-filer under namnområdet `com.microsoft.applicationinsights.core.dependencies.http`. Detta gör det möjligt för Application Insights för att hantera scenarier där olika versioner av samma `org.apache.http` finns i en kodbas. 

>[!NOTE]
>Om du aktiverar felsökningsloggning för alla namnområden i appen på den kommer att användas av alla verkställande moduler, inklusive `org.apache.http` numera `com.microsoft.applicationinsights.core.dependencies.http`. Application Insights kommer inte att kunna använda filtrering för dessa anrop eftersom log-anrop görs i Apache-Library. Nivån felsökningsloggning producerar en betydande del av loggdata och rekommenderas inte för instanser av live i produktion.


## <a name="next-steps"></a>Nästa steg
**Jag ställa in Application Insights för Min server Java-app. Vad kan jag göra?**

* [Övervaka tillgängligheten för dina webbsidor][availability]
* [Övervaka användning av webbsida][usage]
* [Spåra användning och diagnostisera problem i dina appar för enheter][platforms]
* [Skriva kod för att spåra användningen av din app][track]
* [Samla in diagnostikloggar][javalogs]

## <a name="get-help"></a>Få hjälp
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* [Öppna ett GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md

