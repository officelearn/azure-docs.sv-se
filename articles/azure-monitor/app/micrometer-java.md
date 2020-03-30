---
title: Så här använder du Micrometer med Azure Application Insights Java SDK
description: En steg för steg guide om hur du använder Micrometer med din Application Insights Spring Boot och icke-Spring Boot applikationer.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: dd04087db32f0bbfa75dafa7e12c355e5ab7b515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670074"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Så här använder du Micrometer med Azure Application Insights Java SDK
Micrometer program övervakning mäter mått för JVM-baserad programkod och låter dig exportera data till din favorit övervakningssystem. Den här artikeln kommer att lära dig hur du använder Micrometer med Application Insights för både Spring Boot och icke-Spring Boot applikationer.

## <a name="using-spring-boot-15x"></a>Använda Spring Boot 1.5x
Lägg till följande beroenden i filen pom.xml eller build.gradle: 
* [Application Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 eller senare
* Micrometer Azure Registry 1.1.0 eller senare
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 eller högre (detta backports autoconfig koden i våren ram).
* [ApplicationInsights-resurs](../../azure-monitor/app/create-new-resource.md )

Steg

1. Uppdatera filen pom.xml i fjädrastartprogrammet och lägg till följande beroenden i det:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
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
2. Uppdatera filen application.properties eller yml med application insights instrumentation-tangenten med hjälp av följande egenskap:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Skapa ditt program och kör
2. Ovanstående bör få dig igång med föraggregerade mått automatiskt samlas in till Azure Monitor. Mer information om hur du finjusterar Application Insights Spring Boot starter finns i [readme på GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Använda fjädra 2.x

Lägg till följande beroenden i filen pom.xml eller build.gradle:

* Application Insights Spring-boot-starter 2.1.2 eller högre
* Azure-spring-boot-metrics-starters 2.0.7 eller senare
* [Resurs för programinsikter](../../azure-monitor/app/create-new-resource.md )

Steg:

1. Uppdatera filen pom.xml i fjädrastartprogrammet och lägg till följande beroende i det:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Uppdatera filen application.properties eller yml med application insights instrumentation-tangenten med hjälp av följande egenskap:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Skapa ditt program och kör
4. Ovanstående bör få dig att köra med föraggregerade mått automatiskt samlas in till Azure Monitor. Mer information om hur du finjusterar Application Insights Spring Boot starter finns i [readme på GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Standardmått:

*    Automatiskt konfigurerade mått för Tomcat, JVM, logbackmått, Log4J-mått, upptidsmått, processormått, FileDescriptorMetrics.
*    Om Netflix Hystrix till exempel finns på klasssökvägen får vi även dessa mätvärden. 
*    Följande mått kan vara tillgängliga genom att lägga till respektive bönor. 
        - CacheMetrics (KoffeinCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - BryggaMetri 
        - OkHttp3-mått 
        - Kafka Statistik 

 

Så här inaktiverar du automatisk statistiksamling: 
 
- JVM-mått: 
    - management.metrics.binders.jvm.enabled=false 
- Inloggningsmått: 
    - management.metrics.binders.logback.enabled=false
- Uptime-mått: 
    - management.metrics.binders.uptime.enabled=false 
- Processormått:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Hystrix-mått om biblioteket på klasssökvägen: 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ-mått om biblioteket på klasssökvägen: 
    - spring.aop.enabled=false 

> [!NOTE]
> Ange egenskaperna ovan i application.properties eller application.yml-filen för spring boot-programmet

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Använd Micrometer med webbapplikationer som inte är fjädrar

Lägg till följande beroenden i filen pom.xml eller build.gradle:

* Application Insights Web Auto 2.5.0 eller senare
* Micrometer Azure Registry 1.1.0 eller senare
* [Resurs för programinsikter](../../azure-monitor/app/create-new-resource.md )

Steg:

1. Lägg till följande beroenden i filen pom.xml eller build.gradle:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. Placera `ApplicationInsights.xml` filen i resursmappen:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
    
       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
    
       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>
    
       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. Exempel på Servlet-klass (avger ett timermått):

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

4. Exempelkonfigurationsklass:

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

Mer information om mått finns i dokumentationen till [mikrometern](https://micrometer.io/docs/).

Andra exempelkod om hur du skapar olika typer av mått finns i[den officiella Micrometer GitHub-repo .](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)

## <a name="how-to-bind-additional-metrics-collection"></a>Så här binder du ytterligare måttinsamling

### <a name="springbootspring"></a>SpringBoot/Fjäder

Skapa en böna för respektive måttkategori. Anta till exempel att vi behöver Guava-cachemått:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Det finns flera mått som inte är aktiverade som standard men som kan bindas på ovanstående sätt. En fullständig lista finns [i den officiella Repo för Micrometer GitHub.](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )

### <a name="non-spring-apps"></a>Appar som inte är fjädrar
Lägg till följande bindningskod i konfigurationsfilen:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Nästa steg

* Mer information om Micrometer finns i den officiella [dokumentationen för Mikrometer](https://micrometer.io/docs).
* Mer information om Våren på Azure finns i den officiella [fjädra på Azure-dokumentation](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
