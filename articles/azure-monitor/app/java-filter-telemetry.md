---
title: Filtrera Azure Application Insights-telemetri i java-webbappen
description: Minska telemetritrafiken genom att filtrera bort de händelser du inte behöver övervaka.
ms.topic: conceptual
ms.date: 3/14/2019
ms.openlocfilehash: 020e54132e0ca0a9f9ccf0236f94515877015637
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659925"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrera telemetri i java-webbappen

Filter är ett sätt att välja den telemetri som [java-webbappen skickar till Application Insights](java-get-started.md). Det finns några färdiga filter som du kan använda, och du kan också skriva egna anpassade filter.

De färdiga filtren inkluderar:

* Spåra allvarlighetsgrad
* Specifika webbadresser, nyckelord eller svarskoder
* Snabba svar – det vill ha förfrågningar som appen svarade snabbt på
* Specifika händelsenamn

> [!NOTE]
> Filtrerar skeva måtten i din app. Du kan till exempel bestämma att du, för att diagnostisera långsamma svar, ställer in ett filter för att ignorera snabba svarstider. Men du måste vara medveten om att de genomsnittliga svarstider som rapporteras av Application Insights då kommer att vara långsammare än den verkliga hastigheten, och antalet begäranden blir mindre än det verkliga antalet.
> Om detta är ett problem, använd [Sampling](../../azure-monitor/app/sampling.md) istället.

## <a name="setting-filters"></a>Ställa in filter

Lägg till ett `TelemetryProcessors` avsnitt som det här exemplet i ApplicationInsights.xml:


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




[Inspektera hela uppsättningen inbyggda processorer](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Inbyggda filter

### <a name="metric-telemetry-filter"></a>Måtttelemetrifilter

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`- Kommaavgränsad lista över anpassade måttnamn.


### <a name="page-view-telemetry-filter"></a>Telemetrifilter för sidvy

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`- Varaktighet avser den tid det tar att läsa in sidan. Om detta anges rapporteras inte sidor som läses in snabbare än den här gången.
* `NotNeededNames`- Kommaavgränsad lista med sidnamn.
* `NotNeededUrls`- Kommaavgränsad lista över URL-fragment. Till exempel `"home"` filtrerar bort alla sidor som har "hem" i webbadressen.


### <a name="request-telemetry-filter"></a>Begär telemetrifilter


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filter för syntetisk källa

Filtrerar bort all telemetri som har värden i egenskapen SyntheticSource. Dessa inkluderar förfrågningar från robotar, spindlar och tillgänglighetstester.

Filtrera bort telemetri för alla syntetiska begäranden:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtrera bort telemetri för specifika syntetiska källor:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`- Kommaavgränsad lista över syntetiska källnamn.

### <a name="telemetry-event-filter"></a>Händelsefilter för telemetri

Filtrerar anpassade händelser (loggas med [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`- Kommaavgränsad lista över händelsenamn.


### <a name="trace-telemetry-filter"></a>Spåra telemetrifilter

Filtrerar loggspårningar (loggas med [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) eller en [loggningsraminsamlare).](java-trace-logs.md)

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel`giltiga värden är:
  *  OFF - Filtrera bort alla spår
  *  TRACE - Ingen filtrering. är lika med spårningsnivå
  *  INFO - Filtrera bort TRACE-nivå
  *  WARN - Filtrera bort TRACE och INFO
  *  FEL - Filtrera bort VARNA, INFO, TRACE
  *  KRITISK - filtrera bort alla utom kritisk


## <a name="custom-filters"></a>Anpassade filter

### <a name="1-code-your-filter"></a>1. Koda ditt filter

Skapa en klass som implementerar `TelemetryProcessor`i koden:

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


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Anropa filtret i konfigurationsfilen

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

### <a name="3-invoke-your-filter-java-spring"></a>3. Anropa ditt filter (Java Spring)

För program som baseras på spring-ramverket måste anpassade telemetriprocessorer registreras i din huvudapplikationsklass som en böna. De kommer sedan att kopplas automatiskt när programmet startar.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Du måste skapa dina egna `application.properties` filterparametrar i och utnyttja Spring Boots externaliserade konfigurationsramverk för att skicka dessa parametrar till ditt anpassade filter. 


## <a name="troubleshooting"></a>Felsökning

*Mitt filter fungerar inte.*

* Kontrollera att du har angett giltiga parametervärden. Varaktigheter ska till exempel vara heltal. Ogiltiga värden gör att filtret ignoreras. Om det anpassade filtret genererar ett undantag från en konstruktor eller en uppsättningsmetod ignoreras det.

## <a name="next-steps"></a>Nästa steg

* [Sampling](../../azure-monitor/app/sampling.md) - Överväg att sampling som ett alternativ som inte förvränger dina mått.
