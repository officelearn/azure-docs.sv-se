---
title: Utforska Java-spårningsloggar i Azure Application Insights
description: Sök log4J- eller loggbacksspårningar i Application Insights
ms.topic: conceptual
ms.date: 05/18/2019
ms.openlocfilehash: da1b76d52ab93f4d1be7196d6eb7286579481119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657222"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Utforska Java-spårningsloggar i Application Insights
Om du använder Logback eller Log4J (v1.2 eller v2.0) för spårning kan du få dina spårningsloggar skickade automatiskt till Application Insights där du kan utforska och söka på dem.

> [!TIP]
> Du behöver bara ställa in instrumentationsnyckeln för programinsikter en gång för ditt program. Om du använder ett ramverk som Java Spring kanske du redan har registrerat nyckeln någon annanstans i appens konfiguration.

## <a name="using-the-application-insights-java-agent"></a>Använda Java-agenten Application Insights

Som standard fångar Programstatistik Java-agenten automatiskt loggning `WARN` som utförs på nivå och högre.

Du kan ändra tröskelvärdet för loggning `AI-Agent.xml` som fångas med hjälp av filen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

Du kan inaktivera Java-agentens loggningsfångst med `AI-Agent.xml` hjälp av filen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Alternativt (i motsats till att använda Java-agenten) kan du följa instruktionerna nedan

### <a name="install-the-java-sdk"></a>Installera Java SDK

Följ instruktionerna för att installera [Application Insights SDK för Java][java], om du inte redan har gjort det.

### <a name="add-logging-libraries-to-your-project"></a>Lägga till loggningsbibliotek i projektet
*Välj lämplig metod för ditt projekt.*

#### <a name="if-youre-using-maven"></a>Om du använder Maven …
Om projektet redan är inställt på att använda Maven för att skapa sammanfogar du ett av följande kodavsnitt i filen pom.xml.

Uppdatera sedan projektberoendena för att hämta binärfilerna.

*Logga tillbaka*

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
Om projektet redan har konfigurerats för att använda Gradle för `dependencies` att skapa lägger du till en av följande rader i gruppen i filen build.gradle:

Uppdatera sedan projektberoendena för att hämta binärfilerna.

**Logga tillbaka**

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
Följ riktlinjerna för att manuellt installera Application Insights Java SDK, ladda ner burken (Efter ankomsten till Maven Central Page klicka på "jar" länk i nedladdningssektionen) för lämplig appender och lägga till den nedladdade tilläggsburken till projektet.

| Logger | Ladda ned | Bibliotek |
| --- | --- | --- |
| Logga tillbaka |[Logback appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-loggning-logback |
| Log4J v2.0 |[Log4J v2 appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-loggning-log4j2 |
| Log4j v1.2 |[Log4J v1.2 appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Lägg till appen till loggningsramverket
Om du vill börja hämta spårningar sammanfogar du relevant kodavsnitt till konfigurationsfilen Log4J eller Logback: 

*Logga tillbaka*

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

Application Insights-appenders kan refereras av alla konfigurerade logger, och inte nödvändigtvis av rotloggaren (som visas i kodexemplen ovan).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Utforska dina spår i portalen Application Insights
Nu när du har konfigurerat projektet för att skicka spårningar till Application Insights kan du visa och söka efter dessa spårningar i application insights-portalen i [sökbladet.][diagnostic]

Undantag som skickas via loggers visas på portalen som Undantagstelemetri.

![Öppna Sök i portalen Application Insights](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Nästa steg
[Diagnostiksökning][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


