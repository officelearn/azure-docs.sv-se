---
title: Prestandaövervakning för Java-webbappar i Azure Application Insights | Microsoft Docs
description: Utökad prestanda och användning övervakning av Java-webbplats med Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 3a771da2a1ef0333d49e1d83530b3d3032a550d2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Övervaka beroenden, undantag som fångats och metoden körningstider i Java-webbappar


Om du har [instrumenterats Java-webbappen med Application Insights][java], du kan använda Java-agenten för att skaffa dig djupare insikter utan några ändringar i koden:

* **Beroenden:** Data om anrop som programmet gör för andra komponenter, inklusive:
  * **REST-anrop** görs via HttpClient OkHttp och RestTemplate (källan) avbildas.
  * **Redis** anrop som görs via Jedis klienten avbildas.
  * **[JDBC-anrop](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -kommandona MySQL, SQL Server och Oracle DB avbildas automatiskt. För MySQL om anropet tar längre tid än 10-tal, rapporterar agenten frågeplanen.
* **Undantag som fångats:** Information om undantag som hanteras av din kod.
* **Metoden körningstid:** Information om tid det tar för att köra specifika metoder.

Om du vill använda Java-agenten måste installera du den på servern. Dina webbprogram måste vara försett med den [Application Insights SDK för Java][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installera Application Insights-agenten för Java
1. Kör din Java-server på datorn [ladda ner agenten](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Kontrollera att hämta samma verson av Java-agenten som Application Insights SDK för Java kärnor och webb-paket.
2. Redigera startskriptet programmet server och Lägg till följande JVM:
   
    `javaagent:`*fullständig sökväg till agenten JAR-filen*
   
    Till exempel i Tomcat på en Linux-dator:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Starta om programservern.

## <a name="configure-the-agent"></a>Konfigurera agent
Skapa en fil med namnet `AI-Agent.xml` och placera det i samma mapp som agenten JAR-filen.

Ange innehållet i XML-filen. Redigera exemplet nedan om du vill ta med eller utelämna funktionerna du vill.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Du måste aktivera rapporter undantag och metoden tidpunkten för enskilda metoder.

Som standard `reportExecutionTime` är true och `reportCaughtExceptions` är false.

## <a name="view-the-data"></a>Visa data
Sammanställda remote beroende- och metoden körningstider visas i Application Insights-resurs [under panelen prestanda][metrics].

Om du vill söka efter enskilda instanser av beroende, undantag och metoden rapporter, öppna [Sök][diagnostic].

[Diagnostisera problem med beroende - Läs mer](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Har du några frågor? Har du problem?
* Ser du inga data? [Ange undantag för brandväggen](app-insights-ip-addresses.md)
* [Felsöka Java](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
