---
title: Prestanda övervakning för Java-webbappar i Azure Application Insights | Microsoft Docs
description: Utökad prestanda och användnings övervakning av din Java-webbplats med Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ff9d4bb98a79c379fda2c1a0a0ab9d5e0ec212ce
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338092"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Övervaka beroenden, fångade undantag och metod körnings tider i Java-webbappar


Om du har [instrumenterat Java-webbappen med Application Insights][java]kan du använda Java-agenten för att få djupare insikter utan några kod ändringar:

* **Relation** Data om samtal som ditt program gör till andra komponenter, inklusive:
  * **Utgående HTTP-anrop** som görs via Apache httpclient, OkHttp `java.net.HttpURLConnection` och samlas in.
  * **Redis-anrop** som görs via Jedis-klienten fångas.
  * **JDBC-frågor** – för MySQL och PostgreSQL, om samtalet tar längre än 10 sekunder, rapporterar agenten frågeplan.

* **Program loggning:** Avbilda och korrelera dina program loggar med HTTP-förfrågningar och annan telemetri
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Bättre namngivning av åtgärder:** (används för agg regering av förfrågningar i portalen)
  * **Våren** – baserat på `@RequestMapping`.
  * **JAX – RS** -baserad på `@Path`. 

Om du vill använda Java-agenten installerar du den på servern. Dina webb program måste vara instrumenterade med [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installera Application Insights agent för Java
1. [Ladda ned agenten](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) på datorn som kör din Java-server. Se till att ladda ned samma version av Java-agenten som Application Insights Java SDK-kärn- och webbpaket.
2. Redigera start skriptet för program servern och Lägg till följande JVM-argument:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Till exempel i Tomcat på en Linux-dator:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Starta om program servern.

## <a name="configure-the-agent"></a>Konfigurera agenten
Skapa en fil med `AI-Agent.xml` namnet och placera den i samma mapp som agentens jar-fil.

Ange innehållet i XML-filen. Redigera följande exempel för att inkludera eller utelämna de funktioner som du vill använda.

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

## <a name="additional-config-spring-boot"></a>Ytterligare konfiguration (våren boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

För Azure App tjänster gör du följande:

* Välj Inställningar > Programinställningar
* Under Appinställningar lägger du till ett nytt nyckel/värde-par:

Knapp `JAVA_OPTS`Värde`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Du hittar [den senaste versionen av Java-agenten här](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Agenten måste paketeras som en resurs i ditt projekt så att den blir i D:/Home/site/wwwroot/Directory. Du kan bekräfta att agenten har rätt app service katalog genom att gå till **utvecklingsverktyg** > **Avancerade verktyg** > **Felsök konsol** och undersöka innehållet i plats katalogen.    

* Spara inställningarna och starta om appen. (Dessa steg gäller endast för App Services som körs i Windows.)

> [!NOTE]
> AI-Agent. xml och agentens jar-fil ska finnas i samma mapp. De placeras ofta tillsammans i `/resources` mappen i projektet.  

#### <a name="enable-w3c-distributed-tracing"></a>Aktivera distribuerad W3C-spårning

Lägg till följande i AI-Agent. XML:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Läget för bakåtkompatibilitet är aktiverat som standard och enableW3CBackCompat-parametern är valfri och ska endast användas när du vill inaktivera den. 

Vi rekommenderar att detta är fallet när alla dina tjänster har uppdaterats till en nyare version av SDK: er som stöder W3C-protokollet. Vi rekommenderar starkt att du flyttar till en nyare version av SDK: er med stöd för W3C så snart som möjligt.

Se till att **både [inkommande](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) och utgående (agent) konfigurationer** är exakt samma.

## <a name="view-the-data"></a>Visa data
I Application Insights-resursen visas sammanställda fjärrberoende och metod körnings tider [under prestanda panelen][metrics].

Om du vill söka efter enskilda instanser av beroende, undantag och metod rapporter öppnar du [Sök][diagnostic].

[Diagnostisera beroende problem – Läs mer](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Har du några frågor? Har du problem?
* Ser du inga data? [Ange brand Väggs undantag](../../azure-monitor/app/ip-addresses.md)
* [Felsöka Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
