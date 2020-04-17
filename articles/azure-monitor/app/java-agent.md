---
title: Prestandaövervakning för Java-webbappar – Azure Application Insights
description: Utökad prestanda- och användningsövervakning av din Java-webbplats med Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b047a8dd8c67679a5cc8a45e8be82f9ab5227aa4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537550"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Övervaka beroenden, fångade undantag och metodkörningstider i Javas webbappar


Om du har [instrumenterat din Java-webbapp med Application Insights][java]kan du använda Java-agenten för att få djupare insikter, utan några kodändringar:

* **Beroenden:** Data om samtal som ditt program gör till andra komponenter, inklusive:
  * **Utgående HTTP-anrop** som görs via `java.net.HttpURLConnection` Apache HttpClient, OkHttp och fångas in.
  * **Redis samtal** som görs via Jedis klient fångas.
  * **JDBC-frågor** - För MySQL och PostgreSQL, om samtalet tar längre tid än 10 sekunder, rapporterar agenten frågeplanen.

* **Programloggning:** Samla in och korrelera dina programloggar med HTTP-begäranden och annan telemetri
  * **Log4j 1,2**
  * **Log4j2 (På andra sätt)**
  * **Logga tillbaka**

* **Bättre namn på åtgärden:** (används för aggregering av begäranden i portalen)
  * **Spring** - `@RequestMapping`baserat på .
  * **JAX-RS** - `@Path`baserat på . 

Om du vill använda Java-agenten installerar du den på servern. Dina webbappar måste instrumenteras med [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installera application insights-agenten för Java
1. [Ladda ned agenten](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) på datorn som kör din Java-server. Se till att ladda ned samma version av Java-agenten som Application Insights Java SDK-kärn- och webbpaket.
2. Redigera startskriptet för programservern och lägg till följande JVM-argument:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Till exempel i Tomcat på en Linux-dator:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Starta om programservern.

## <a name="configure-the-agent"></a>Konfigurera agenten
Skapa en `AI-Agent.xml` fil med namnet och placera den i samma mapp som agent JAR-filen.

Ange innehållet i xml-filen. Redigera följande exempel om du vill inkludera eller utelämna de funktioner du vill använda.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Ytterligare config (Fjäder Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

För Azure App Services gör du följande:

* Välj Inställningar > Programinställningar
* Under Appinställningar lägger du till ett nytt nyckel/värde-par:

Nyckel: `JAVA_OPTS` Värde:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

För den senaste versionen av Java-agenten, kolla utgåvorna [här](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Agenten måste paketeras som en resurs i projektet så att den hamnar i D:/home/site/wwwroot/directory. Du kan bekräfta att din agent finns i rätt App Service-katalog genom att gå till **Development Tools** > **Advanced Tools** > **Debug Console** och undersöka innehållet i webbplatskatalogen.    

* Spara inställningarna och starta om din app. (De här stegen gäller endast för App Services som körs på Windows.)

> [!NOTE]
> AI-Agent.xml och agentburkfilen ska finnas i samma mapp. De placeras ofta tillsammans `/resources` i mappen för projektet.  

#### <a name="enable-w3c-distributed-tracing"></a>Aktivera W3C-distribuerad spårning

Lägg till följande i AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Bakåtkompatibilitetsläget är aktiverat som standard och parametern enableW3CBackCompat är valfri och bör endast användas när du vill stänga av den. 

Helst skulle detta vara fallet när alla dina tjänster har uppdaterats till nyare version av SDKs stödja W3C-protokollet. Det rekommenderas starkt att flytta till nyare version av SDK med W3C-stöd så snart som möjligt.

Kontrollera att **både [inkommande](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) och utgående (agent) konfigurationer** är exakt samma.

## <a name="view-the-data"></a>Visa data
I application insights-resursen visas aggregerade fjärrberoende och metodkörningstider [under panelen Prestanda][metrics].

Om du vill söka efter enskilda förekomster av beroende-, undantags- och metodrapporter öppnar du [Sök][diagnostic].

[Diagnostisera beroendeproblem - läs mer](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Har du några frågor? Har du problem?
* Ser du inga data? [Ange brandväggsund undantag](../../azure-monitor/app/ip-addresses.md)
* [Felsöka Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
