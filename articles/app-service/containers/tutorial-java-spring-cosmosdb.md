---
title: Skapa Java-webbapp i Linux – Azure App Service
description: Skapa, distribuera och skala Spring Boot Java-webbappar med Azure App Service på Linux och Azure Cosmos DB.
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: e148165afd91d76917e06577198950ee7552995f
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107724"
---
# <a name="tutorial-build-a-java-web-app-using-spring-and-azure-cosmos-db"></a>Självstudie: Skapa ett Java-webbprogram med Spring och Azure Cosmos DB

Den här självstudien vägleder dig genom processen för att skapa, konfigurera, distribuera och skala Java-webbappar på Azure. När du är klar har du en [Spring Boot](https://projects.spring.io/spring-boot/)-app som lagrar data i [Azure Cosmos DB](/azure/cosmos-db) och körs på [Azure App Service på Linux](/azure/app-service/containers).

![Java-app som körs i Azure App Service](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Cosmos DB-databas.
> * Ansluta en exempelapp till databasen och testa den lokalt
> * Distribuera exempelappen till Azure
> * Strömma diagnostikloggar från App Service
> * Lägga till ytterligare instanser för att skala ut exempelappen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Azure CLI](https://docs.microsoft.com/cli/azure/overview) installerat på din egen dator. 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven 3.](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Klona TODO-appen och förbereda lagringsplatsen

I den här självstudien används en TODO-exempelapp med ett webbgränssnitt som anropar REST-API för Spring som backas upp av [Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). Koden för appen är tillgänglig [på GitHub](https://github.com/Microsoft/spring-todo-app). Mer information om hur du skriver Java-appar med Spring och Cosmos DB finns i [självstudien om Spring Boot Starter med Azure Cosmos DB SQL API](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) och [Spring Data Azure Cosmos DB-snabbstarten](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Kör följande kommandon i terminalen för att klona lagringsplatsexemplet och konfigurera exempelappmiljön.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Skapa ett Azure Cosmos DB-konto

Följ dessa steg för att skapa en Azure Cosmos DB-databas i din prenumeration. TODO-listappen ansluts till databasen och lagrar sina data under körning och sparar programtillståndet oavsett var du kör programmet.

1. Logga in din Azure CLI och ange din prenumeration om du vill och har fler än en ansluten till dina inloggningsuppgifter.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Skapa en Azure-resursgrupp och notera resursgruppens namn.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Skapa Azure Cosmos DB av typen `GlobalDocumentDB`. Namnet på Cosmos DB får endast innehålla gemena bokstäver. Anteckna fältet `documentEndpoint` i svaret från kommandot.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Hämta din Azure Cosmos DB-nyckel för att ansluta till appen. Behåll `primaryMasterKey`, `documentEndpoint` i närheten eftersom du behöver dem i nästa steg.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Konfigurera egenskaper för TODO-appen

Öppna en terminal på din dator. Kopiera exempelskriptfilen i den klonade lagringsplatsen så att du kan anpassa den för din Cosmos DB-databas som du nyss skapade.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Redigera `.scripts/set-env-variables.sh` i din favoritredigerare och ange anslutningsinformation för Azure Cosmos DB. För App Service Linux-konfigurationen måste du använda samma region som tidigare (`your-resource-group-region`) och resursgruppen (`your-azure-group-name`) som användes när du skapade Cosmos DB-databasen. Välj ett WEBAPP_NAME som är unikt eftersom det inte kan duplicera alla webbappnamn i alla Azure-distributioner.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

// App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Kör sedan skriptet:

```bash
source .scripts/set-env-variables.sh
```
   
Dessa miljövariabler används i `application.properties` i TODO-listappen. Fälten i egenskapsfilen konfigurerar en standardkonfiguration för databasen för Spring Data:

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

Exempelappen använder sedan den `@Document` anteckning som importerats från `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` för att ställa in en entitetstyp som ska lagras och hanteras av Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Använd Maven för att köra exemplet.

```bash
mvn package spring-boot:run
```

Resultatet bör likna följande.

```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

Du kan komma åt Spring TODO-appen lokalt med hjälp av den här länken när appen startas: [http://localhost:8080/](http://localhost:8080/).

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Om du ser undantag istället för meddelandet ”Started TodoApplication” (Startade TODO-program) kontrollerar du att skriptet `bash` i föregående steg exporterade miljövariablerna korrekt och att värdena är korrekta för Azure Cosmos DB-databasen du skapade.

## <a name="configure-azure-deployment"></a>Konfigurera Azure-distribution

Öppna filen `pom.xml` i katalogen `initial/spring-boot-todo` och lägg till följande konfiguration av [Maven-pluginprogram för Azure App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md).

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.4.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>Distribuera till App Service i Linux

Använd `azure-webapp:deploy` Maven-målet för att distribuera TODO-appen till Azure App Service i Linux.

```bash

// Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.4.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

Utdata innehåller URL till dina distribuerade program (i det här exemplet `https://spring-todo-app.azurewebsites.net` ). Du kan kopiera denna URL i webbläsaren eller köra följande kommando i terminalfönstret för att läsa in din app.

```bash
open https://spring-todo-app.azurewebsites.net
```

Du bör se när appen körs med fjärrwebbadressen i adressfältet:

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="view-logs-to-troubleshoot-the-app"></a>Visa loggar för att felsöka appen

Aktivera loggning för den distribuerade Java-webbappen i App Service i Linux:

```bash
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
  --web-server-logging filesystem
```

Sedan strömmas webbapploggarna till terminalen:

```bash
az webapp log tail --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME}
```

Du ser de senaste raderna med utdata och när nya begäranden görs för TODO-appen strömmas de på konsolen. Använd CTRL + C om du vill avsluta konsolen.

```bash
bash-3.2$ az webapp log tail --name ${WEBAPP_NAME}  --resource-group ${RESOURCEGROUP_NAME}
2018-10-28T22:50:17  Welcome, you are now connected to log-streaming service.
2018-10-28T22:44:56.265890407Z   _____                               
2018-10-28T22:44:56.265930308Z   /  _  \ __________ _________   ____  
2018-10-28T22:44:56.265936008Z  /  /_\  \___   /  |  \_  __ \_/ __ \ 
2018-10-28T22:44:56.265940308Z /    |    \/    /|  |  /|  | \/\  ___/ 
2018-10-28T22:44:56.265944408Z \____|__  /_____ \____/ |__|    \___  >
2018-10-28T22:44:56.265948508Z         \/      \/                  \/ 
2018-10-28T22:44:56.265952508Z A P P   S E R V I C E   O N   L I N U X
2018-10-28T22:44:56.265956408Z Documentation: https://aka.ms/webapp-linux
2018-10-28T22:44:56.266260910Z Setup openrc ...
2018-10-28T22:44:57.396926506Z Service `hwdrivers' needs non existent service `dev'
2018-10-28T22:44:57.397294409Z  * Caching service dependencies ... [ ok ]
2018-10-28T22:44:57.474152273Z Starting ssh service...
...
...
2018-10-28T22:46:13.432160734Z [INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
2018-10-28T22:46:13.744859424Z [INFO] TomcatWebServer - Tomcat started on port(s): 80 (http) with context path ''
2018-10-28T22:46:13.783230205Z [INFO] TodoApplication - Started TodoApplication in 57.209 seconds (JVM running for 70.815)
2018-10-28T22:46:14.887366993Z 2018-10-28 22:46:14.887  INFO 198 --- [p-nio-80-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-10-28T22:46:14.887637695Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization started
2018-10-28T22:46:14.998479907Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization completed in 111 ms

2018-10-28T22:49:20.572059062Z Sun Oct 28 22:49:20 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:25.850543080Z Sun Oct 28 22:49:25 GMT 2018 DELETE ======= /api/todolist/{4f41ab03-1b12-4131-a920-fe5dfec106ca} ======= 
2018-10-28T22:49:26.047126614Z Sun Oct 28 22:49:26 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:30.201740227Z Sun Oct 28 22:49:30 GMT 2018 POST ======= /api/todolist ======= Milk
2018-10-28T22:49:30.413468872Z Sun Oct 28 22:49:30 GMT 2018 GET ======= /api/todolist =======
```

## <a name="scale-out-the-todo-app"></a>Skala ut TODO-appen

Skala ut programmet genom att lägga till en annan arbetsroll:

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de här resurserna för en annan självstudie (se [Nästa steg](#next)) kan du ta bort dem genom att köra följande kommando i Cloud Shell: 
  
```bash
az group delete --name your-azure-group-name
``` 

<a name="next"></a>

## Next steps

[Azure for Java Developers](/java/azure/)
[Spring Boot](https://spring.io/projects/spring-boot), 
[Spring Data for Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), 
[Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction)
and
[App Service Linux](/azure/app-service/containers/app-service-linux-intro).

Learn more about running Java apps on App Service on Linux in the developer guide.

> [!div class="nextstepaction"] 
> [Java in App Service Linux dev guide](/azure/app-service/containers/app-service-linux-java)
