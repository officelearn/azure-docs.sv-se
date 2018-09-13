---
title: Filtrera Azure Application Insights telemetry i Java-webbappen | Microsoft Docs
description: Minska telemetritrafik genom att filtrera bort händelser som du inte behöver övervaka.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 8ea431b3ab1836626fc6c7551f3bee24e4a3db86
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646802"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrera telemetri i Java-webbappen

Filter är ett sätt att välja telemetrin som dina [Java-webbapp skickar till Application Insights](app-insights-java-get-started.md). Det finns vissa out-of the box-filter som du kan använda och du kan också skriva egna anpassade filter.

Out-of the box-filtren är:

* Spåra allvarlighetsgrad
* Specifika URL: er, nyckelord eller svarskoder
* Snabba svar – det vill säga begäran som din app svarat på snabbt
* Specifika händelsenamn

> [!NOTE]
> Filter förskjuta mått för din app. Du kan till exempel bestämma att, för att kunna diagnostisera långsamma svar du anger ett filter för att ta bort snabba svarstider. Men du måste vara medveten om att de genomsnittliga svarstider som rapporterats av Application Insights blir långsammare än SANT hastighet och antal begäranden ska vara mindre än antalet verkliga.
> Om det är ett problem kan använda [Sampling](app-insights-sampling.md) i stället.

## <a name="setting-filters"></a>Ange filter

I ApplicationInsights.xml, lägger du till en `TelemetryProcessors` avsnittet som i följande exempel:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Granska den fullständiga uppsättningen inbyggda processorer](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Inbyggda filter

### <a name="metric-telemetry-filter"></a>Metrisk telemetri-filter

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` – Kommaavgränsad lista över anpassade mått namn.


### <a name="page-view-telemetry-filter"></a>Sidvystelemetri filter

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` -Varaktighet refererar till den tid det tar att läsa in sidan. Om inställningen anges rapporteras inte sidor som läses in snabbare än den angivna tiden.
* `NotNeededNames` – Kommaavgränsad lista över namn på sidan.
* `NotNeededUrls` – Kommaavgränsad lista över URL: en fragment. Till exempel `"home"` filtrerar ut alla sidor som har ”home” i Webbadressen.


### <a name="request-telemetry-filter"></a>Begär telemetri Filter


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Syntetisk källfilter

Filtrerar ut all telemetri som har värden i egenskapen SyntheticSource. Dessa inkluderar begäranden från robotar, spindlar och tillgänglighetstester.

Filtrera bort telemetri för alla syntetiska förfrågningar:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtrera bort telemetri för särskilda syntetiska källor:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` – Kommaavgränsad lista med syntetiska namn på.

### <a name="telemetry-event-filter"></a>Händelsefilter för telemetri

Anpassade händelser (inloggad med [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` – Kommaavgränsad lista över händelsenamn.


### <a name="trace-telemetry-filter"></a>Spåra telemetri filter

Filter loggspårningar (inloggad med [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) eller en [loggning framework insamlaren](app-insights-java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` Giltiga värden är:
 *  RABATT - filtrera bort alla spårningar
 *  SPÅRNINGEN – ingen filtrering. är lika med spårningsnivå
 *  INFO - Filter ut spårningsnivå
 *  WARN - Filter ut SPÅRNINGS- och information
 *  FEL - Filter ut Varna, INFO, SPÅRNING
 *  KRITISK - filter ut alla utom kritiska


## <a name="custom-filters"></a>Anpassade filter

### <a name="1-code-your-filter"></a>1. Koda ditt filter

I din kod, skapar du en klass som implementerar `TelemetryProcessor`:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Anropa filter i konfigurationsfilen

I ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

## <a name="troubleshooting"></a>Felsökning

*Mina filter fungerar inte.*

* Kontrollera att du har angett giltiga parametervärden. Varaktigheter bör till exempel vara heltal. Ogiltiga värden kommer filtret som ska ignoreras. Om ditt filter genererar ett undantag från en konstruktor eller set-metod, kommer att ignoreras.

## <a name="next-steps"></a>Nästa steg

* [Sampling](app-insights-sampling.md) -Överväg sampling som ett alternativ som inte förskjuta dina mått.
