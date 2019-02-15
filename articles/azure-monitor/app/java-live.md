---
title: Application Insights för Java-webbappar som redan är live
description: Börja övervaka ett webbprogram som körs redan på servern
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: dbc16718358f86b02c6970ec42c4bc0ff68a951d
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301051"
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights för Java-webbappar som redan är live

Om du har ett webbprogram som redan körs på din Java EE-server kan du börja övervaka den med [Application Insights](../../azure-monitor/app/app-insights-overview.md) utan att behöva göra ändringar i koden eller kompilera om ditt projekt. Med det här alternativet får du information om HTTP-begäranden som skickas till din server, ohanterade undantag och prestandaräknare.

Du behöver en prenumeration på [Microsoft Azure](https://azure.com).

> [!NOTE]
> Proceduren på den här sidan lägger till SDK: N till din webbapp under körning. Den här runtime instrumentation är användbart om du inte vill uppdatera eller återskapa din källkod. Men om möjligt rekommenderar vi att du [lägga till SDK till i källkoden](java-get-started.md) i stället. Som ger dig fler alternativ, till exempel skriva kod för att spåra användaraktivitet.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Hämta en Application Insights-instrumenteringsnyckel
1. Logga in på den [Microsoft Azure-portalen](https://portal.azure.com)
2. Skapa en ny Application Insights-resurs och ange programtypen till Java-webbprogram.
   
    ![Fyll i ett namn, välj Java-webbapp och klicka på Skapa](./media/java-live/02-create.png)

    Resursen har skapats på några sekunder.

4. Öppna den nya resursen och dess instrumenteringsnyckeln. Du kommer att behöva klistra in den här nyckeln i projektkoden inom kort.
   
    ![I den nya resursöversikten klickar du på Egenskaper och kopierar instrumenteringsnyckeln.](./media/java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Ladda ned SDK
1. Ladda ned [Application Insights SDK för Java](https://aka.ms/aijavasdk). 
2. Extrahera SDK-innehållet till den katalog som innehåller projektet binära filer som har lästs in på din server. Om du använder Tomcat görs vanligtvis den här katalogen under `webapps/<your_app_name>/WEB-INF/lib`

Observera att du måste upprepa det här på varje server-instans och för varje app.

## <a name="3-add-an-application-insights-xml-file"></a>3. Lägg till en XML-fil för Application Insights
Skapa ApplicationInsights.xml i mappen där du har lagt till SDK: N. Placera i den följande XML.

Ersätt instrumenteringsnyckeln som du fick från Azure Portal.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* Instrumenteringsnyckeln skickas tillsammans med alla element i telemetrin och uppmanar Application Insights att visa den i din resurs.
* Komponenten HTTP-begäran är valfri. Den skickar automatiskt telemetri om förfrågningar och svarstider till portalen.
* Händelsekorrelation är ett tillägg till komponenten HTTP-begäran. Det tilldelar en identifierare för varje förfrågan som tas emot av servern och lägger till denna identifierare som en egenskap för alla objekt i telemetrin som egenskapen Operation.Id. Du kan korrelera telemetrin som är associerade med varje begäran genom att ange ett filter i [diagnostiksökning](../../azure-monitor/app/diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Lägga till ett HTTP-filter
Öppna filen Web.XML i projektet och sammanfoga följande kodavsnitt under webbappens nod, där dina programfiler är konfigurerade.

För bästa resultat bör filtret mappas före alla andra filter.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5. Kontrollera brandväggsundantag
Du kan behöva [ange undantag för att skicka utgående data](../../azure-monitor/app/ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Starta om din webbapp
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Visa telemetrin i Application Insights
Gå tillbaka till Application Insights-resursen på [Microsoft Azure Portal](https://portal.azure.com).

Telemetri om HTTP-förfrågningar visas på översiktsbladet. (Om informationen inte visas väntar du några sekunder och klickar på Uppdatera.)

![exempeldata](./media/java-live/5-results.png)

Klicka dig vidare i diagrammet om du vill visa mer detaljerade mätvärden. 

![](./media/java-live/6-barchart.png)

Och när du visar egenskaperna för en begäran, kan du se telemetrihändelserna som är associerade med den, till exempel begäranden och undantag.

![](./media/java-live/7-instance.png)

[Lär dig mer om mätvärden.](../../azure-monitor/app/metrics-explorer.md)

## <a name="next-steps"></a>Nästa steg
* [Lägg till telemetri till webbsidor](javascript.md) vill övervaka sidvisningar och användarmått.
* [Konfigurera webbtester](../../azure-monitor/app/monitor-web-app-availability.md) att kontrollera att ditt program är aktivt och effektivt.
* [Samla in loggspårningar](java-trace-logs.md)
* [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
* [Konfigurera en Spring Boot-initieringsapp](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)
