---
title: "Utforska Java spårningsloggar i Azure Application Insights | Microsoft Docs"
description: "Sök Log4J eller Logback spåren i Application Insights"
services: application-insights
documentationcenter: java
author: CFreemanwa
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: bwren
ms.openlocfilehash: 5baba3deaf58a1a24995c60381592a9c2ffefd81
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Utforska Java spårningsloggar i Application Insights
Om du använder Logback eller Log4J (version 1.2 eller v2.0) för spårning, kan du ha spårloggarna skickas automatiskt till Application Insights där du kan utforska och söka i dem.

## <a name="install-the-java-sdk"></a>Installera Java SDK

Installera [Application Insights SDK för Java][java], om du inte redan har gjort som.

(Om du inte vill spåra HTTP-begäranden, kan du utelämna de flesta av XML-konfigurationsfil, men du måste inkludera minst den `InstrumentationKey` element. Du bör också kontakta `new TelemetryClient()` initieras SDK.)


## <a name="add-logging-libraries-to-your-project"></a>Lägg till loggning bibliotek i projektet
*Välj lämplig metod för ditt projekt.*

#### <a name="if-youre-using-maven"></a>Om du använder Maven …
Om projektet har redan konfigurerats att använda Maven för version, sammanfoga du något av följande kodavsnitt i koden i filen pom.xml.

Uppdatera sedan projektberoenden för att få binärfiler som hämtats.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J version 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Om du använder Gradle …
Om projektet har redan konfigurerats att använda Gradle för version, lägga till en av följande rader till den `dependencies` i filen build.gradle:

Uppdatera sedan projektberoenden för att få binärfiler som hämtats.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '1.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '1.0.+'
```

**Log4J version 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '1.0.+'
```

#### <a name="otherwise-"></a>Eller …
Ladda ned och extrahera lämpliga loggbilaga och lägger till biblioteket i projektet:

| Loggaren | Ladda ned | Bibliotek |
| --- | --- | --- |
| Logback |[SDK med Logback loggbilaga](https://aka.ms/xt62a4) |logback-applicationinsights-loggning |
| Log4J v2.0 |[SDK Log4J v2 loggbilaga](https://aka.ms/qypznq) |log4j2-applicationinsights-loggning |
| Log4j version 1.2 |[SDK Log4J version 1.2 loggbilaga](https://aka.ms/ky9cbo) |log4j1_2-applicationinsights-loggning |

## <a name="add-the-appender-to-your-logging-framework"></a>Lägg till loggbilaga i din loggningsramverk
Om du vill börja få spårningar, sammanfoga det relevanta kodavsnittet till konfigurationsfilen Log4J eller Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.Log4j">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J version 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights appenders kan refereras genom att alla angivna loggaren och inte nödvändigtvis loggaren rot (som visas i kodexemplen ovan).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Utforska dina spår i Application Insights-portalen
Nu när du har konfigurerat ditt projekt för att skicka spårningar till Application Insights kan du visa och söka efter dessa spåren i Application Insights-portalen i den [Sök] [ diagnostic] bladet.

![Öppna Sök i Application Insights-portalen](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>Nästa steg
[Diagnostiska sökning][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md


