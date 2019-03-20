---
title: Övervaka prestanda för Java-webbappar på Linux – Azure | Microsoft Docs
description: Utökad övervakning av programprestanda för Java-webbplatsen med insamlade plugin-programmet för Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 783cef6ff4e107838bb3ff7502fb4a8e9189ec3d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011184"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>insamlade: Linux-prestandamått i Application Insights


Utforska prestandamått för Linux-system i [Programinsikter](../../azure-monitor/app/app-insights-overview.md), installera [insamlade](https://collectd.org/), tillsammans med dess Application Insights-plugin-programmet. Den här lösningen med öppen källkod samlar in olika system- och statistik.

Normalt använder insamlade om du redan har [instrumenterats Java-webbtjänsten med Application Insights][java]. Det ger dig mer data som hjälper dig att förbättra appens prestanda- och diagnostikproblem. 

## <a name="get-your-instrumentation-key"></a>Hämta din instrumentationsnyckel
I den [Microsoft Azure-portalen](https://portal.azure.com)öppnar den [Application Insights](../../azure-monitor/app/app-insights-overview.md) resurs där du vill att data ska visas. (Eller [skapa en ny resurs](../../azure-monitor/app/create-new-resource.md ).)

Ta en kopia av instrumenteringsnyckeln som identifierar resursen.

![Bläddra igenom alla, öppna din resurs i Essentials listrutan Välj och kopierar Instrumenteringsnyckeln.](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Installera insamlade och plugin-programmet
På din server-datorer för Linux:

1. Installera [insamlade](https://collectd.org/) version 5.4.0 eller senare.
2. Ladda ned den [-plugin-programmet Application Insights insamlade-skrivaren](https://aka.ms/aijavasdk). Notera versionsnumret.
3. Kopiera JAR-plugin-programmet i `/usr/share/collectd/java`.
4. Redigera `/etc/collectd/collectd.conf`:
   * Se till att [plugin-programmet för Java](https://collectd.org/wiki/index.php/Plugin:Java) är aktiverad.
   * Uppdatera JVMArg för java.class.path för att inkludera följande JAR-filen. Uppdatera versionsnumret för att motsvara det konto som du laddade ned:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Lägg till det här kodfragmentet använder Instrumenteringsnyckeln från din resurs:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Här är en del av en exempel-konfigurationsfil:

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

Konfigurera andra [insamlade plugin-program](https://collectd.org/wiki/index.php/Table_of_Plugins), som kan samla in olika data från olika källor.

Starta om insamlade enligt dess [manuell](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Visa data i Application Insights
Öppna i Application Insights-resursen, [mått och Lägg till diagram][metrics], att välja de mått som du vill se från anpassad kategori.

Som standard sammanställs måtten för samtliga värddatorer där mått som samlades in. Om du vill visa mått per värd, i bladet diagrammet, aktiverar gruppering och sedan välja att gruppera efter insamlade-värd.

## <a name="to-exclude-upload-of-specific-statistics"></a>Att undanta uppladdning av specifika statistik
Plugin-programmet Application Insights skickar alla data som samlas in av alla aktiverade insamlade läsa-plugin-program som standard. 

Vill utesluta specifika plugin-program eller datakällor data:

* Redigera konfigurationsfilen. 
* I `<Plugin ApplicationInsightsWriter>`, lägger du till direktivet rader så här:

| Direktiv | Verkan |
| --- | --- |
| `Exclude disk` |Undanta alla data som samlas in av den `disk` plugin-programmet |
| `Exclude disk:read,write` |Exkludera datakällor med namnet `read` och `write` från den `disk` plugin-programmet. |

Separata direktiv med en ny rad.

## <a name="problems"></a>Har du problem?
*Jag ser inte data i portalen*

* Öppna [Search] [ diagnostic] att se om rådatahändelser är här. Ibland kan de ta längre tid att visas i metrics explorer.
* Du kan behöva [ange brandväggsundantag för utgående data](../../azure-monitor/app/ip-addresses.md)
* Aktivera spårning i Application Insights-plugin-programmet. Lägg till följande rad i `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Öppna en terminal och börja insamlade utförligt läge, att den rapporterar problem skulle uppstå:
  * `sudo collectd -f`

## <a name="known-issue"></a>Kända problem

Plugin-programmet Application Insights skriva är inte kompatibel med vissa Läs plugin-program. Vissa plugin-program skickar ibland ”NaN” där Application Insights-plugin-programmet förväntar sig ett Flyttalsnummer.

Symptom: Insamlade loggen visar fel som innehåller ”AI:... SyntaxError: Oväntad i token N ”.

Lösning: Undanta data som samlas in av plugin-program för problemet skrivning. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


