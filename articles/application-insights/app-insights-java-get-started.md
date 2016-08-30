<properties
    pageTitle="Analysera Java-webbappar med Application Insights | Microsoft Azure"
    description="Övervaka prestanda och användningen av din Java-webbplats med Application Insights. "
    services="application-insights"
    documentationCenter="java"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/12/2016"
    ms.author="awills"/>

# Komma igång med Application Insights i ett Java-webbprojekt

*Application Insights finns endast som förhandsversion.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

[Application Insights](https://azure.microsoft.com/services/application-insights/) är en utökningsbar analystjänst som hjälper dig att förstå prestanda och användningen av ditt liveprogram. Tjänsten hjälper dig att [identifiera och diagnostisera prestandaproblem och undantag](app-insights-detect-triage-diagnose.md) samt att [skriva kod][api] för att se vad användarna gör med din app.

![exempeldata](./media/app-insights-java-get-started/5-results.png)

Application Insights har stöd för Java-appar som körs på Linux, Unix eller Windows.

Du behöver:

* Oracle JRE 1.6 eller senare eller Zulu JRE 1.6 eller senare
* En prenumeration på [Microsoft Azure](https://azure.microsoft.com/). (Du kan börja med [den kostnadsfria utvärderingsversionen](https://azure.microsoft.com/pricing/free-trial/).)

*Om du har en webbapp som redan är aktiv kan du följa den alternativa proceduren för att [lägga till SDK vid körningen på webbservern](app-insights-java-live.md). Med det alternativet undviker du att behöva återskapa koden, men du kan inte skriva kod för att spåra användaraktivitet.*


## 1. Hämta en Application Insights-instrumenteringsnyckel

1. Logga in på [Microsoft Azure-portalen](https://portal.azure.com).
2. Skapa en ny Application Insights-resurs.

    ![Klicka på + och välj Application Insights](./media/app-insights-java-get-started/01-create.png)
3. Ange programtypen till Java-webbapp.

    ![Fyll i ett namn, välj Java-webbapp och klicka på Skapa](./media/app-insights-java-get-started/02-create.png)
4. Leta upp instrumenteringsnyckeln för den nya resursen. Du ska snart klistra in den i projektkoden.

    ![I den nya resursöversikten klickar du på Egenskaper och kopierar instrumenteringsnyckeln.](./media/app-insights-java-get-started/03-key.png)

## 2. Lägga till Application Insights SDK för Java till ditt projekt

*Välj lämplig metod för ditt projekt.*

#### Om du använder Eclipse för att skapa ett Maven- eller Dynamic Web-projekt …

Använd [plugin-programmet Application Insights SDK för Java][eclipse].

#### Om du använder Maven …

Om ditt projekt redan har konfigurerats för utveckling med Maven sammanfogar du följande kod i pom.xml-filen.

Uppdatera sedan projektberoendena för att få binärfilerna.

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


* *Stöter du på utvecklingsfel eller fel relaterade till verifieringen av kontrollsummor?* Prova att använda en specifik version, t.ex.: `<version>1.0.n</version>`. Du hittar den senaste versionen i [viktig information om SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) eller i våra [Maven-artefakter](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Behöver du uppdatera till en ny SDK?* Uppdatera ditt projekts beroenden.

#### Om du använder Gradle …

Om ditt projekt redan har konfigurerats för utveckling med Gradle sammanfogar du följande kod i build.gradle-filen.

Uppdatera sedan projektberoendena för att få binärfilerna.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }

* *Stöter du på utvecklingsfel eller fel relaterade till verifieringen av kontrollsummor? Prova att använda en specifik version, t.ex.: *`version:'1.0.n'`. *Du hittar den senaste versionen i [viktig information om SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).*
* *Så här uppdaterar du till ett nytt SDK*
 * Uppdatera ditt projekts beroenden.

#### Eller …

Lägg till SDK manuellt:

1. Ladda ned [Application Insights SDK för Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html).
2. Extrahera binärfilerna från ZIP-filen och lägg till dem i projektet.

### Frågor …

* *Vad är relationen mellan `-core`- och `-web`-komponenterna i ZIP-filen?*

 * `applicationinsights-core` ger dig det avskalade API:et. Du behöver alltid detta.
 * `applicationinsights-web` ger dig mått som spårar antalet HTTP-förfrågningar och svarstider. Du kan utelämna detta om du inte vill att den här telemetrin ska samlas in automatiskt. Till exempel om du vill skriva din egen.

* *Så här uppdaterar du SDK när du publicerar ändringar*
 * Ladda ned senaste [Application Insigths SDK för Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.zip) och ersätt det gamla.
 * Ändringar beskrivs i [viktig information om SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).



## 3. Lägga till en XML-fil för Application Insights

Lägg till ApplicationInsights.xml i resursmappen i ditt projekt eller se till att den läggs till i sökvägen för projektets distributionsklass. Kopiera följande XML-kod till den.

Ersätt instrumenteringsnyckeln som du fick från Azure-portalen.

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


* Instrumenteringsnyckeln skickas tillsammans med alla element i telemetrin och uppmanar Application Insights att visa den i din resurs.
* Komponenten HTTP-begäran är valfri. Den skickar automatiskt telemetri om förfrågningar och svarstider till portalen.
* Händelsekorrelation är ett tillägg till komponenten HTTP-begäran. Det tilldelar en identifierare för varje begäran som tas emot av servern och lägger till den som en egenskap för alla objekt i telemetrin som egenskapen ”Operation.Id”. Detta gör att du kan korrelera telemetrin som är associerad med varje begäran genom att ange ett filter i [Diagnostiksökning][diagnostic].

## 4. Lägga till ett HTTP-filter

Det sista konfigurationssteget gör att komponenten HTTP-begäran kan logga varje webbegäran. (Krävs inte om du bara vill ha det avskalade API:et.)

Leta upp och öppna filen web.xml i projektet och sammanfoga följande kod under webbappens nod, där dina programfiler är konfigurerade.

För bästa resultat bör filtret mappas före alla andra filter.

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

#### Om du använder MVC 3.1 eller senare

Redigera dessa element så att Application Insights-paketet tas med:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Om du använder Struts 2

Lägg till det här objektet i Struts-konfigurationsfilen (vanligtvis struts.xml eller struts-default.xml):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(Om det finns spärrar som har definierats i en standardstack lägger du bara till spärren till den stacken.)



## 5. Köra ditt program

Kör programmet i felsökningsläge på utvecklingsdatorn eller publicera det till servern.

## 6. Visa telemetrin i Application Insights


Gå tillbaka till Application Insights-resursen på [Microsoft Azure-portalen](https://portal.azure.com).

Data om HTTP-begäranden visas på översiktsbladet. (Om informationen inte visas väntar du några sekunder och klickar på Uppdatera.)

![exempeldata](./media/app-insights-java-get-started/5-results.png)

[Lär dig mer om mått.][metrics]

Klicka dig vidare i diagrammen om du vill visa mer detaljerade aggregerade mätvärden.

![](./media/app-insights-java-get-started/6-barchart.png)

> Application Insights förutsätter att formatet för HTTP-begäranden för MVC-program är: `VERB controller/action`. Till exempel grupperas `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` och `GET Home/Product/sdf96vws` till `GET Home/Product`. Detta gör det möjligt att skapa meningsfulla aggregeringar av begäranden, till exempel antalet begäranden och genomsnittlig körningstid för begäranden.


### Instansdata 

Klicka dig vidare inom en specifik begärandetyp om du vill visa enskilda instanser. 

Två typer av data visas i Application Insights: aggregerade data, som lagras och visas som medelvärden, antal och belopp; och instansdata, som är enskilda rapporter över HTTP-begäranden, undantag, sidvisningar eller anpassade händelser.

När du visar egenskaperna för en begäran kan du se telemetrihändelserna som är associerade med den, till exempel begäranden och undantag.

![](./media/app-insights-java-get-started/7-instance.png)


### Analytics: Kraftfullt frågespråk

Allt eftersom du ackumulerar mer data kan du köra frågor både för att aggregera data och för att hitta enskilda instanser. [Analytics]() är ett kraftfullt verktyg både för att bättre förstå prestanda och användning, och för diagnostikändamål.

![Exempel med Analytics](./media/app-insights-java-get-started/025.png)


## 5. Installera din app på servern

Publicera appen på servern, låt användarna använda den och se hur telemetrin visas på portalen.

* Kontrollera att din brandvägg tillåter programmet att skicka telemetri till följande portar:

 * dc.services.visualstudio.com:443
 * dc.services.visualstudio.com:80
 * f5.services.visualstudio.com:443
 * f5.services.visualstudio.com:80


* Installera följande på Windows-servrar:

 * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Gör att du kan använda prestandaräknare.)

## Fel relaterade till begäranden och undantag

Ohanterade undantag samlas in automatiskt:

![Rulla nedåt och klicka på panelen Fel](./media/app-insights-java-get-started/21-exceptions.png)

Om du vill samla in data om andra undantag kan du välja mellan två alternativ:

* [Infoga anrop till trackException() i koden][apiexceptions]. 
* [Installera Java-agenten på servern](app-insights-java-agent.md). Du anger de metoder som du vill övervaka.


## Övervaka metodanrop och externa beroenden

[Installera Java-agenten](app-insights-java-agent.md) om du vill logga angivna interna metoder och anrop som görs via JDBC, med tidsinställningsdata.


## Prestandaräknare

Klicka på panelen **Servrar** så ser du ett antal prestandaräknare.


![](./media/app-insights-java-get-started/11-perf-counters.png)

### Anpassa samlingen med prestandaräknare

Om du vill inaktivera datainsamlingen från standarduppsättningen med prestandaräknare lägger du till följande kod under rotnoden i filen ApplicationInsights.xml:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Samla in data från fler prestandaräknare

Du kan ange ytterligare prestandaräknare som du vill samla in data från.

#### JMX-räknare (exponeras av Java Virtual Machine)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*   `displayName` – Namnet visas på Application Insights-portalen.
*   `objectName` – JMX-objektnamnet.
*   `attribute` – Attributet för JMX-objektnamnet som ska hämtas.
*   `type` (valfritt) – Typen för JMX-objektets attribut:
 *  Standardvärde: en enkel typ, till exempel int eller long.
 *  `composite`: prestandaräknardata har formatet ”Attribute.Data”
 *  `tabular`: prestandaräknardata har tabellradsformat



#### Windows-prestandaräknare

Varje [Windows-prestandaräknare](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) är medlem i en kategori (på samma sätt som ett fält är medlem i en klass). Kategorier kan antingen vara globala eller ha numrerade eller namngivna instanser.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*   displayName – Namnet som visas på Application Insights-portalen.
*   categoryName – Kategorin av prestandaräknare (prestandaobjekt) som den här prestandaräknaren är associerad med.
*   counterName – Namnet på prestandaräknaren.
*   instanceName – Namnet på instansen av prestandaräknarkategorin eller en tom sträng ("") om kategorin innehåller en enda instans. Om categoryName är Process och den prestandaräknare som du vill samla in data från hör till den aktuella JVM-processen som din app körs i anger du `"__SELF__"`.

Dina prestandaräknare visas som anpassade mått i [Metrics Explorer][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)


### Unix-prestandaräknare

* [Installera collectd med Application Insights-plugin-programmet](app-insights-java-collectd.md) om du vill samla in en mängd olika system- och nätverksdata.

## Samla in användar- och sesionsdata

Du skickar telemetri från webbservern. Men för att få en heltäckande bild av ditt program kan du lägga till ännu mer övervakning:

* [Lägg till telemetri till webbsidor][usage] om du vill övervaka sidvisningar och användarmått.
* [Konfigurera webbtester][availability] för att försäkra dig om att programmet är aktivt och att det svarar.

## Samla in loggspårningar

Du kan använda Application Insights för att arbeta med loggar från Log4J, Logback eller andra loggningsramverk. Du kan korrelera loggarna med HTTP-förfrågningar och annan telemetri. [Lär dig hur du gör][javalogs].

## Skicka din egen telemetri

Nu när du har installerat SDK kan du använda API:et för att skicka din egen telemetri.

* [Spåra anpassade händelser och mått][api] och ta reda på vad användare gör med ditt program.
* [Sök efter händelser och loggar][diagnostic] för att diagnostisera problem.


## Webbtester för tillgänglighet

Application Insights kan testa din webbplats med jämna mellanrum för att kontrollera att tjänsten är tillgänglig och att den svarar. [Konfigurera ][availability] genom att rulla ned och klicka på Tillgänglighet.

![Rulla ned, klicka på Tillgänglighet och sedan på Lägg till webbtest.](./media/app-insights-java-get-started/31-config-web-test.png)

Du kan visa diagram över svarstider, samt få e-postaviseringar om platsen kraschar.

![Exempel på webbtest](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[Läs mer om webbtester för tillgänglighet.][availability] 






## Har du några frågor? Har du problem?

[Felsöka Java](app-insights-java-troubleshoot.md)

## Nästa steg

Mer information finns på [Java Developer Center](/develop/java/).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=jun16_HO2-->


