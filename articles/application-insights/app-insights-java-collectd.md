---
title: "Övervaka Java web app prestanda på Linux - Azure | Microsoft Docs"
description: "Utökad övervakning av programprestanda för din Java-webbplats med CollectD plugin-programmet för Application Insights."
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: cde0fc020f1774e0e7669e7573e4aaff3534b34c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: Linux prestandamått i Application Insights


Utforska prestandastatistik för Linux-system i [Programinsikter](app-insights-overview.md), installera [collectd](http://collectd.org/), tillsammans med dess plugin Application Insights. Den här lösningen med öppen källkod samlar in olika system- och statistik.

Normalt ska du använda collectd om du redan har [instrumenterats Java webbtjänsten med Application Insights][java]. Den ger dig fler data som kan hjälpa dig att förbättra prestanda för din app eller diagnostisera problem. 

![exempel diagram](./media/app-insights-java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>Hämta nyckel för instrumentation
I den [Microsoft Azure-portalen](https://portal.azure.com)öppnar den [Programinsikter](app-insights-overview.md) resurs där du vill att data ska visas. (Eller [skapar en ny resurs](app-insights-create-new-resource.md).)

Ta en kopia av nyckeln instrumentation som identifierar resursen.

![Bläddra igenom alla, öppna din resurs i Essentials nedrullningsbara Markera och kopiera nyckeln Instrumentation](./media/app-insights-java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Installera collectd och plugin-programmet
På ditt Linux-servrar:

1. Installera [collectd](http://collectd.org/) version 5.4.0 eller senare.
2. Hämta den [Programinsikter collectd writer plugin](https://aka.ms/aijavasdk). Notera versionsnumret.
3. Kopiera JAR-plugin-programmet i `/usr/share/collectd/java`.
4. Redigera `/etc/collectd/collectd.conf`:
   * Se till att [pluginprogrammet Java](https://collectd.org/wiki/index.php/Plugin:Java) är aktiverad.
   * Uppdatera JVMArg för java.class.path för att inkludera följande JAR. Uppdatera versionsnumret för att matcha det du hämtade:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Lägg till den här fragment med Instrumentation nyckeln från din resurs:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Här är en del av en exempelkonfigurationsfilen:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Konfigurera andra [collectd plugin-program](https://collectd.org/wiki/index.php/Table_of_Plugins), som kan samla in olika data från olika källor.

Starta om collectd enligt dess [manuell](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Visa data i Application Insights
Öppna i Application Insights-resurs [Metrics Explorer och Lägg till diagram][metrics], välja mått som du vill se från anpassad kategori.

![](./media/app-insights-java-collectd/result.png)

Som standard samman mätvärdena över alla värddatorerna som mätvärdena som har samlats in. Om du vill visa mått per värd i diagrammet informationsbladet aktivera gruppering och sedan välja att gruppera efter CollectD-värden.

## <a name="to-exclude-upload-of-specific-statistics"></a>För att utesluta överföringen av specifika statistik
Som standard skickar Application Insights plugin-programmet alla data som samlas in av alla aktiverade collectd läsa plugin-program. 

För att utesluta data från specifika plugin-program eller datakällor:

* Redigera konfigurationsfilen. 
* I `<Plugin ApplicationInsightsWriter>`, Lägg till direktivet rader så här:

| Direktivet | Verkan |
| --- | --- |
| `Exclude disk` |Undanta alla data som samlas in av den `disk` plugin-program |
| `Exclude disk:read,write` |Exkludera datakällor med namnet `read` och `write` från den `disk` plugin-programmet. |

Separata direktiv med en ny rad.

## <a name="problems"></a>Har du problem?
*Data i portalen visas inte*

* Öppna [Sök] [ diagnostic] att se om raw händelser har tagits emot. Ibland kan de ta längre tid att visas i metrics explorer.
* Du kan behöva [ange brandväggsundantag för utgående data](app-insights-ip-addresses.md)
* Aktivera spårning i Application Insights plugin-programmet. Lägg till följande rad i `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Öppna en terminal och starta collectd i utförligt läge, se eventuella problem som den rapporterar:
  * `sudo collectd -f`

## <a name="known-issue"></a>Kända problem

Application Insights skriva plugin-programmet är inte kompatibelt med vissa Läs plugin-program. Vissa plugin-program att ibland skicka ”NaN” där plugin-programmet Application Insights förväntar sig ett flyttal.

Symptom: Collectd loggen visar fel som innehåller ”AI:... SyntaxError: Oväntad token N ”.

Lösning: Utesluta data som samlas in av plugin-program för problemet skrivåtgärder. 

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md


