---
title: "Utforska Java spårningsloggar i Azure Application Insights | Microsoft Docs"
description: "Sök Log4J eller Logback spåren i Application Insights"
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: mbullwin
ms.openlocfilehash: fae3269e21d0f760ae77a70333047306c07c2961
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Utforska Java spårningsloggar i Application Insights
Om du använder Logback eller Log4J (version 1.2 eller v2.0) för spårning, kan du ha spårloggarna skickas automatiskt till Application Insights där du kan utforska och söka i dem.

## <a name="install-the-java-sdk"></a>Installera Java SDK

Följ instruktionerna för att installera [Application Insights SDK för Java][java], om du inte redan har gjort som.

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
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J version 1.2*

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
Om projektet har redan konfigurerats att använda Gradle för version, lägga till en av följande rader till den `dependencies` i filen build.gradle:

Uppdatera sedan projektberoenden för att få binärfiler som hämtats.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J version 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Eller …
Följ riktlinjerna för att manuellt installera Application Insights SDK för Java, ladda ned jar (efter ariving Maven Central sida klickar du på 'jar-länk i avsnittet download) för lämplig loggbilaga och Lägg till hämtade loggbilaga jar i projektet.

| Logger | Ladda ned | Bibliotek |
| --- | --- | --- |
| Logback |[Logback loggbilaga Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 loggbilaga Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j version 1.2 |[Log4J version 1.2 loggbilaga Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


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

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
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

Undantag skickat via loggare visas på portalen som Undantagstelemetri.

![Öppna Sök i Application Insights-portalen](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>Nästa steg
[Diagnostiska sökning][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md


