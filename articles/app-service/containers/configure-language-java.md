---
title: Konfigurera Linux Java-appar – Azure App Service | Microsoft Docs
description: Lär dig hur du konfigurerar Java-appar som körs i Azure App Service i Linux.
keywords: azure app service, web app, linux, oss, java, java ee, jee, javaee
services: app-service
author: bmitchell287
manager: douge
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 1488dbdcc042b29880560e7255de96b8d0409779
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498509"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurera en Linux Java-app för Azure App Service

Azure App Service i Linux kan Java-utvecklare snabbt bygga, distribuera och skala sina Tomcat-eller Java Standard Edition-paketerade webb program på en fullständigt hanterad Linux-baserad tjänst. Distribuera program med maven-plugin-program från kommando raden eller i redigerings program som IntelliJ, Sol förmörkelse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service följer du själv studie kursen [Java snabb start](quickstart-java.md) och [Java med postgresql](tutorial-java-enterprise-postgresql-app.md) .

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [maven-plugin-programmet för Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) för att distribuera både jar-och. War-filer. Distribution med populära IDE: er stöds också med [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) eller [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Annars beror distributions metoden på din Arkiv typ:

- Om du vill distribuera. War-filer till Tomcat `/api/wardeploy/` använder du slut punkten för att publicera Arkiv filen. Mer information om det här API: et finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Om du vill distribuera. jar-filer på Java se-avbildningarna använder `/api/zipdeploy/` du slut punkten för kudu-webbplatsen. Mer information om det här API: et finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Distribuera inte. War eller. jar med FTP. FTP-verktyget är utformat för att ladda upp start skript, beroenden eller andra runtime-filer. Det är inte det bästa valet för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Logga och Felsök appar

Prestanda rapporter, trafik visualiseringar och hälso checkups är tillgängliga för varje app via Azure Portal. Mer information finns i [Översikt över Azure App Service-diagnostik](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Åtkomst till SSH-konsol

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Mer information finns i [strömmande loggar med Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Loggning av app

Aktivera [program loggning](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) via Azure Portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) för att konfigurera App Service att skriva programmets standard-och standard konsol fel strömmar till det lokala fil systemet eller Azure-Blob Storage. Loggning till den lokala App Service fil Systems instansen är inaktive rad 12 timmar efter att den har kon figurer ATS Om du behöver kvarhållare kan du konfigurera programmet att skriva utdata till en Blob Storage-behållare. Du hittar dina program loggar för Java och Tomcat i katalogen */Home/LogFiles/Application/* .

Om ditt program använder [logback](https://logback.qos.ch/) eller [log4j](https://logging.apache.org/log4j) för spårning kan du vidarebefordra de här spårningarna för granskning till Azure Application Insights med hjälp av konfigurations anvisningar för loggnings ramverk i [utforska Java trace-loggar i Application Insights ](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Felsökningsverktyg

De inbyggda Java-avbildningarna baseras på operativ systemet [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . `apk` Använd Package Manager för att installera eventuella fel söknings verktyg eller kommandon.

### <a name="flight-recorder"></a>Flyg inspelning

Alla Linux Java-avbildningar på App Service har Zulu Flight-brännare installerad så att du enkelt kan ansluta till JVM och starta en profilerings inspelning eller generera en heap-dump.

#### <a name="timed-recording"></a>Inspelnings tid

Kom igång genom att använda SSH i app service och köra `jcmd` kommandot för att se en lista över alla Java-processer som körs. Förutom jcmd bör du se ditt Java-program med ett process-ID-nummer (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Kör kommandot nedan för att starta en 30-sekunders inspelning av JVM. Detta kommer att profilera JVM och skapa en JFR-fil med namnet *jfr_example. jfr* i arbets katalogen. (Ersätt 116 med PID för din Java-app.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Under det 30 andra intervallet kan du verifiera att inspelningen sker genom att köra `jcmd 116 JFR.check`. Då visas alla inspelningar för den aktuella Java-processen.

#### <a name="continuous-recording"></a>Kontinuerlig inspelning

Du kan använda Zulu Flight-brännare för att kontinuerligt profilera ditt Java-program med minimal påverkan på körnings prestanda ([källa](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Det gör du genom att köra följande Azure CLI-kommando för att skapa en app-inställning med namnet JAVA_OPTS med den nödvändiga konfigurationen. Innehållet i inställningen för JAVA_OPTS-appen skickas till `java` kommandot när appen startas.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

När inspelningen har startats kan du när som helst dumpa de aktuella inspelnings data med `JFR.dump` hjälp av kommandot.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Mer information finns i [kommando referensen för Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analysera inspelningar

Använd [FTPS](../deploy-ftp.md) för att ladda ned jfr-filen till den lokala datorn. Om du vill analysera JFR-filen laddar du ned och installerar [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Anvisningar om hur du Zulu uppdrags kontroll finns i [Azul-dokumentationen](https://docs.azul.com/zmc/) och i [installations anvisningarna](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service för Linux stöder direkt justering och anpassning genom Azure Portal och CLI. Läs följande artiklar för konfiguration av icke-Java-en webbapp:

- [Konfigurera appinställningar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Konfigurera en anpassad domän](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Aktivera SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Lägg till en CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurera kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ange Java Runtime-alternativ

Om du vill ange allokerat minne eller andra alternativ för JVM körning i båda miljöerna Tomcat och Java se skapar du `JAVA_OPTS` en app- [inställning](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) med namnet med alternativen. App Service Linux skickar den här inställningen som en miljö variabel till Java-körningsmiljön när den startas.

I Azure Portal, under **program inställningar** för webbappen, skapar du en ny app-inställning med `JAVA_OPTS` namnet som innehåller de ytterligare `-Xms512m -Xmx1204m`inställningarna, till exempel.

Om du vill konfigurera appens inställning från maven-plugin-programmet lägger du till inställningar/värde-Taggar i Azure plugin-avsnittet. I följande exempel anges en angiven minsta och högsta Java-Heap-storlek:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Utvecklare som kör ett enda program med en distributions plats i sin App Service plan kan använda följande alternativ:

- B1 och S1-instanser:`-Xms1024m -Xmx1024m`
- B2-och S2-instanser:`-Xms3072m -Xmx3072m`
- B3 och S3-instanser:`-Xms6144m -Xmx6144m`

När du finjusterar inställningarna för programheap granskar du App Service plan information och tar hänsyn till flera program och distributions fack måste hitta den optimala allokeringen av minnet.

Om du distribuerar ett JAR-program ska det heta *app. jar* så att den inbyggda avbildningen kan identifiera din app korrekt. (Plugin-programmet maven byter namn på det automatiskt.) Om du inte vill byta namn på JAR till *app. jar*kan du ladda upp ett shell-skript med kommandot för att köra din jar. Klistra sedan in den fullständiga sökvägen till skriptet i text rutan för [Start filen](app-service-linux-faq.md#built-in-images) i konfigurations avsnittet i portalen.

### <a name="turn-on-web-sockets"></a>Aktivera webb-Sockets

Aktivera stöd för Web Sockets i Azure Portal i **program inställningarna** för programmet. Du måste starta om programmet för att inställningen ska börja gälla.

Aktivera WebSocket-stöd med hjälp av Azure CLI med följande kommando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starta sedan om ditt program:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ange standard tecken kodning

Skapa en ny app-inställning med namnet `JAVA_OPTS` med värde `-Dfile.encoding=UTF-8`under **program inställningar** för webbappen i Azure Portal.

Du kan också konfigurera appens inställning med hjälp av App Service maven-plugin-programmet. Lägg till inställnings namn och värde Taggar i plugin-konfigurationen:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Justera tids gräns för start

Om Java-programmet är särskilt stort bör du öka tids gränsen för start. Om du vill göra det skapar du en program `WEBSITES_CONTAINER_START_TIME_LIMIT` inställning och anger den till det antal sekunder som App Service ska vänta innan timeout. Det maximala värdet är `1800` sekunder.

### <a name="pre-compile-jsp-files"></a>Kompilera JSP-filer i förväg

För att förbättra prestanda för Tomcat-program kan du kompilera JSP-filerna innan du distribuerar till App Service. Du kan använda [plugin-programmet maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) som tillhandahålls av Apache sling eller med hjälp av den här [Ant build-filen](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service för Linux har samma uppsättning [rekommenderade säkerhets metoder](/azure/security/security-paas-applications-using-app-services) som andra program.

### <a name="authenticate-users-easy-auth"></a>Autentisera användare (enkel autentisering)

Konfigurera app-autentisering i Azure Portal med alternativet **autentisering och auktorisering** . Därifrån kan du aktivera autentisering med Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Azure Portal konfiguration fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [Konfigurera din app service app för att använda Azure Active Directory inloggning](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) och relaterade artiklar för andra identitets leverantörer. Om du behöver aktivera flera inloggnings leverantörer följer du anvisningarna i artikeln [anpassa App Service-autentisering](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) .

#### <a name="tomcat-and-wildfly"></a>Tomcat och WildFly

Ditt Tomcat-eller WildFly-program kan komma åt användarens anspråk direkt från servlet genom att omvandla huvudobjektet till ett kart objekt. Kart-objektet mappar varje anspråks typ till en samling av anspråken för den typen. I koden nedan `request` är en instans av `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kan du kontrol lera `Map` objektet för ett enskilt anspråk. Följande kodfragment itererar till exempel igenom alla anspråks typer och skriver ut innehållet i varje samling.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Använd `/.auth/ext/logout` sökvägen för att logga ut användare. Om du vill utföra andra åtgärder kan du läsa dokumentationen om [App Service autentisering och auktorisering](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Det finns också en officiell dokumentation om Tomcat [HttpServletRequest-gränssnittet](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) och dess metoder. Följande servlet-metoder har också dehydratiseras baserat på din App Service konfiguration:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Om du vill inaktivera den här funktionen skapar du en `WEBSITE_AUTH_SKIP_PRINCIPAL` program inställning med namnet `1`med värdet. Om du vill inaktivera alla servlet-filter som har lagts till av `WEBSITE_SKIP_FILTERS` App Service skapar du en `1`inställning med namnet med värdet.

#### <a name="spring-boot"></a>Spring Boot

Förstarts utvecklare kan använda [Start Start programmet Azure Active Directory våren](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) för att skydda program med hjälp av välbekanta säkerhets anteckningar och API: er. Se till att öka storleken på den maximala sidhuvuden i *program. Properties* -filen. Vi föreslår ett värde av `16384`.

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ instruktionerna i [BIND ett befintligt anpassat SSL-certifikat](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) för att ladda upp ett befintligt SSL-certifikat och bind det till programmets domän namn. Som standard tillåter programmet fortfarande HTTP-anslutningar – Följ de specifika stegen i självstudien för att genomdriva SSL och TLS.

### <a name="use-keyvault-references"></a>Använda nyckel Valvs referenser

[Azure](../../key-vault/key-vault-overview.md) -nyckels valvet tillhandahåller centraliserad hemlig hantering med åtkomst principer och gransknings historik. Du kan lagra hemligheter (till exempel lösen ord eller anslutnings strängar) i ett nyckel valv och komma åt dessa hemligheter i ditt program via miljövariabler.

Börja med att följa anvisningarna för [att ge appen åtkomst till Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och [skapa en nyckel valv referens till din hemlighet i en program inställning](../app-service-key-vault-references.md#reference-syntax). Du kan kontrol lera att referensen matchar hemligheten genom att skriva ut miljövariabeln och fjärrans luta till App Service terminalen.

Om du vill mata in dessa hemligheter i din fjäder-eller Tomcat-konfigurationsfil använder du miljövariabeln insprutning-syntax (`${MY_ENV_VAR}`). För våren-konfigurationsfiler läser du den här dokumentationen om de [externa konfigurationerna](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Konfigurera APM-plattformar

Det här avsnittet visar hur du ansluter Java-program som distribuerats på Azure App Service i Linux med NewRelic-och AppDynamics-plattformarna för program prestanda övervakning (APM).

[Konfigurera nya Relic](#configure-new-relic)
[Konfigurera AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Konfigurera nya Relic

1. Skapa ett NewRelic-konto på [NewRelic.com](https://newrelic.com/signup)
2. Hämta Java-agenten från NewRelic kommer den att ha ett fil namn som liknar *newrelic-Java-x. x. x. zip*.
3. Kopiera licens nyckeln, du behöver den för att konfigurera agenten senare.
4. [Använda SSH i App Service](app-service-linux-ssh-support.md) -instansen och skapa en ny katalog */Home/site/wwwroot/APM*.
5. Överför de uppackade NewRelic Java agent-filerna till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/newrelic*.
6. Ändra YAML-filen på */Home/site/wwwroot/APM/newrelic/newrelic.yml* och ersätt licens värdet för plats hållaren med din egen licens nyckel.
7. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om din app använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet. `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du använder **WildFly**kan du läsa mer i den nya Relic [-dokumentationen för](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) att få vägledning om hur du installerar Java-agenten och JBoss-konfigurationen.
    - Om du redan har en miljö variabel för `JAVA_OPTS` eller `CATALINA_OPTS`lägger `javaagent` du till alternativet i slutet av det aktuella värdet.

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

1. Skapa ett AppDynamics-konto på [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Hämta Java-agenten från AppDynamics-webbplatsen kommer fil namnet att likna *AppServerAgent-x. x. x. XXXXX. zip*
3. [Använda SSH i App Service](app-service-linux-ssh-support.md) -instansen och skapa en ny katalog */Home/site/wwwroot/APM*.
4. Överför Java-agentens filer till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/AppDynamics*.
5. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om du använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.
    - Om du använder **WildFly**kan du läsa mer i AppDynamics [-dokumentationen om](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) hur du installerar Java-agenten och JBoss-konfigurationen.

## <a name="configure-jar-applications"></a>Konfigurera JAR-program

### <a name="starting-jar-apps"></a>Starta JAR-appar

Som standard förväntar App Service att JAR-programmet ska ha namnet *app. jar*. Om det har det här namnet kommer det att köras automatiskt. För maven-användare kan du ange jar-namnet genom att `<finalName>app</finalName>` inkludera `<build>` i avsnittet i din *Pom. XML*. [Du kan göra samma sak i Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) genom att `archiveFileName` ange egenskapen.

Om du vill använda ett annat namn för din JAR måste du också ange [Start kommandot](app-service-linux-faq.md#built-in-images) som kör jar-filen. Till exempel `java -jar my-jar-app.jar`. Du kan ställa in värdet för ditt Start kommando i portalen, under konfiguration > allmänna inställningar eller med en program inställning med namnet `STARTUP_COMMAND`.

### <a name="server-port"></a>Server Port

App Service Linux dirigerar inkommande begär anden till port 80, så att ditt program även lyssnar på port 80. Du kan göra detta i programmets konfiguration (t *. ex. vår app. Properties* -fil) eller i ditt Start kommando (till exempel `java -jar spring-app.jar --server.port=80`). Se följande dokumentation för vanliga Java-ramverk:

- [Våren Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play-ramverk](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Datakällor

### <a name="tomcat"></a>Tomcat

Dessa anvisningar gäller för alla databas anslutningar. Du måste fylla i plats hållarna med den valda databasens driv rutins klass namn och JAR-fil. Det angivna är en tabell med klass namn och hämtning av driv rutiner för vanliga databaser.

| Databas   | Klass namn för driv rutin                             | JDBC Driver                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Ladda ned](https://dev.mysql.com/downloads/connector/j/) (Välj plattform oberoende) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Om du vill konfigurera Tomcat för att använda Java Database Connectivity (JDBC) eller Java-persistence API (JPA), `CATALINA_OPTS` måste du först anpassa den miljö variabel som läses in av Tomcat vid start. Ange dessa värden via en app-inställning i [maven-plugin-programmet för App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariabler på sidan Inställningar för **konfigurations** > **program** i Azure Portal.

Ta sedan reda på om data källan ska vara tillgänglig för ett program eller för alla program som körs på Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Data källor på program nivå

1. Skapa en *context. XML-* fil i projektets *meta-inf/* katalog. Skapa *meta-inf/-* katalogen om den inte finns.

2. I *context. XML*lägger du till `Context` ett-element för att länka data källan till en JNDI-adress. `driverClassName` Ersätt plats hållaren med driv Rutinens klass namn från tabellen ovan.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Uppdatera programmets *Web. XML* så att data källan används i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Delade resurser på server nivå

1. Kopiera innehållet i */usr/local/Tomcat/conf* till */home/tomcat/conf* på din app service Linux-instans med SSH om du inte redan har en konfiguration.

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Lägg till ett kontext element i *Server. XML* i `<Server>` elementet.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. Uppdatera programmets *Web. XML* så att data källan används i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Slutför konfiguration

Till sist placerar du driv rutins jar v7 i Tomcat-classpath och startar om App Service.

1. Se till att JDBC-drivrutinsfiler är tillgängliga för Tomcat-classloader genom att placera dem i */Home/Tomcat/lib* -katalogen. (Skapa den här katalogen om den inte redan finns.) Utför följande steg för att överföra filerna till din App Service-instans:

    1. I [Cloud Shell](https://shell.azure.com)installerar du webapp-tillägget:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Kör följande CLI-kommando för att skapa en SSH-tunnel från det lokala systemet till App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Anslut till den lokala tunnel porten med din SFTP-klient och överför filerna till mappen */Home/Tomcat/lib* .

    Alternativt kan du använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [anvisningarna för att hämta dina FTP-autentiseringsuppgifter](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Om du har skapat en data källa på server nivå startar du om App Service Linux-programmet. Tomcat kommer att `CATALINA_HOME` återställas till `/home/tomcat/conf` och använda den uppdaterade konfigurationen.

### <a name="spring-boot"></a>Spring Boot

För att ansluta till data källor i Start program för våren rekommenderar vi att du skapar anslutnings strängar och infogar dem i *appens. egenskaps* fil.

1. I avsnittet "konfiguration" på sidan App Service anger du ett namn för strängen, klistrar in JDBC-anslutningssträngen i fältet värde och anger typen anpassad. Du kan också ange den här anslutnings strängen som plats inställning.

    Den här anslutnings strängen är tillgänglig för vårt program som en miljö variabel `CUSTOMCONNSTR_<your-string-name>`med namnet. Den anslutnings sträng som vi skapade ovan får exempelvis namnet `CUSTOMCONNSTR_exampledb`.

2. I din *app. Properties* -fil refererar du till den här anslutnings strängen med miljö variabel namnet. I vårt exempel skulle vi använda följande.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Mer information om det här ämnet finns i [vår Boot-dokumentation om data åtkomst](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) och [externa konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="configure-java-ee-wildfly"></a>Konfigurera Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition på App Service Linux är för närvarande en för hands version. Den här stacken rekommenderas **inte** för produktion till sitt arbete. information om Java-SE-och Tomcat-stackar.

Med Azure App Service i Linux kan Java-utvecklare bygga, distribuera och skala Java Enterprise-program (Java-program) på en fullständigt hanterad Linux-baserad tjänst.  Den underliggande Java Enterprise runtime-miljön är program servern med öppen källkod [WildFly](https://wildfly.org/) .

Det här avsnittet innehåller följande underavsnitt:

- [Skala med App Service](#scale-with-app-service)
- [Anpassa konfigurationen av program Server](#customize-application-server-configuration)
- [Installera moduler och beroenden](#install-modules-and-dependencies)
- [Konfigurera data källor](#configure-data-sources)
- [Aktivera meddelande tjänster](#enable-messaging-providers)
- [Konfigurera cachelagring av session Management](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Skala med App Service

Den WildFly program server som körs i App Service på Linux körs i fristående läge, inte i en domän konfiguration. När du skalar App Service-planen konfigureras varje WildFly-instans som en fristående server.

Skala ditt program lodrätt eller vågrätt med [skalnings regler](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) och genom [att öka antalet instanser](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Anpassa konfigurationen av program Server

Web App-instanser är tillstånds lösa, så varje ny instans som startas måste konfigureras vid start för att stödja den WildFly-konfiguration som krävs av programmet.
Du kan skriva ett start bash-skript för att anropa WildFly CLI för att:

- Konfigurera data källor
- Konfigurera meddelande leverantörer
- Lägg till andra moduler och beroenden i WildFly Server-konfigurationen.

Skriptet körs när WildFly är igång, men innan programmet startas. Skriptet bör använda [JBoss CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) som anropas från */opt/JBoss/WildFly/bin/JBoss-cli.sh* för att konfigurera program servern med valfri konfiguration eller ändringar som behövs när servern har startats.

Använd inte det interaktiva läget i CLI för att konfigurera WildFly. I stället kan du ange ett skript för kommandon i JBoss CLI med hjälp `--file` av kommandot, till exempel:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Använd FTP för att ladda upp start skriptet till en plats i App Service-instansen under katalogen */Home* , till exempel */Home/site/Deployments/tools*. Mer information finns i [distribuera din app för att Azure App Service att använda FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Ange fältet **Start skript** i Azure Portal till platsen för start gränssnitts skriptet, till exempel */Home/site/Deployments/tools/Your-Startup-script.sh*.

Ange [appinställningar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) i program konfigurationen för att överföra miljövariabler för användning i skriptet. Program inställningarna behåller anslutnings strängar och andra hemligheter som krävs för att konfigurera programmet från versions kontroll.

### <a name="install-modules-and-dependencies"></a>Installera moduler och beroenden

Om du vill installera moduler och deras beroenden i WildFly-classpath via JBoss CLI måste du skapa följande filer i en egen katalog. Vissa moduler och beroenden kan behöva ytterligare konfiguration, t. ex. JNDI namngivning eller annan API-speciell konfiguration, så att listan är en minsta uppsättning av vad du behöver för att konfigurera ett beroende i de flesta fall.

- En [Beskrivning av XML-modulen](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). XML-filen definierar namn, attribut och beroenden för din modul. Den här [exempel filen module. XML](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definierar en postgres-modul, dess jar-fil JDBC-beroende och andra beroenden för modul som krävs.
- Alla nödvändiga JAR-filberoenden för modulen.
- Ett skript med dina JBoss CLI-kommandon för att konfigurera den nya modulen. Den här filen innehåller dina kommandon som ska köras av JBoss CLI för att konfigurera servern att använda beroendet. Dokumentation om kommandon för att lägga till moduler, data källor och meddelande leverantörer finns i [det här dokumentet](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Ett bash start skript för att anropa JBoss CLI och köra skriptet i föregående steg. Den här filen kommer att köras när App Service instansen startas om eller när nya instanser tillhandahålls under en utskalning. Det här start skriptet är där du kan utföra andra konfigurationer för ditt program eftersom JBoss-kommandon skickas till JBoss CLI. Filen kan vara minst ett enda kommando för att skicka ditt JBoss CLI-kommando skript till JBoss CLI:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

När du har filer och innehåll för modulen följer du stegen nedan för att lägga till modulen i WildFly-program servern.

1. Använd FTP för att ladda upp filer till en plats i App Service-instansen under katalogen */Home* , till exempel */Home/site/Deployments/tools*. Mer information finns i [distribuera din app för att Azure App Service att använda FTP/S](../deploy-ftp.md).
2. På sidan**allmänna inställningar** för **konfiguration** > i Azure Portal anger du fältet **Start skript** till platsen för start kommando skriptet, till exempel */Home/site/Deployments/tools/startup.sh*.
3. Starta om App Service-instansen genom att trycka på  knappen **starta om** i översikts avsnittet i portalen eller använda Azure CLI.

### <a name="configure-data-sources"></a>Konfigurera data källor

Om du vill konfigurera WildFly/JBoss för åtkomst till en data källa använder du den allmänna processen som beskrivs ovan i avsnittet "installera moduler och beroenden". Följande avsnitt innehåller detaljerad information om den här processen för PostgreSQL, MySQL och SQL Server data källor.

Det här avsnittet förutsätter att du redan har en app, en App Service instans och en Azure Database Service-instans. Anvisningarna nedan refererar till App Service namn, resurs grupp och information om databas anslutningen. Du hittar den här informationen på Azure Portal.

Om du föredrar att gå igenom hela processen från början med en exempel-app, se [Självstudier: Bygg en Java EE-och postgres-webbapp i](tutorial-java-enterprise-postgresql-app.md)Azure.

I följande steg förklaras kraven för att ansluta befintliga App Service och databaser.

1. Hämta JDBC-drivrutinen för [postgresql](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)eller [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Packa ned det hämtade arkivet för att hämta filen driver. jar.

2. Skapa en fil med ett namn som *module. XML* och Lägg till följande markering. Ersätt plats hållaren (inklusive vinkel paren tes) `org.postgres` med för postgresql `com.mysql` , för MySQL eller `com.microsoft` för SQL Server. `<module name>` Ersätt `<JDBC .jar file path>` med namnet på. jar-filen från föregående steg, inklusive den fullständiga sökvägen till den plats där du vill placera filen i din app service-instans. Detta kan vara vilken plats som helst under katalogen */Home* .

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Skapa en fil med ett namn som *DataSource-commands. CLI* och Lägg till följande kod. Ersätt `<JDBC .jar file path>` med värdet som du använde i föregående steg. Ersätt `<module file path>` med fil namnet och App Service sökvägen från föregående steg, till exempel */Home/module.XML*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Den här filen körs av start skriptet som beskrivs i nästa steg. Den installerar JDBC-drivrutinen som en WildFly-modul, skapar motsvarande WildFly-datakälla och laddar om servern för att se till att ändringarna börjar gälla.

4. Skapa en fil med ett namn som *startup.sh* och Lägg till följande kod. Ersätt `<JBoss CLI script>` med namnet på filen som du skapade i föregående steg. Se till att inkludera den fullständiga sökvägen till platsen där du placerar filen i App Service-instansen, till exempel */Home/DataSource-commands.CLI*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Använd FTP för att ladda upp filen JDBC. jar, modulens XML-fil, JBoss CLI-skriptet och start skriptet till App Service-instansen. Lägg de här filerna på den plats som du angav i föregående steg, till exempel */Home*. Mer information om FTP finns i [distribuera din app för att Azure App Service använda FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Använd Azure CLI för att lägga till inställningar till App Service som innehåller information om databas anslutningen. Ersätt `<resource group>` och`<webapp name>` med de värden som App Service använder. Ersätt `<database server name>` ,`<database name>`, och`<admin password>` med din databas anslutnings information. `<admin name>` Du kan hämta App Service-och databas information från Azure Portal.

    **PostgreSQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    DATABASE_CONNECTION_URL-värdena är olika för varje databas server och skiljer sig från värdena på Azure Portal. URL-formaten som visas här (och i kodfragmenten ovan) krävs för användning av WildFly:

    * **Postgresql:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **Mysql:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. I Azure Portal navigerar du till app service och letar upp sidan**allmänna inställningar** för **konfigurationen** > . Ange **Start skript** fältet till namnet och platsen för start skriptet, till exempel */Home/startup.sh*.

Nästa gången App Service startas om körs start skriptet och de nödvändiga konfigurations stegen utförs. För att testa att den här konfigurationen sker korrekt kan du komma åt App Service med SSH och sedan köra start skriptet själv från bash-prompten. Du kan också granska App Service loggar. Mer information om de här alternativen finns i [Logga och Felsöka appar](#logging-and-debugging-apps).

Sedan måste du uppdatera WildFly-konfigurationen för appen och distribuera den igen. Använd följande steg:

1. Öppna filen *src/main/Resources/meta-inf/persisted. XML* för din app och `<jta-data-source>` hitta elementet. Ersätt innehållet på det sätt som visas här:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Återskapa och distribuera om din app med hjälp av följande kommando i bash-prompten:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Starta om App Service-instansen genom att trycka på  knappen **starta om** i översikts avsnittet i Azure Portal eller med hjälp av Azure CLI.

Din App Service-instans har nu kon figurer ATS för att få åtkomst till din databas.

Mer information om hur du konfigurerar databas anslutning med WildFly finns i [postgresql](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)eller [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="enable-messaging-providers"></a>Aktivera meddelande tjänster

Så här aktiverar du meddelande drivna bönor med Service Bus som meddelande funktion:

1. Använd [meddelande biblioteket Apache QPID JMS Messaging](https://qpid.apache.org/proton/index.html). Inkludera det här beroendet i Pom. XML (eller någon annan build-fil) för programmet.

2. Skapa [Service Bus-resurser](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Skapa en Azure Service Bus namnrymd och kö inom denna namnrymd och en princip för delad åtkomst med funktionerna för att skicka och ta emot.

3. Skicka princip nyckeln för delad åtkomst till din kod antingen med URL-kodning av den primära nyckeln för principen eller [använd Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Följ stegen som beskrivs i avsnittet Installera moduler och beroenden med XML-beskrivningen för XML,. jar-beroenden, JBoss CLI-kommandon och start skript för JMS-providern. Förutom de fyra filerna måste du också skapa en XML-fil som definierar JNDI namn för JMS-kön och ämnet. Se [den här lagrings platsen](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) för referens konfigurationsfiler.

### <a name="configure-session-management-caching"></a>Konfigurera cachelagring av session Management

Som standard använder App Service på Linux cookies för att kontrol lera att klient begär Anden med befintliga sessioner dirigeras till samma instans av programmet. Detta standard beteende kräver ingen konfiguration men har vissa begränsningar:

- Om en program instans startas om eller skalas ned går det inte att gå till sessionstillstånd för användarens session i program servern.
- Om program har timeout-inställningar eller ett fast antal användare, kan det ta lite tid för automatiskt skalade nya instanser att ta emot inläsning eftersom endast nya sessioner dirigeras till de nyligen startade instanserna.

Du kan konfigurera WildFly till att använda ett externt sessionsobjekt, till exempel [Azure cache för Redis](/azure/azure-cache-for-redis/). Du måste [inaktivera den befintliga](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurationen av arr-tillhörigheten för att stänga av sessionen cookie-baserad Routning och tillåta att det konfigurerade WildFly-sessionstillståndet fungerar utan störningar.

## <a name="docker-containers"></a>Docker-containrar

Om du vill använda Azure-Zulu-JDK som stöds i dina behållare, se till att hämta och använda de färdiga avbildningarna enligt dokumentationen från [Azul-Zulu Enterprise för Azures hämtnings sida](https://www.azul.com/downloads/azure-only/zulu/) eller Använd `Dockerfile` exemplen från [Microsoft Java GitHub lagrings platsen ](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Support specifikation

### <a name="runtime-availability"></a>Tillgänglighet för körning

App Service för Linux stöder två körningar för hanterad värd för Java-webb program:

- [Tomcat servlet-behållaren](https://tomcat.apache.org/) för att köra program som paketerade som Web Archive (War)-filer. Versioner som stöds är 8,5 och 9,0.
- Java SE körnings miljö för att köra program som paketeras som filer i Java Archive (JAR). Versioner som stöds är Java 8 och 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azul Zulu Enterprise builds of OpenJDK är en kostnads färdig distribution med flera plattformar av OpenJDK för Azure och Azure Stack som backas upp av Microsoft-och Azul-system. De innehåller alla komponenter för att skapa och köra Java SE-program. Du kan installera JDK från [Java JDK-installationen](https://aka.ms/azure-jdks).

JDKs som stöds korrigeras automatiskt en gång i kvartalet i januari, april, juli och oktober varje år.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Korrigeringar och korrigeringar för viktiga säkerhets problem kommer att släppas så snart de blir tillgängliga från Azul system. En "större" sårbarhet definieras av en baspoäng på 9,0 eller högre på [NIST vanliga sårbarhets bedömnings system, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Utfasning och pensionering

Om en Java-körning som stöds dras tillbaka kommer Azure-utvecklare som använder den berörda körningen att få ett meddelande om förvarning minst sex månader innan körningen dras tillbaka.

## <a name="next-steps"></a>Nästa steg

Besök [Azure för Java Developer](/java/azure/) Center för att hitta Azures snabb starter, självstudier och Java-referens dokumentation.

Allmänna frågor om hur du använder App Service för Linux som inte är särskilt för Java-utveckling besvaras i [vanliga frågor och svar om App Service Linux](app-service-linux-faq.md).
