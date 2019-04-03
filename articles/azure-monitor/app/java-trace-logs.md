---
title: Utforska Java spårningsloggar i Azure Application Insights | Microsoft Docs
description: Sök Log4J eller Logback spårningar i Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 614f9a44f7c699be38906ac00e12f523490ce112
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884303"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Utforska Java spårningsloggar i Application Insights
Om du använder Logback eller Log4J (version 1.2 eller v2.0) för att analysera och du kan ha spårningsloggarna skickas automatiskt till Application Insights kan du utforska och söka i dem.

## <a name="install-the-java-sdk"></a>Installera Java SDK

Följ anvisningarna för att installera [Application Insights SDK för Java][java], om du inte redan har gjort som.

## <a name="add-logging-libraries-to-your-project"></a>Lägg till loggning bibliotek i projektet
*Välj lämplig metod för ditt projekt.*

#### <a name="if-youre-using-maven"></a>Om du använder Maven …
Om ditt projekt redan har konfigurerats att använda Maven för bygg sammanfogar du något av följande kodfragment kod i pom.xml-filen.

Uppdatera sedan projektberoenden för att få de binärfiler som hämtats.

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

*Log4J v1.2*

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
Om projektet har redan ställts in till med Gradle, lägger du till något av följande rader till den `dependencies` i build.gradle-filen:

Uppdatera sedan projektberoenden för att få de binärfiler som hämtats.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Eller …
Följ riktlinjerna för att manuellt installera Application Insights Java SDK, ladda ned den JAR-filen (efter anländer till Maven Central klickar du på ”jar-länken i nedladdningar) för lämpliga loggbilaga och Lägg till hämtade loggbilaga JAR-filen i projektet.

| Loggare | Ladda ned | Bibliotek |
| --- | --- | --- |
| Logback |[Logback loggbilaga Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 loggbilaga Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Log4J v1.2 loggbilaga Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Lägg till loggbilaga i ditt loggningsramverk
Om du vill börja hämta spårningar, sammanfoga det relevanta kodavsnittet till konfigurationsfilen Log4J eller Logback: 

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

*Log4J v2.0*

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

*Log4J v1.2*

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

Application Insights-appenders kan refereras genom alla konfigurerad loggare och inte nödvändigtvis rot-loggaren (som visas i kodexemplen ovan).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Utforska dina spårningar i Application Insights-portalen
Nu när du har konfigurerat ditt projekt för att skicka loggspårningar till Application Insights kan du visa och söka efter dessa spår i Application Insights-portalen i den [Search] [ diagnostic] bladet.

Undantag som skickats via tangenttryckningar visas på portalen som Undantagstelemetri.

![Öppna sökning i Application Insights-portalen](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Nästa steg
[Diagnostiksökning][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


