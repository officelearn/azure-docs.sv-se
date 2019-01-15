---
title: Hur du använder mikrometer med Azure Application Insights Java SDK | Microsoft Docs
description: 'En steg-för-steg-instruktioner om hur du använder mikrometer med Application Insights Spring Boot och Spring Boot-program. '
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
ms.openlocfilehash: 778690fb2796cea3154b3acbb662341fdaea87da
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259614"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Hur du använder mikrometer med Azure Application Insights Java SDK
Mikrometer övervakning mått programmått för JVM-baserade program kod och hjälper dig att exportera data till dina favorit övervakningssystem. Den här artikeln får du lära dig att använda mikrometer med Application Insights för både Spring Boot och Spring Boot-program.

## <a name="using-spring-boot-15x"></a>Med Spring starta 1,5 x
Lägg till följande beroenden till pom.xml eller build.gradle-fil: 
* [Application Insights spring boot-starter-](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)1.1.0-BETA eller senare
* Mikrometer Azure registret 1.1.0 eller senare
* [Mikrometer Spring äldre](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 eller senare (den här backportar autoconfig koden inom ramen för Spring).
* [ApplicationInsights Resource](../../azure-monitor/app/create-new-resource.md )

Steg

1. Uppdatera filen pom.xml av Spring Boot-program och Lägg till följande beroenden i den:

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
2. Uppdatera filen application.properties eller yml med Application Insights-Instrumentationen nyckeln med hjälp av följande egenskap:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Skapa ditt program och köra
2. Ovanstående bör komma igång och körs med automatisk preaggregeras mått samlas in till Azure Monitor. Information om hur du finjusterar Application Insights Spring Boot starter finns i den [viktigt på GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Med Spring 2.x

Lägg till följande beroenden till pom.xml eller build.gradle-fil:

* Application Insights Spring boot starter 2.1.2 eller senare
* Azure-spring-boot-mått-startare 2.0.7 eller senare  
* [Application Insights-resurs](../../azure-monitor/app/create-new-resource.md )

Steg:

1. Uppdatera filen pom.xml av Spring Boot-program och Lägg till följande beroende i den:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Uppdatera filen application.properties eller yml med Application Insights-Instrumentationen nyckeln med hjälp av följande egenskap:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Skapa ditt program och köra
4. Ovanstående bör hjälper dig att köra med preaggregeras mått automatiskt insamlade till Azure Monitor. Information om hur du finjusterar Application Insights Spring Boot starter finns i den [viktigt på GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Standard mått:

*    Konfigureras automatiskt mått för Tomcat, JVM, Logback mått, Log4J mätvärden, drifttid mått, Processor mått, FileDescriptorMetrics.
*    Till exempel om netflix hystrix finns på klassökvägen få vi dessa mått samt. 
*    Följande mått kan vara tillgängliga genom att lägga till respektive beans. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcaseCache, Jcache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 mått 
        - Kafka Metrics 

 

Hur du stänger av automatisk mått samling: 
 
- JVM-mått: 
    - management.metrics.binders.jvm.enabled=false 
- Logback mått: 
    - management.metrics.binders.logback.enabled=false
- Drifttid mått: 
    - management.metrics.binders.uptime.enabled=false 
- Processor mått:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Hystrix mått om biblioteket på klassökvägen: 
    - Management.Metrics.binders.hystrix.Enabled=False 
- AspectJ mått om biblioteket på klassökvägen: 
    - spring.aop.Enabled=False 

> [!NOTE]
> Ange egenskaperna ovan i filen application.properties eller application.yml för Spring Boot-program

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Använda mikrometer med Spring Boot-webbprogram

Lägg till följande beroenden till pom.xml eller build.gradle-fil:
 
* [Application Insight Core 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) eller senare
* [Application Insights Web 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) eller senare
* [Registrera webbfiltret](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* Mikrometer Azure registret 1.1.0 eller senare
* [Application Insights-resurs](../../azure-monitor/app/create-new-resource.md )

Steg:

1. Lägg till följande beroenden i din pom.xml eller build.gradle-fil:

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

2. Placera programmet Insights.xml i resursmappen

    Exemplet Servlet-klass (genererar ett timer-mått):

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

      Exempel på konfigurationsklass:

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

Läs mer om mätvärden i [mikrometer dokumentation](https://micrometer.io/docs/).

Andra exempelkoden om hur du skapar olika typer av mått finns i[officiella mikrometer GitHub-lagringsplatsen](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Hur du binder ytterligare mått samling

### <a name="springbootspring"></a>SpringBoot/Spring

Skapa en böna av respektive måttkategorin. Anta exempelvis att vi behöver Guava cachestatistik:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Det finns flera mått som inte är aktiverade som standard, men kan vara kopplat på sättet som ovan. En fullständig lista finns i [officiella mikrometer GitHub-lagringsplatsen](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Icke-Spring-appar
Lägg till följande bindningskod i konfigurationsfilen:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om mikrometer referera till officiellt [mikrometer dokumentation](https://micrometer.io/docs).
* Vill veta mer om Spring på Azure som refererar till officiellt [Spring på dokumentation om Azure](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
