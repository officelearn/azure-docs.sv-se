---
title: Utforska Java trace-loggar i Azure Application Insights (2.5.0-BETA) | Microsoft Docs
description: Sök Log4J eller logback traces i Application Insights (2.5.0-BETA)
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: 028563658256f7bd8e016b5c7bbf703a5030a3de
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298365"
---
# <a name="explore-java-trace-logs-in-application-insights-250-beta"></a>Utforska Java trace-loggar i Application Insights (2.5.0-BETA)
Om du använder logback eller Log4J (v 1.2 eller v 2.0) för spårning kan du låta spårnings loggarna skickas automatiskt till Application Insights där du kan utforska och söka efter dem.

## <a name="note-if-you-are-using-the-application-insights-java-agent"></a>Obs: om du använder Application Insights Java-agenten

Du kan konfigurera Application Insights Java-agenten att automatiskt avbilda dina loggar genom att aktivera funktionen i `AI-Agent.xml` filen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

Annars...

## <a name="install-the-java-sdk"></a>Installera Java SDK

Följ anvisningarna för att installera [Application Insights SDK för Java][java], om du inte redan har gjort det.

## <a name="add-logging-libraries-to-your-project"></a>Lägg till logg bibliotek i projektet
*Välj lämplig metod för ditt projekt.*

#### <a name="if-youre-using-maven"></a>Om du använder Maven …
Om ditt projekt redan har kon figurer ATS för att använda Maven för build, sammanfoga ett av följande kod avsnitt i din Pom. XML-fil.

Uppdatera sedan projekt beroendena för att få de hämtade binärfilerna.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Om du använder Gradle …
Om ditt projekt redan har kon figurer ATS för att använda Gradle för Build lägger du till någon av följande rader `dependencies` i gruppen i filen build. Gradle:

Uppdatera sedan projekt beroendena för att få de hämtade binärfilerna.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v 2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Eller …
Följ rikt linjerna för att installera Application Insights Java SDK manuellt, Hämta burken (när du har tillkommer till Central sidan i maven klickar du på länken ' jar ' i nedladdnings avsnittet) för lämplig tillägg och lägger till den nedladdade tilläggs jar i projektet.

| Loggar | Ladda ned | Bibliotek |
| --- | --- | --- |
| Logback |[Logback tilläggsprogram-jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v 2.0 |[Log4J v2-tilläggsprogram](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v 1.2 |[Log4J v 1.2 tillfogar jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Lägg till tillägget i loggnings ramverket
Du börjar hämta spår genom att slå samman det relevanta kodfragmentet till Log4J-eller logback-konfigurations filen: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v 2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights tillägg kan refereras till av en konfigurerad loggare och inte nödvändigt vis av rot loggaren (som visas i kod exemplen ovan).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Utforska spårningarna i Application Insightss portalen
Nu när du har konfigurerat projektet för att skicka spår till Application Insights kan du Visa och söka igenom dessa spårningar i Application Insights portalen på [Sök][diagnostic] bladet.

Undantag som skickas via loggar visas i portalen som telemetri för undantag.

![Öppna Sök i Application Insights-portalen](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Nästa steg
[Diagnostisk sökning][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


