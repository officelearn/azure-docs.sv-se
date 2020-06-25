---
title: Övervaka Java-program på valfri miljö – Azure Monitor Application Insights
description: Övervakning av program prestanda för Java-program som körs i en miljö utan att behöva instrumentera appen. Distribuerad spårning och program karta.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 3e3d108603ad6210143deea58049ff7b230bb6fa
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85319711"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java-kodad program övervakning Azure Monitor Application Insights – offentlig för hands version

Java-kodad program övervakning är allt om enkelhet – det finns inga kod ändringar, Java-agenten kan aktive ras via bara ett par konfigurations ändringar.

 Java-agenten fungerar i valfri miljö och gör att du kan övervaka alla Java-program. Med andra ord, oavsett om du kör Java-appar på virtuella datorer, lokalt, i AKS i Windows, Linux – du namnger den, kommer Java 3,0-agenten att övervaka din app.

Det krävs inte längre att lägga till Application Insights Java SDK i programmet, eftersom 3,0-agenten automatiskt samlar in begär Anden, beroenden och loggar på egen hand.

Du kan fortfarande skicka anpassad telemetri från ditt program. 3,0-agenten spårar och korrelerar den tillsammans med all automatiskt insamlad telemetri.

## <a name="quickstart"></a>Snabbstart

**1. Ladda ned agenten**

Ladda ned [applicationinsights-agent-3.0.0-Preview. 5. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

**2. peka JVM till agenten**

Lägg till `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.5.jar` i programmets JVM-argument

Typiska JVM-argument inkluderar `-Xmx512m` och `-XX:+UseG1GC` . Så om du vet var du vill lägga till dessa, vet du redan var du ska lägga till det.

Mer hjälp om hur du konfigurerar programmets JVM-argument finns i [3,0 Preview: tips för att uppdatera dina JVM-argument](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments).

**3. peka agenten till din Application Insights-resurs**

Om du inte redan har en Application Insights resurs kan du skapa en ny genom att följa stegen i guiden för att [Skapa resurser](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

Peka agenten till Application Insights resurs, antingen genom att ange en miljö variabel:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Eller genom att skapa en konfigurations fil med namnet `ApplicationInsights.json` och placera den i samma katalog som `applicationinsights-agent-3.0.0-PREVIEW.5.jar` , med följande innehåll:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Du kan hitta din anslutnings sträng i Application Insights-resursen:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights anslutnings sträng":::

**4!!!**

Starta ditt program och gå till din Application Insights-resurs i Azure Portal för att se dina övervaknings data.

> [!NOTE]
> Det kan ta några minuter innan dina övervaknings data visas i portalen.


## <a name="configuration-options"></a>Konfigurationsalternativ

I `ApplicationInsights.json` filen kan du också konfigurera:

* Namn på moln roll
* Moln roll instans
* Samla in program logg
* JMX mått
* Micrometer
* Pulsslag
* Samling
* HTTP-proxy
* Själv diagnostik

Se information på [3,0 offentlig för hands version: konfigurations alternativ](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Autosamlade begär Anden, beroenden, loggar och mått

### <a name="requests"></a>Begäranden

* JMS-konsumenter
* Kafka-konsumenter
* Nett/webflöde
* Servlets
* Våren-schemaläggning

### <a name="dependencies-with-distributed-trace-propagation"></a>Beroenden med Distributed trace-spridning

* Apache HttpClient och HttpAsyncClient
* gRPC
* Java. net. HttpURLConnection
* JMS
* Kafka
* Nett klient
* OkHttp

### <a name="other-dependencies"></a>Andra beroenden

* Cassandra
* JDBC
* MongoDB (asynkron och synkronisering)
* Redis (sallat och Jedis)

### <a name="logs"></a>Loggar

* Java. util. logging
* Log4j
* SLF4J/logback

### <a name="metrics"></a>Mått

* Micrometer (inklusive egenskaper för vårens start motstånd)
* JMX mått

## <a name="sending-custom-telemetry-from-your-application"></a>Skicka anpassad telemetri från ditt program

Vårt mål i 3.0 + är att du ska kunna skicka din anpassade telemetri med standard-API: er.

Vi stöder micrometer, opentelemetri-API och populära loggnings ramverk. Application Insights Java 3,0 fångar automatiskt in telemetri och korrelerar det tillsammans med all automatisk insamlad telemetri.

Därför planerar vi inte att lansera en SDK med Application Insights 3,0 för tillfället.

Application Insights Java 3,0 lyssnar redan efter telemetri som skickas till Application Insights Java SDK 2. x. Den här funktionen är en viktig del av uppgraderings artikeln för befintliga 2. x-användare och det fyller en viktig lucka i vårt stöd för anpassad telemetri tills API: t opentelemetri är GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Skicka anpassad telemetri med Application Insights Java SDK 2. x

Lägg till `applicationinsights-core-2.6.0.jar` i ditt program (alla 2. x-versioner stöds av Application Insights Java 3,0, men det är värt att använda det senaste om du har ett val):

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

och använder det för att skicka anpassad telemetri.

### <a name="events"></a>Händelser

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Mått

Du kan skicka mått för telemetri via [micrometer](https://micrometer.io):

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Du kan också använda Application Insights Java SDK 2. x:

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
Du kan skicka anpassad log-telemetri via ditt favorit loggnings ramverk.

Du kan också använda Application Insights Java SDK 2. x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Undantag
Du kan skicka anpassade undantags telemetri via ditt favorit loggnings ramverk.

Du kan också använda Application Insights Java SDK 2. x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Uppgradera från Application Insights Java SDK 2. x

Om du redan använder Application Insights Java SDK 2. x i ditt program behöver du inte ta bort den. Java 3,0-agenten identifierar den och samlar in och korrelerar en anpassad telemetri som du skickar via Java SDK 2. x, samtidigt som du undertrycker en autoinsamling som utförs av Java SDK 2. x för att förhindra dubbel avbildning.

> [!NOTE]
> Obs: Java SDK 2. x TelemetryInitializers och TelemetryProcessors kommer inte att köras när du använder 3,0-agenten.
