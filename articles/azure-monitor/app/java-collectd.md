---
title: Övervaka Java-webbappprestanda på Linux - Azure | Microsoft-dokument
description: Utökad programprestandaövervakning av din Java-webbplats med plugin-programmet CollectD för Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 687f97c305bffdfb408feb314ccded4f93ac574a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660741"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>insamlade: Linux-prestandamått i Application Insights


Om du vill utforska Linux-systemprestandamått i [Application Insights](../../azure-monitor/app/app-insights-overview.md)installerar du [insamlade](https://collectd.org/)tillsammans med plugin-programmet Application Insights. Denna lösning med öppen källkod samlar in olika system- och nätverksstatistik.

Vanligtvis använder du samlad om du redan har [instrumenterat din Java-webbtjänst med Application Insights][java]. Det ger dig mer data som hjälper dig att förbättra appens prestanda eller diagnostisera problem. 

## <a name="get-your-instrumentation-key"></a>Hämta instrumenteringsnyckeln
Öppna application [insights-resursen](../../azure-monitor/app/app-insights-overview.md) där du vill att data ska visas i [Microsoft Azure-portalen.](https://portal.azure.com) (Eller [skapa en ny resurs](../../azure-monitor/app/create-new-resource.md ).)

Ta en kopia av instrumenteringsnyckeln, som identifierar resursen.

![Bläddra bland alla, öppna resursen och välj sedan instrumenteringsnyckeln i listrutan Essentials](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Installera samlad och plugin-programmet
På dina Linux-servermaskiner:

1. Installera [samlad](https://collectd.org/) version 5.4.0 eller senare.
2. Ladda ner [Application Insights insamlade författare plugin](https://github.com/microsoft/ApplicationInsights-Java/tree/master/collectd/src/main/java/com/microsoft/applicationinsights/collectd/internal). Notera versionsnumret.
3. Kopiera plugin JAR `/usr/share/collectd/java`till .
4. Redigera: `/etc/collectd/collectd.conf`
   * Kontrollera att [Java-insticksprogrammet](https://collectd.org/wiki/index.php/Plugin:Java) är aktiverat.
   * Uppdatera JVMArg för java.class.path för att inkludera följande JAR. Uppdatera versionsnumret så att det matchar det du hämtade:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Lägg till det här kodavsnittet med hjälp av instrumenteringsnyckeln från resursen:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Här är en del av en exempelkonfigurationsfil:

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

Konfigurera andra [insamlade plugins](https://collectd.org/wiki/index.php/Table_of_Plugins), som kan samla in olika data från olika källor.

Starta om samlas in enligt dess [manual](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Visa data i Application Insights
Öppna mått och lägg [till diagram][metrics]i application insights-resursen och välj de mått som du vill se från kategorin Anpassad.

Som standard aggregeras måtten över alla värddatorer från vilka måtten samlades in. Om du vill visa måtten per värd aktiverar du Gruppering i bladet Diagraminformation och väljer sedan att gruppera efter CollectD-Host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Så här utesluter du överföring av specifik statistik
Som standard skickar Application Insights plugin alla data som samlats in av alla aktiverade insamlade "läs" plugins. 

Så här utesluter du data från specifika insticksprogram eller datakällor:

* Redigera konfigurationsfilen. 
* I `<Plugin ApplicationInsightsWriter>`, lägga till direktivrader som denna:

| Direktiv | Verkan |
| --- | --- |
| `Exclude disk` |Exkludera alla `disk` data som samlas in av insticksprogrammet |
| `Exclude disk:read,write` |Uteslut de `read` källor `write` som `disk` namnges och från insticksprogrammet. |

Särdirektiv med en ny linje.

## <a name="problems"></a>Har du problem?
*Jag ser inte data i portalen*

* Öppna [Sök][diagnostic] för att se om de råa händelserna har anlänt. Ibland tar det längre tid att visas i statistikutforskaren.
* Du kan behöva [ange brandväggsund undantag för utgående data](../../azure-monitor/app/ip-addresses.md)
* Aktivera spårning i plugin-programmet Application Insights. Lägg till `<Plugin ApplicationInsightsWriter>`den här raden inom:
  * `SDKLogger true`
* Öppna en terminal och börja samlas i utförligt läge för att se eventuella problem som den rapporterar:
  * `sudo collectd -f`

## <a name="known-issue"></a>Kända problem

Plugin:en av Application Insights Write är inkompatibel med vissa läsinsticksprogram. Vissa plugins skickar ibland "NaN" där Application Insights plugin förväntar sig ett flyttalsnummer.

Symptom: Den insamlade loggen visar fel som inkluderar "AI: ... SyntaxError: Oväntad token N".

Lösning: Exkludera data som samlats in av problemet Skriv plugins. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


