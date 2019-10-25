---
title: Analysera Java-webbappar med Azure Application Insights | Microsoft Docs
description: 'Övervakning av programprestanda för Java-webbappar med Application Insights. '
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 28fbb5fcfba2b346d0519dec79e538b1e513b7dd
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817138"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Kom igång med Application Insights i ett Java-webbprojekt

[Application Insights](https://azure.microsoft.com/services/application-insights/) är en utökningsbar analystjänst för webbutvecklare som hjälper dig att förstå prestanda och användningen av ditt liveprogram. Använd den för att [automatiskt begära instrument, spåra beroenden och samla in prestanda räknare](auto-collect-dependencies.md#java), diagnostisera prestanda problem och undantag och [skriva kod][api] för att spåra vad användarna gör med din app. 

![Skärm bild av exempel data för översikt](./media/java-get-started/overview-graphs.png)

Application Insights har stöd för Java-appar som körs på Linux, Unix eller Windows.

Du behöver:

* Java 7 eller senare
* En prenumeration på [Microsoft Azure](https://azure.microsoft.com/).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Hämta en Application Insights Instrumentation-nyckel
1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).
2. Skapa en Application Insights-resurs. Ange programtypen till Java-webbapp.

3. Leta upp instrumenteringsnyckeln för den nya resursen. Du kommer att behöva klistra in den här nyckeln i projektkoden inom kort.

    ![I den nya resursöversikten klickar du på Egenskaper och kopierar instrumenteringsnyckeln.](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Lägg till Application Insights SDK för java i projektet
*Välj lämplig metod för ditt projekt.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Om du använder Maven … <a name="maven-setup" />
Om ditt projekt redan har konfigurerats för utveckling med Maven sammanfogar du följande kod i pom.xml-filen.

Uppdatera sedan projektberoendena för att få binärfilerna.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Om du använder Gradle … <a name="gradle-setup" />
Om ditt projekt redan har konfigurerats för utveckling med Gradle sammanfogar du följande kod i build.gradle-filen.

Uppdatera sedan projektberoendena för att få binärfilerna.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Om du hanterar beroenden manuellt gäller följande:
Hämta den [senaste versionen](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) och kopiera nödvändiga filer till projektet, så att tidigare versioner ersätts.

### <a name="questions"></a>Frågor …
* *Vad är förhållandet mellan `-web-auto`, `-web` och `-core` komponenter?*
  * `applicationinsights-web-auto` ger dig mått som spårar antalet begär Anden och svars tider för HTTP-servlet genom att automatiskt registrera Application Insights servlet-filtret vid körning.
  * `applicationinsights-web` ger dig också mått som spårar antalet begär Anden och svars tider för HTTP-servlet, men kräver manuell registrering av Application Insights servlet-filtret i ditt program.
  * `applicationinsights-core` ger dig bara det Bare-API: t, till exempel om ditt program inte är servlet-baserat.
  
* *Hur uppdaterar jag SDK till den senaste versionen?*
  * Om du använder Gradle eller Maven...
    * Uppdatera build-filen för att ange den senaste versionen.
  * Om du hanterar beroenden manuellt gäller följande:
    * Ladda ned senaste [Application Insights SDK för Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) och ersätt det gamla. Ändringar beskrivs i [viktig information om SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Lägg till en ApplicationInsights. XML-fil
Lägg till ApplicationInsights.xml i resursmappen i ditt projekt eller se till att den läggs till i sökvägen för projektets distributionsklass. Kopiera följande XML-kod till den.

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
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Konfigurationsfilen kan också finnas på valfri plats som är tillgänglig för ditt program.  Systemegenskapen `-Dapplicationinsights.configurationDirectory` anger den katalog där ApplicationInsights.xml finns. Exempel: En konfigurationsfil som finns på `E:\myconfigs\appinsights\ApplicationInsights.xml` konfigureras med egenskapen `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Instrumenteringsnyckeln skickas tillsammans med alla element i telemetrin och uppmanar Application Insights att visa den i din resurs.
* Komponenten HTTP-begäran är valfri. Den skickar automatiskt telemetri om förfrågningar och svarstider till portalen.
* Händelsekorrelation är ett tillägg till komponenten HTTP-begäran. Det tilldelar en identifierare för varje förfrågan som tas emot av servern och lägger till denna identifierare som en egenskap för alla objekt i telemetrin som egenskapen Operation.Id. Det gör att du kan korrelera Telemetrin som är associerad med varje begäran genom att ange ett filter i [diagnostisk sökning][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Olika sätt att konfigurera instrumenteringsnyckeln på
Application Insights SDK:n söker efter nyckeln i följande ordning:

1. System egenskap:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Miljö variabel: APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurationsfil: ApplicationInsights.xml

Du kan också [ange den i koden](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. Lägg till agent

[Installera Java-agenten](java-agent.md) för att avbilda utgående HTTP-anrop, JDBC-frågor, program loggning och bättre namngivning av åtgärder.

## <a name="5-run-your-application"></a>5. kör programmet
Kör programmet i felsökningsläge på utvecklingsdatorn eller publicera det till servern.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Visa din telemetri i Application Insights
Gå tillbaka till Application Insights-resursen på [Microsoft Azure Portal](https://portal.azure.com).

Data om HTTP-förfrågningar visas på översiktsbladet. (Om informationen inte visas väntar du några sekunder och klickar på Uppdatera.)

![Skärm bild av exempel data för översikt](./media/java-get-started/overview-graphs.png)

[Lär dig mer om mätvärden.][metrics]

Klicka dig vidare i diagrammen om du vill visa mer detaljerade aggregerade mätvärden.

![Fönstret Application Insightss problem med diagram](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Instansdata
Klicka dig vidare inom en specifik begärandetyp om du vill visa enskilda instanser.

![Detaljgranska i en speciell exempel vy](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics: Kraftfullt frågespråk
Allt eftersom du ackumulerar mer data kan du köra frågor både för att aggregera data och för att hitta enskilda instanser.  [Analytics](../../azure-monitor/app/analytics.md) är ett kraftfullt verktyg både för att bättre förstå prestanda och användning, och för diagnostikändamål.

![Exempel med Analytics](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Installera din app på servern
Publicera appen på servern, låt användarna använda den och se hur telemetrin visas på portalen.

* Kontrollera att din brandvägg tillåter programmet att skicka telemetri till följande portar:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Om utgående trafik måste dirigeras via en brandvägg definierar du systemegenskaper `http.proxyHost` och `http.proxyPort`.

* Installera följande på Windows-servrar:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Den här komponenten gör det möjligt att använda prestandaräknare.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service config (våren boot)

Våren Boot-appar som körs på Windows kräver ytterligare konfiguration för att köras på Azure App Services. Ändra **Web. config** och Lägg till följande:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Fel relaterade till begäranden och undantag
Ohanterade undantag och begär Anden som Miss lyckas samlas in automatiskt av Application Insights webb filter.

Om du vill samla in data om andra undantag kan du [Infoga anrop till trackException () i din kod][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Övervaka metodanrop och externa beroenden
[Installera Java-agenten](java-agent.md) om du vill logga angivna interna metoder och anrop som görs via JDBC, med tidsinställningsdata.

Och för automatisk namngivning av åtgärder.

## <a name="w3c-distributed-tracing"></a>Distribuerad W3C-spårning

Application Insights Java SDK stöder nu [distribuerad W3C-spårning](https://w3c.github.io/trace-context/).

Den inkommande SDK-konfigurationen förklaras ytterligare i vår artikel om [korrelation](correlation.md#telemetry-correlation-in-the-java-sdk).

Den utgående SDK-konfigurationen definieras i filen [AI-agent. XML](java-agent.md) .

## <a name="performance-counters"></a>Prestandaräknare
Öppna **Undersök**, **statistik**, om du vill se en serie prestanda räknare.

![Skärm bild av mått fönstret med process privata byte valda](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Anpassa samlingen med prestandaräknare
Om du vill inaktivera datainsamlingen från standarduppsättningen med prestandaräknare lägger du till följande kod under rotnoden i filen ApplicationInsights.xml:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Samla in data från fler prestandaräknare
Du kan ange ytterligare prestandaräknare som du vill samla in data från.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-räknare (exponeras av Java Virtual Machine)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – Namnet visas på Application Insights-portalen.
* `objectName` – JMX-objektnamnet.
* `attribute` – Attributet för JMX-objektnamnet som ska hämtas
* `type` (valfritt) – Typen för JMX-objektets attribut:
  * Standardvärde: en enkel typ, till exempel int eller long.
  * `composite`: prestandaräknardata har formatet ”Attribute.Data”
  * `tabular`: prestandaräknardata har tabellradsformat

#### <a name="windows-performance-counters"></a>Windows-prestandaräknare
Varje [Windows-prestandaräknare](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) är medlem i en kategori (på samma sätt som ett fält är medlem i en klass). Kategorier kan antingen vara globala eller ha numrerade eller namngivna instanser.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Namnet som visas på Application Insights-portalen.
* categoryName – Kategorin av prestandaräknare (prestandaobjekt) som den här prestandaräknaren är associerad med.
* counterName – Namnet på prestandaräknaren.
* instanceName – Namnet på instansen av prestandaräknarkategorin eller en tom sträng ("") om kategorin innehåller en enda instans. Om categoryName är Process och den prestandaräknare som du vill samla in data från hör till den aktuella JVM-processen som din app körs i anger du `"__SELF__"`.

### <a name="unix-performance-counters"></a>Unix-prestandaräknare
* [Installera collectd med Application Insights-plugin-programmet](java-collectd.md) om du vill samla in en mängd olika system- och nätverksdata.

## <a name="get-user-and-session-data"></a>Samla in användar- och sesionsdata
Du skickar telemetri från webbservern. Men för att få en heltäckande bild av ditt program kan du lägga till ännu mer övervakning:

* [Lägg till telemetri till dina webb sidor][usage] för att övervaka sidvyer och användar mått.
* [Konfigurera webbtester][availability] för att se till att ditt program hålls Live och svarar.

## <a name="send-your-own-telemetry"></a>Skicka din egen telemetri
Nu när du har installerat SDK kan du använda API:et för att skicka din egen telemetri.

* [Spåra anpassade händelser och mått][api] för att lära dig vad användarna gör med ditt program.
* [Sök efter händelser och loggar][diagnostic] för att diagnostisera problem.

## <a name="availability-web-tests"></a>Programtester för tillgänglighet
Application Insights kan testa din webbplats med jämna mellanrum för att kontrollera att tjänsten är tillgänglig och att den svarar.

[Läs mer om hur du konfigurerar webb test för tillgänglighet.][availability]

## <a name="questions-problems"></a>Har du några frågor? Problem?
[Felsöka Java](java-troubleshoot.md)

## <a name="next-steps"></a>Nästa steg
* [Övervaka beroende anrop](java-agent.md)
* [Övervaka Unix-prestandaräknare](java-collectd.md)
* Lägg till [övervakning till dina webbsidor](javascript.md) för att övervaka sidans belastning, AJAX-anrop, webbläsarundantag.
* Skriv [anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md) att spåra användningen i webbläsaren eller på servern.
* Använd [Analytics](../../azure-monitor/app/analytics.md) för kraftfulla frågor via telemetri från din app
* Mer information finns i [Azure för Java-utvecklare](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
