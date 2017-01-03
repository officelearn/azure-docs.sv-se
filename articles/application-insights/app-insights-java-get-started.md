---
title: Analysera Java-webbappar med Azure Application Insights | Microsoft Docs
description: "Övervakning av programprestanda för Java-webbappar med Application Insights. "
services: application-insights
documentationcenter: java
author: harelbr
manager: douge
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/02/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 4fc4561516490b9b285220e7ae688bf97384fe6e
ms.openlocfilehash: c900840e419c06b70e3a2f53a6aa8314145324fe


---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Komma igång med Application Insights i ett Java-webbprojekt


[Application Insights](https://azure.microsoft.com/services/application-insights/) är en utökningsbar analystjänst för webbutvecklare som hjälper dig att förstå prestanda och användningen av ditt liveprogram. Tjänsten hjälper dig att [identifiera och diagnostisera prestandaproblem och undantag](app-insights-detect-triage-diagnose.md) samt att [skriva kod][api] för att se vad användarna gör med din app.

![exempeldata](./media/app-insights-java-get-started/5-results.png)

Application Insights har stöd för Java-appar som körs på Linux, Unix eller Windows.

Du behöver:

* Oracle JRE 1.6 eller senare eller Zulu JRE 1.6 eller senare
* En prenumeration på [Microsoft Azure](https://azure.microsoft.com/).

*Om du har en webbapp som redan är aktiv kan du följa den alternativa proceduren [för att lägga till SDK vid körningen på webbservern](app-insights-java-live.md). Med det alternativet undviker du att behöva återskapa koden, men du kan inte skriva kod för att spåra användaraktivitet.*

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Hämta en Application Insights-instrumenteringsnyckel
1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).
2. Skapa en Application Insights-resurs. Ange programtypen till Java-webbapp.
   
    ![Fyll i ett namn, välj Java-webbapp och klicka på Skapa](./media/app-insights-java-get-started/02-create.png)
3. Leta upp instrumenteringsnyckeln för den nya resursen. Du kommer att behöva klistra in den här nyckeln i projektkoden inom kort.
   
    ![I den nya resursöversikten klickar du på Egenskaper och kopierar instrumenteringsnyckeln.](./media/app-insights-java-get-started/03-key.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Lägga till Application Insights SDK för Java till ditt projekt
*Välj lämplig metod för ditt projekt.*

#### <a name="if-youre-using-eclipse-to-create-a-maven-or-dynamic-web-project-"></a>Om du använder Eclipse för att skapa ett Maven- eller Dynamic Web-projekt …
Använd [plugin-programmet Application Insights SDK för Java][eclipse].

#### <a name="if-youre-using-maven"></a>Om du använder Maven …
Om ditt projekt redan har konfigurerats för utveckling med Maven sammanfogar du följande kod i pom.xml-filen.

Uppdatera sedan projektberoendena för att få binärfilerna.

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[1.0,)</version>
      </dependency>
    </dependencies>
```

* *Stöter du på utvecklingsfel eller fel relaterade till verifieringen av kontrollsummor?* Prova att använda en specifik version, t.ex.: `<version>1.0.n</version>`. Du hittar den senaste versionen i [viktig information om SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) eller i våra [Maven-artefakter](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Behöver du uppdatera till en ny SDK?* Uppdatera ditt projekts beroenden.

#### <a name="if-youre-using-gradle"></a>Om du använder Gradle …
Om ditt projekt redan har konfigurerats för utveckling med Gradle sammanfogar du följande kod i build.gradle-filen.

Uppdatera sedan projektberoendena för att få binärfilerna.

```JSON

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }
```

* *Stöter du på utvecklingsfel eller fel relaterade till verifieringen av kontrollsummor? Prova att använda en specifik version, t.ex.: *`version:'1.0.n'`. *Du hittar den senaste versionen i [viktig information om SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).*
* *Så här uppdaterar du till ett nytt SDK*
  * Uppdatera ditt projekts beroenden.

#### <a name="otherwise-"></a>Eller …
Lägg till SDK manuellt:

1. Ladda ned [Application Insights SDK för Java](https://aka.ms/aijavasdk).
2. Extrahera binärfilerna från ZIP-filen och lägg till dem i projektet.

### <a name="questions"></a>Frågor …
* *Vad är relationen mellan `-core` och `-web`-komponenterna i ZIP-filen?*
  
  * `applicationinsights-core` ger dig det avskalade API:et. Du behöver alltid ha den här komponenten.
  * `applicationinsights-web` ger dig mått som spårar antalet HTTP-förfrågningar och svarstider. Du kan utelämna den här komponenten om du inte vill att den här telemetrin ska samlas in automatiskt. Till exempel om du vill skriva din egen.
* *Så här uppdaterar du SDK när du publicerar ändringar*
  
  * Ladda ned senaste [Application Insights SDK för Java](https://aka.ms/qqkaq6) och ersätt det gamla.
  * Ändringar beskrivs i [viktig information om SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-application-insights-xml-file"></a>3. Lägga till en XML-fil för Application Insights
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
* Händelsekorrelation är ett tillägg till komponenten HTTP-begäran. Det tilldelar en identifierare för varje förfrågan som tas emot av servern och lägger till denna identifierare som en egenskap för alla objekt i telemetrin som egenskapen Operation.Id. Detta gör att du kan korrelera telemetrin som är associerad med varje begäran genom att ange ett filter i [Diagnostiksökning][diagnostic].
* Application Insights-nyckeln kan skickas dynamiskt från Azure Portal som en systemegenskap (-DAPPLICATION_INSIGHTS_IKEY = your_ikey). Om det finns inte någon definierad egenskap sker sökning efter miljövariabeln (APPLICATION_INSIGHTS_IKEY) i Azure App-inställningarna. Om båda egenskaperna är odefinierade används som standard InstrumentationKey från ApplicationInsights.xml. Med hjälp av den här sekvensen kan du på ett dynamiskt sätt hantera olika InstrumentationKeys för olika miljöer.

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Olika sätt att konfigurera instrumenteringsnyckeln på
Application Insights SDK:n söker efter nyckeln i följande ordning:

1. Systemegenskap: -DAPPLICATION_INSIGHTS_IKEY=your_ikey
2. Miljövariabel: APPLICATION_INSIGHTS_IKEY
3. Konfigurationsfil: ApplicationInsights.xml

Du kan också [ange den i koden](app-insights-api-custom-events-metrics.md#ikey):

```Java

    telemetryClient.InstrumentationKey = "...";
```

## <a name="4-add-an-http-filter"></a>4. Lägga till ett HTTP-filter
Det sista konfigurationssteget gör att komponenten HTTP-begäran kan logga varje webbegäran. (Krävs inte om du bara vill ha det avskalade API:et.)

Leta upp och öppna filen web.xml i projektet och sammanfoga följande kod under webbappens nod, där dina programfiler är konfigurerade.

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

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Om du använder Spring Web MVC 3.1 or later
Redigera dessa element i *-servlet.xml för att ta med Application Insights-paketet:

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Om du använder Struts 2
Lägg till det här objektet i Struts-konfigurationsfilen (vanligtvis struts.xml eller struts-default.xml):

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

(Om det finns spärrar som har definierats i en standardstack lägger du bara till spärren till den stacken.)

## <a name="5-run-your-application"></a>5. Köra ditt program
Kör programmet i felsökningsläge på utvecklingsdatorn eller publicera det till servern.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Visa telemetrin i Application Insights
Gå tillbaka till Application Insights-resursen på [Microsoft Azure Portal](https://portal.azure.com).

Data om HTTP-förfrågningar visas på översiktsbladet. (Om informationen inte visas väntar du några sekunder och klickar på Uppdatera.)

![exempeldata](./media/app-insights-java-get-started/5-results.png)

[Lär dig mer om mätvärden.][metrics]

Klicka dig vidare i diagrammen om du vill visa mer detaljerade aggregerade mätvärden.

![](./media/app-insights-java-get-started/6-barchart.png)

> Application Insights förutsätter att formatet för HTTP-begäranden för MVC-program är: `VERB controller/action`. `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` och `GET Home/Product/sdf96vws` grupperas t.ex. i `GET Home/Product`. Denna gruppering gör det möjligt att skapa meningsfulla förfrågningsaggregeringar, t.ex. antal förfrågningar och genomsnittlig körningstid för förfrågningarna.
> 
> 

### <a name="instance-data"></a>Instansdata
Klicka dig vidare inom en specifik begärandetyp om du vill visa enskilda instanser. 

Två typer av data visas i Application Insights: aggregerade data, som lagras och visas som medelvärden, antal och belopp; och instansdata, som är enskilda rapporter över HTTP-begäranden, undantag, sidvisningar eller anpassade händelser.

När du visar egenskaperna för en begäran kan du se telemetrihändelserna som är associerade med den, till exempel begäranden och undantag.

![](./media/app-insights-java-get-started/7-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics: Kraftfullt frågespråk
Allt eftersom du ackumulerar mer data kan du köra frågor både för att aggregera data och för att hitta enskilda instanser.  [Analytics](app-insights-analytics.md) är ett kraftfullt verktyg både för att bättre förstå prestanda och användning, och för diagnostikändamål.

![Exempel med Analytics](./media/app-insights-java-get-started/025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Installera din app på servern
Publicera appen på servern, låt användarna använda den och se hur telemetrin visas på portalen.

* Kontrollera att din brandvägg tillåter programmet att skicka telemetri till följande portar:
  
  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Om utgående trafik måste dirigeras via en brandvägg definierar du systemegenskaper `http.proxyHost` och `http.proxyPort`. 

* Installera följande på Windows-servrar:
  
  * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)
    
    (Den här komponenten gör det möjligt att använda prestandaräknare.)


## <a name="exceptions-and-request-failures"></a>Fel relaterade till begäranden och undantag
Ohanterade undantag samlas in automatiskt:

![Öppna Inställningar, Fel](./media/app-insights-java-get-started/21-exceptions.png)

Om du vill samla in data om andra undantag kan du välja mellan två alternativ:

* [Infoga anrop till trackException() i din kod][apiexceptions]. 
* [Installera Java-agenten på servern](app-insights-java-agent.md). Du anger de metoder som du vill övervaka.

## <a name="monitor-method-calls-and-external-dependencies"></a>Övervaka metodanrop och externa beroenden
[Installera Java-agenten](app-insights-java-agent.md) om du vill logga angivna interna metoder och anrop som görs via JDBC, med tidsinställningsdata.

## <a name="performance-counters"></a>Prestandaräknare
Öppna **Inställningar**, **Servrar**, om du vill se ett utbud av prestandaräknare.

![](./media/app-insights-java-get-started/11-perf-counters.png)

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

Dina prestandaräknare visas som anpassade mått i [Metrics Explorer][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Unix-prestandaräknare
* [Installera collectd med Application Insights-plugin-programmet](app-insights-java-collectd.md) om du vill samla in en mängd olika system- och nätverksdata.

## <a name="get-user-and-session-data"></a>Samla in användar- och sesionsdata
Du skickar telemetri från webbservern. Men för att få en heltäckande bild av ditt program kan du lägga till ännu mer övervakning:

* [Lägg till telemetri till webbsidor][usage] om du vill övervaka sidvisningar och användarmått.
* [Konfigurera webbtester][availability] för att se till att ditt program är aktivt och effektivt.

## <a name="capture-log-traces"></a>Samla in loggspårningar
Du kan använda Application Insights om du vill arbeta med loggar från Log4J, Logback eller andra loggningsramverk. Du kan korrelera loggarna med HTTP-förfrågningar och annan telemetri. [Lär dig mer][javalogs].

## <a name="send-your-own-telemetry"></a>Skicka din egen telemetri
Nu när du har installerat SDK kan du använda API:et för att skicka din egen telemetri.

* [Spåra anpassade händelser och mått][api] och ta reda på vad användare gör med ditt program.
* [Sök efter händelser och loggar][diagnostic] för att diagnostisera problem.

## <a name="availability-web-tests"></a>Webbtester för tillgänglighet
Application Insights kan testa din webbplats med jämna mellanrum för att kontrollera att tjänsten är tillgänglig och att den svarar. [Du konfigurerar inställningarna][availability] genom att klicka på Webbtest.

![Klicka först på Webbtester och sedan på Lägg till webbtest](./media/app-insights-java-get-started/31-config-web-test.png)

Du kan visa diagram över svarstider, samt få e-postaviseringar om platsen kraschar.

![Exempel på webbtest](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[Läs mer om webbtester för tillgänglighet.][availability] 

## <a name="questions-problems"></a>Frågor? Har du problem?
[Felsöka Java](app-insights-java-troubleshoot.md)

## <a name="next-steps"></a>Nästa steg
* [Övervaka beroende anrop](app-insights-java-agent.md)
* [Övervaka Unix-prestandaräknare](app-insights-java-collectd.md)
* Lägg till [övervakning till dina webbsidor](app-insights-javascript.md) för att övervaka sidans belastning, AJAX-anrop, webbläsarundantag.
* Skriv [anpassad telemetri](app-insights-api-custom-events-metrics.md) att spåra användningen i webbläsaren eller på servern.
* Skapa [instrumentpaneler](app-insights-dashboards.md) för att samordna nyckeldiagram för övervakning av systemet.
* Använd [Analytics](app-insights-analytics.md) för kraftfulla frågor via telemetri från din app
* Mer information finns på [Java Developer Center](/develop/java/).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=Jan17_HO1-->


