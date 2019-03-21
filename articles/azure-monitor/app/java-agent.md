---
title: Övervakning av programprestanda för Java-webbappar i Azure Application Insights | Microsoft Docs
description: Utökad övervakning av prestanda och användning av din Java-webbplats med Application Insights.
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
ms.openlocfilehash: ce5f7ab1e6751a9ce68aa2d9c466a112c9cac182
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004047"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Övervaka beroenden, undantag som fångats och metoden körningstider i Java-webbappar


Om du har [instrumenterats Java-webbapp med Application Insights][java], du kan använda Java-agenten för att få bättre insikter, utan några kodändringar:

* **Beroenden:** Data om anrop som programmet gör för andra komponenter, inklusive:
  * **REST-anrop** görs via HttpClient, OkHttp och RestTemplate (Spring) avbildas.
  * **Redis** anrop som görs via Jedis klienten fångas.
  * **[JDBC-anrop](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -MySQL-, SQL Server- och Oracle DB-kommandon automatiskt avbildas. För MySQL om anropet tar längre tid än 10s, rapporterar agenten frågeplanen.
* **Fångats undantag:** Information om undantag som hanteras av din kod.
* **Metoden körningstid:** Information om tid det tar för att köra specifika metoder.

Om du vill använda Java-agenten måste installera du den på din server. Web apps måste vara utrustade med det [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installera Application Insights-agenten för Java
1. På datorn som du kör din Java-server, [ladda ned agenten](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Kontrollera att för att hämta samma version av Java-agenten som Application Insights Java SDK core och webb-paket.
2. Redigera startskriptet application server och Lägg till följande JVM:
   
    `javaagent:`*fullständig sökväg till agenten JAR-filen*
   
    Till exempel på Tomcat på en Linux-dator:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Starta om programservern.

## <a name="configure-the-agent"></a>Konfigurera agenten
Skapa en fil med namnet `AI-Agent.xml` och placera den i samma mapp som agenten JAR-filen.

Ange innehållet i xml-filen. Redigera i följande exempel för att ta med eller utelämna funktionerna du vill.

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

Du måste aktivera rapporter undantag och metoden val av tidpunkt för enskilda metoder.

Som standard `reportExecutionTime` är SANT och `reportCaughtExceptions` är FALSKT.

## <a name="additional-config-spring-boot"></a>Ytterligare konfiguration (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

För Azure App Services gör du följande:

* Välj Inställningar > Programinställningar
* Under Appinställningar lägger du till ett nytt nyckel/värde-par:

Nyckel: `JAVA_OPTS` Värde: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.3.1-SNAPSHOT.jar`

Den senaste versionen av Java-agenten finns i versionerna [här](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Agenten måste paketeras som en resurs i ditt projekt så att den hamnar i D:/home/site/wwwroot/katalog. Du kan bekräfta att din agent är i rätt katalog för App Service genom att gå till **utvecklingsverktyg** > **avancerade verktyg** > **Felsökningskonsolen**och undersöka innehållet i katalogen.    

* Spara inställningarna och starta om din app. (De här stegen gäller endast för App Services som körs på Windows.)

> [!NOTE]
> AI-Agent.xml och agenten jar-filen ska vara i samma mapp. De placeras ofta tillsammans i den `/resources` mappen i projektet.  

### <a name="spring-rest-template"></a>Mall för spring-Rest

Användning av Apache HTTP-klient krävs för Application Insights för att instrumentera har HTTP-anrop som görs med Springs resten av mallen. Spring's Rest mallen har inte konfigurerats för att använda Apache HTTP-klient som standard. Genom att ange [HttpComponentsClientHttpRequestfactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html) i konstruktorn för en Spring Rest mall används Apache HTTP.

Här är ett exempel på hur du gör detta med Spring Beans. Det här är ett väldigt enkelt exempel som använder standardinställningarna för klassen factory.

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>Aktivera W3C distribuerad spårning

Lägg till följande AI-Agent.xml:

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> Bakåtkompatibilitet läge är aktiverat som standard och enableW3CBackCompat-parametern är valfri och bör endast användas när du vill stänga av den. 

Vi rekommenderar är detta fallet när alla tjänster har uppdaterats till nyare version av SDK: er stöder W3C-protokollet. Vi rekommenderar starkt att flytta till nyare version av SDK: er med stöd för W3C så snart som möjligt.

Se till att **både [inkommande](correlation.md#w3c-distributed-tracing) och utgående (agent) konfigurationer** är exakt desamma.

## <a name="view-the-data"></a>Visa data
Sammanställda remote beroende- och metoden körningstider visas i Application Insights-resurs [under panelen prestanda][metrics].

För att söka efter enskilda instanser av beroende, undantag, och metoden rapporter, öppna [Search][diagnostic].

[Diagnostisera beroendeproblem - Läs mer](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Har du några frågor? Har du problem?
* Ser du inga data? [Brandväggsundantag för set](../../azure-monitor/app/ip-addresses.md)
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
