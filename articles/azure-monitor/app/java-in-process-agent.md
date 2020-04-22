---
title: Övervaka Java-program i alla miljöer – Azure Monitor Application Insights
description: Övervakning av programmets prestanda för Java-program som körs i alla miljöer utan att instrumentera appen. Distribuerad spårning och programkarta.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: b9c1a52051e63beee9a784714a7bb1a6a79e8759
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687722"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java-kodlöst program övervakning Azure Monitor Application Insights - offentlig förhandsversion

Java kodlös program övervakning handlar om enkelhet - det finns inga kodändringar, kan Java-agenten aktiveras genom bara ett par konfigurationsändringar.

 Java-agenten fungerar i alla miljöer och låter dig övervaka alla dina Java-program. Med andra ord, oavsett om du kör dina Java-appar på virtuella datorer, lokalt, i AKS, på Windows, Linux - you name it, kommer Java 3.0-agenten att övervaka din app.

Det behövs inte längre att lägga till Programstatistiken Java SDK i ditt program, eftersom 3.0-agenten automatiskt samlar in begäranden, beroenden och loggar alla på egen hand.

Du kan fortfarande skicka anpassad telemetri från ditt program. Den 3,0 agent kommer att spåra och korrelera den tillsammans med alla autocollected telemetri.

## <a name="quickstart"></a>Snabbstart

**1. Ladda ner agenten**

Ladda ner [applicationinsights-agent-3.0.0-PREVIEW.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

**2. Rikta JVM till agenten**

Lägg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.3.jar` till i programmets JVM args

Typiska JVM `-Xmx512m` args inkluderar och `-XX:+UseG1GC`. Så om du vet var du ska lägga till dessa, då vet du redan var du ska lägga till detta.

Mer hjälp med att konfigurera programmets JVM-args finns i [3.0 Preview: Tips för uppdatering av dina JVM-args](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments).

**3. Peka agenten på din Application Insights-resurs**

Om du inte redan har en Application Insights-resurs kan du skapa en ny genom att följa stegen i [resursgenereringsguiden](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

Peka agenten på application insights-resursen, antingen genom att ange en miljövariabel:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Eller genom att skapa `ApplicationInsights.json`en konfigurationsfil med `applicationinsights-agent-3.0.0-PREVIEW.3.jar`namnet och placera den i samma katalog som , med följande innehåll:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Du hittar din anslutningssträng i din Application Insights-resurs:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Anslutningssträng för programinsikter":::

**4. Det är allt!**

Starta nu ditt program och gå till din Application Insights-resurs i Azure-portalen för att se dina övervakningsdata.

> [!NOTE]
> Det kan ta ett par minuter innan övervakningsdata visas i portalen.


## <a name="configuration-options"></a>Konfigurationsalternativ

I `ApplicationInsights.json` filen kan du dessutom konfigurera:

* Namn på molnroll
* Molnrollinstans
* Insamling av programlogg
* JMX-mått
* Micrometer
* Pulsslag
* Samling
* HTTP-proxy
* Självdiagnostik

Se information på [3.0 Offentlig förhandsversion: Konfigurationsalternativ](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Automatiskt sammanställda begäranden, beroenden, loggar och mått

### <a name="requests"></a>Begäranden

* JMS Konsumenter
* Kafka Konsumenter
* Netty/WebFlux
* Servlets (servlets)
* Vårschemaläggning

### <a name="dependencies-with-distributed-trace-propagation"></a>Beroenden med distribuerad spårningsspridning

* Apache httpClient och httpAsyncClient
* gRPC (på andra sätt)
* java.net.HttpURLAnslutning
* Jms
* Kafka
* Netty-klient
* Okhttp

### <a name="other-dependencies"></a>Andra beroenden

* Cassandra
* JDBC
* MongoDB (async och synkronisering)
* Redis (Sallad och Jedis)

### <a name="logs"></a>Loggar

* java.util.logga
* Log4j (på andra sätt)
* SLF4J/Logback

### <a name="metrics"></a>Mått

* Micrometer
* JMX-mått

## <a name="sending-custom-telemetry-from-your-application"></a>Skicka anpassad telemetri från ditt program

Vårt mål i 3.0+ är att du ska kunna skicka din anpassade telemetri med standard-API:er.

Vi stöder Micrometer, OpenTelemetry API och de populära loggningsramverken. Application Insights Java 3.0 kommer automatiskt att fånga telemetri, och korrelera den tillsammans med alla automatiskt insamlade telemetri.

Därför planerar vi inte att släppa en SDK med Application Insights 3.0 just nu.

Application Insights Java 3.0 lyssnar redan efter telemetri som skickas till Application Insights Java SDK 2.x. Den här funktionen är en viktig del av uppgraderingshistorien för befintliga 2.x-användare, och den fyller en viktig lucka i vårt anpassade telemetristöd tills OpenTelemetry API är GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Skicka anpassad telemetri med Application Insights Java SDK 2.x

Lägg `applicationinsights-core-2.6.0.jar` till ditt program (alla 2.x versioner stöds av Application Insights Java 3.0, men det är värt att använda det senaste om du har ett val):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Skapa en TelemetryClient:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

och använda den för att skicka anpassad telemetri.

### <a name="events"></a>Händelser

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Mått

Du kan skicka måtttelemetri via [Micrometer:](https://micrometer.io)

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Eller så kan du också använda Application Insights Java SDK 2.x:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Beroenden

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Loggar
Du kan skicka anpassad loggtelemetri via ditt favoritloggningsramverk.

Eller så kan du också använda Application Insights Java SDK 2.x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Undantag
Du kan skicka anpassad undantagstelemetri via ditt favoritloggningsramverk.

Eller så kan du också använda Application Insights Java SDK 2.x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Uppgradera från Application Insights Java SDK 2.x

Om du redan använder Application Insights Java SDK 2.x i ditt program behöver du inte ta bort den. Java 3.0-agenten kommer att upptäcka den och fånga och korrelera all anpassad telemetri som du skickar via Java SDK 2.x, samtidigt som du undertrycker alla automatiska sortering som utförs av Java SDK 2.x för att förhindra dubblettfångst.

> [!NOTE]
> Java SDK 2.x TelemetriInitializers och TelemetriProcessors körs inte när 3.0-agenten används.
