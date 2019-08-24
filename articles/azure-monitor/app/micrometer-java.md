---
title: Använda micrometer med Azure Application Insights Java SDK | Microsoft Docs
description: 'En steg-för-steg-guide om hur du använder micrometer med din Application Insights våren start-och icke-våren start program. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: 1074495f5ac9112b6ce4f67ad2d81ee57b28e720
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012701"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Använda micrometer med Azure Application Insights Java SDK
Micrometer program övervakning mäter mått för JVM program kod och låter dig exportera data till dina favorit övervaknings system. I den här artikeln får du lära dig hur du använder micrometer med Application Insights för både våren boot-och non-våren Boot-program.

## <a name="using-spring-boot-15x"></a>Använda våren Boot 1.5 x
Lägg till följande beroenden i din Pom. XML-eller build. gradle-fil: 
* [Application Insights fjäder-start-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)1.1.0-beta eller senare
* Micrometer Azure Registry 1.1.0 eller senare
* [Micrometer våren Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 eller senare (den här backports koden för AutoConfig i våren-ramverket).
* [ApplicationInsights-resurs](../../azure-monitor/app/create-new-resource.md )

Steg

1. Uppdatera Pom. XML-filen för ditt fjäder Boot-program och Lägg till följande beroenden:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.0-BETA</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Uppdatera program. properties-eller YML-filen med Application Insights Instrumentation-nyckeln med hjälp av följande egenskap:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Skapa ditt program och kör
2. Ovan bör du komma igång med föraggregerade mått som samlas in automatiskt till Azure Monitor. Mer information om hur du finjusterar Application Insights våren Boot starter finns i [README på GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Använda våren 2. x

Lägg till följande beroenden i din Pom. XML-eller build. gradle-fil:

* Application Insights fjäder-start-starter 2.1.2 eller senare
* Azure-våren-Boot-Metrics-starter 2.1.5 eller högre  
* [Application Insights resurs](../../azure-monitor/app/create-new-resource.md )

Steg:

1. Uppdatera Pom. XML-filen för ditt fjäder Boot-program och Lägg till följande beroende i det:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.1.6</version>
    </dependency>
    ```
1. Uppdatera program. properties-eller YML-filen med Application Insights Instrumentation-nyckeln med hjälp av följande egenskap:

     `management.metrics.export.azuremonitor.instrumentation-key=<your-instrumentation-key-here>`
3. Skapa ditt program och kör
4. Ovanstående bör få dig att köra med föraggregerade mått som samlas in automatiskt till Azure Monitor. Mer information om hur du finjusterar Application Insights våren Boot starter finns i [README på GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Standard mått:

*    Automatiskt konfigurerade mått för Tomcat, JVM, logback-mått, Log4J-mått, drift tids mått, processor mått och FileDescriptorMetrics.
*    Om t. ex. Netflix-hystrix finns på klass Sök väg får du även dessa mått. 
*    Följande mått kan vara tillgängliga genom att lägga till respektive bönor. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcaseCache, Jcache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 mått 
        - Kafka mått 

 

Så här stänger du av automatisk insamling av mått: 
 
- JVM mått: 
    - Management. Metrics. binder. JVM. enabled = false 
- Logback mått: 
    - management.metrics.binders.logback.enabled=false
- Drifts mått: 
    - Management. Metrics. binder. drift tid. enabled = false 
- Processor mått:
    -  Management. Metrics. binder. processor. enabled = false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Hystrix-mått om bibliotek på classpath: 
    - Management. Metrics. binder. hystrix. enabled = false 
- AspectJ-mått om bibliotek på classpath: 
    - våren. aop. enabled = false 

> [!NOTE]
> Ange egenskaperna ovan i filen Application. properties eller Application. yml för ditt våren Boot-program

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Använda micrometer med icke-våren start webb program

Lägg till följande beroenden i din Pom. XML-eller build. gradle-fil:
 
* [Program Insight Core 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) eller senare
* [Application Insights webb 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) eller senare
* [Registrera webb filter](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* Micrometer Azure Registry 1.1.0 eller senare
* [Application Insights resurs](../../azure-monitor/app/create-new-resource.md )

Steg:

1. Lägg till följande beroenden i din Pom. XML-eller build. gradle-fil:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web</artifactId>
            <version>2.2.0</version>
        </dependency
     ```

2. Lägg Application Insights. xml i mappen resurser

    Exempel på servlet-klass (genererar ett timer-mått):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

      Exempel på konfigurations klass:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Mer information om mått finns i [micrometer-dokumentationen](https://micrometer.io/docs/).

Annan exempel kod för hur du skapar olika typer av mått finns i[den officiella micrometer GitHub-lagrings platsen](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Så här binder du ytterligare mått samling

### <a name="springbootspring"></a>SpringBoot/Spring

Skapa en bönor av respektive mått kategori. Anta till exempel att vi behöver guava-cache-mått:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Det finns flera mått som inte är aktiverade som standard men som kan bindas på ovanstående sätt. En fullständig lista finns i [den officiella micrometer GitHub-lagrings platsen](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Appar som inte är våren
Lägg till följande bindnings kod i konfigurations filen:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Nästa steg

* Mer information om micrometer hittar du i den officiella [micrometer-dokumentationen](https://micrometer.io/docs).
* Mer information om våren på Azure hittar du i den officiella [vår dokumentation om Azure](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
