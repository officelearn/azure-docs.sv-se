---
title: Skapa en Java- och MySQL-webbapp i Azure
description: Lär dig hur du får en Java-app som ansluter till databastjänsten Azure MySQL att fungera i Azure App Service.
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: db1005bbce25b0fa3fec76e6f9428a4cdd6fa4aa
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024383"
---
# <a name="tutorial-build-a-java-and-mysql-web-app-in-azure"></a>Självstudie: Skapa en Java- och MySQL-webbapp i Azure

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera till App Service på _Linux_, se artikeln om att [distribuera en Spring Boot-app i containrar till Azure](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin).
>

I den här självstudien visas hur du skapar en Java-webbapp i Azure och ansluter den till en MySQL-databas. När du är klar har du en [Spring Boot](https://projects.spring.io/spring-boot/)-app som lagrar data i [Azure Database for MySQL](../mysql/overview.md) och körs på [Azure App Service Web Apps](app-service-web-overview.md).

![Java-app som körs i Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapa en MySQL-databas i Azure
> * ansluta en exempelapp till databasen
> * distribuera appen till Azure
> * Uppdatera och distribuera om appen
> * strömma diagnostikloggar från Azure
> * övervaka appen i Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

1. [Ladda ned och installera Git](https://git-scm.com/)
1. [Ladda ned och installera Java 7 JDK eller senare](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [Ladda ned, installera och starta MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Förbereda lokal MySQL 

I det här steget skapar du en databas på en lokal MySQL-server som du sedan använder för att testa appen lokalt på din dator.

### <a name="connect-to-mysql-server"></a>Ansluta till MySQL-server

Öppna ett terminalfönster och anslut till din lokala MySQL-server. Du kan använda det här terminalfönstret för att köra alla kommandon i den här självstudien.

```bash
mysql -u root -p
```

Om du uppmanas att ange ett lösenord anger du lösenordet för `root`-kontot. Se [MySQL: Återställa rotlösenordet](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) om du inte kommer ihåg rotlösenordet för ditt konto.

MySQL-servern är redan igång om kommandot körs utan problem. Om inte, kontrollerar du att den lokala MySQL-servern är igång genom att följa [anvisningarna efter installation av MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Skapa en databas 

Skapa en databas och en tabell för att göra-objekt `mysql`-prompten.

```sql
CREATE DATABASE tododb;
```

Skriv `quit` för att avsluta serveranslutningen.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Skapa och köra exempelappen 

I det här steget klonar du Spring Boot-appen vi använder som exempel, du konfigurerar den att använda den lokala MySQL-databasen och kör den på din dator. 

### <a name="clone-the-sample"></a>Klona exemplet

Gå till terminalfönstret, navigera till en arbetskatalog och klona exempellagringsplatsen. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Konfigurera appen att använda MySQL-databasen

Uppdatera `spring.datasource.password` och värdet i *spring-boot-mysql-todo/src/main/resources/application.properties* med samma rotlösenord som användes för att öppna MySQL-prompten:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

Skapa och köra exemplet med Maven-adaptern som finns på lagringsplatsen:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Öppna din webbläsare med `http://localhost:8080` och se hur exemplet fungerar. När du lägger till uppgifter på listan ska du använda följande SQL-kommandon i MySQL-prompten för att visa de data som lagras i MySQL.

```SQL
use testdb;
select * from todo_item;
```

Avsluta programmet genom att trycka på `Ctrl`+`C` i terminalfönstret. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Skapa en Azure MySQL-databas

I det här steget skapar du en instans av [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) med hjälp av [Azure CLI](/cli/azure/install-azure-cli). Senare under kursen konfigurerar exempelappen att använda den här databasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [`az group create`](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där relaterade resurser som webbappar, databaser och lagringskonton distribueras och hanteras. 

Följande exempel skapar en resursgrupp i regionen Europa, norra:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

Du kan visa vilka möjliga värden du kan använda för `--location` med kommandot [`az appservice list-locations`](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Skapa en MySQL-server

I Cloud Shell skapar du en server i Azure Database for MySQL med kommandot [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create).

I följande kommando byter du ut platshållaren *\<mysql_server_name>* mot ett unikt servernamn, platshållaren *\<admin_user>* mot ett användarnamn och platshållaren *\<admin_password>* mot ett lösenord. Det här servernamnet används som en del av PostgreSQL-slutpunkten (`https://<mysql_server_name>.mysql.database.azure.com`), så namnet måste vara unikt för alla servrar i Azure.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>--location "West Europe" --admin-user <admin_user> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

> [!NOTE]
> Eftersom du använder flera olika autentiseringsuppgifter i den här självstudiekursen är `--admin-user` och `--admin-password` angivna till dummy-värden för att undvika förvirring. I en produktionsmiljö bör du följa rekommenderade säkerhetsmetoder när du väljer ett bra användarnamn och lösenord för MySQL-server i Azure.
>
>

När MySQL-servern skapas visar Azure CLI information som ser ut ungefär så här:

```json
{
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Konfigurera serverbrandväggen

Skapa i Cloud Shell en brandväggsregel för MySQL-servern för att tillåta klientanslutningar med hjälp av kommandot [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create). När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](app-service-ip-addresses.md#find-outbound-ips).
>

## <a name="configure-the-azure-mysql-database"></a>Konfigurera Azure MySQL-databasen

Anslut till MySQL-server i Azure via det lokala terminalfönstret. Använd det värde du angav tidigare för _&lt;mysql_server_name>_. När du uppmanas att ange ett lösenord använder du lösenordet som du angav när du skapade databasen i Azure.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Skapa en databas 

Skapa en databas och en tabell för att göra-objekt `mysql`-prompten.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Skapa en användare med behörigheter

Skapa en databasanvändare och ge användaren alla privilegier i `tododb`-databasen. Ersätt platshållarna `<Javaapp_user>` och `<Javaapp_password>` med dina egna unika appnamn.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Skriv `quit` för att avsluta serveranslutningen.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Distribuera exemplet till Azure App Service

Skapa en Azure App Service-plan med den **kostnadsfria** prisnivån med hjälp av CLI-kommandot [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create). App Service-planen definierar vilka fysiska resurser som används som värd för dina appar. Alla program som tilldelas till en App Service-plan delar dessa resurser. Det innebär att du kan minska kostnaderna när du har flera appar i en plan. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

När planen är klar visar Azure CLI utdata som liknar följande exempel:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Skapa en Azure-webbapp

Använd CLI-kommandot [`az webapp create`](/cli/azure/webapp#az-webapp-create) i Cloud Shell för att skapa en webbappsdefinition i App Service-planen `myAppServicePlan`. Webbappsdefinitionen innehåller en URL för att få åtkomst till appen och konfigurerar flera alternativ för att distribuera koden till Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Ersätt platshållaren `<app_name>` med ditt eget unika appnamn. Det här unika namnet är en del av standarddomännamnet för webbappen. Därför måste namnet vara unikt i förhållande till alla appar i Azure. Du kan mappa en anpassad domännamnspost till webbappen innan du exponerar den för användarna.

När webbappsdefinitionen är klar visar Azure CLI information liknande den i följande exempel: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Konfigurera Java 

Konfigurera i Cloud Shell den Java Runtime-konfiguration som appen behöver med kommandot [`az webapp config set`](/cli/azure/webapp/config#az-webapp-config-set).

Följande kommando konfigurerar webbappen att köra på en sen Java 8 JDK och [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Konfigurera appen att använda Azure SQL-databasen

Ange programinställningar i webbappen om att använda den Azure MySQL-databas du skapade i Azure innan du kör exempelappen. Dessa egenskaper exponeras för webbappen som miljövariabler och åsidosätter de värden som anges i application.properties inuti den paketerade webbappen. 

Ange programinställningar i Cloud Shell med hjälp av [`az webapp config appsettings`](/cli/azure/webapp/config/appsettings) i CLI:

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>Hämta FTP-autentiseringsuppgifter för distribution 
Du kan distribuera webbappen till Azure App Service på flera olika sätt, till exempel med FTP, lokal Git, GitHub, Azure DevOps och Bitbucket. I det här exemplet används FTP för att distribuera WAR-filen som skapats tidigare på din lokala dator till Azure App Service.

Fastställ vilka autentiseringsuppgifter som ska skickas i ett ftp-kommando till webbappen med hjälp av [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment#az-webapp-deployment-list-publishing-profiles)-kommandot i Cloud Shell: 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>Ladda upp appen via FTP

Använd valfritt FTP-verktyg för att distribuera WAR-filen till mappen */site/wwwroot/webapps* på serveradressen som du hittade i fältet `URL` i föregående kommando. Ta bort den befintliga Root-programkatalogen (rot) och ersätt den befintliga ROOT.war med den WAR-fil som skapats tidigare i kursen.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>Testa webbappen

Bläddra till `http://<app_name>.azurewebsites.net/` och lägg till några uppgifter i listan. 

![Java-app som körs i Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Grattis!** Du kör en datadriven Java-app i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Uppdatera appen med ytterligare en kolumn i att göra-listan med information om vilken dag objektet skapades. Spring Boot uppdaterar databasschemat åt dig när datamodellen ändras utan att ändra befintliga databasposter.

1. Öppna i det lokala systemet *src/main/java/com/example/fabrikam/TodoItem.java* och lägg till följande importer i klassen:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Lägg till en `String`-egenskap `timeCreated` i *src/main/java/com/example/fabrikam/TodoItem.java* och inled den med en tidsstämpel när objektet skapas. Lägg till get/set-metoder för den nya `timeCreated`-egenskapen när du redigerar den här filen.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. Uppdatera *src/main/java/com/example/fabrikam/TodoDemoController.java* med en rad i `updateTodo`-metoden för att ange tidsstämpeln:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Lägg till stöd för det nya fältet i `Thymeleaf`-mallen. Uppdatera *src/main/resources/templates/index.html* med en ny tabellrubrik för tidsstämpeln och ett nytt fält att visa värdet för tidsstämpeln i varje datarad i tabellen.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. Återskapa programmet:

    ```bash
    mvnw clean package 
    ```

6. Distribuera den uppdaterade WAR-filen som tidigare genom att ta bort den befintliga katalogen *site/wwwroot/webapps/ROOT* och *ROOT.war* och sedan ladda upp den uppdaterade WAR-filen som ROOT.war. 

När du uppdaterar appen visas kolumnen med **tidpunkt när objektet skapades**. När du lägger till en ny uppgift fylls tidsstämpeln i automatiskt i appen. Befintliga uppgifter förblir oförändrade och fungerar i appen trots att den underliggande datamodellen har ändrats. 

![Java-app som har uppdaterats med en ny kolumn](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar 

När Java-appen körs i Azure App Service kan du skicka konsolloggarna direkt till din terminal. På så sätt kan du få samma diagnostikmeddelanden för att felsöka programfel.

Starta loggströmningen genom att använda kommandot [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) i Cloud Shell.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Hantera din Azure-webbapp

Gå till [Azure Portal](https://portal.azure.com) för att se den webbapp du skapade.

Klicka på **App Services** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-tutorial-java-mysql/access-portal.png)

Sidan **Översikt** visas som standard på webbappens sida. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra hanteringsåtgärder som att stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

Flikarna på sidan innehåller många bra funktioner som du kan lägga till i webbappen. I listan nedan kan du se några av möjligheterna:
* Mappa ett anpassat DNS-namn
* Bind ett anpassat SSL-certifikat
* Konfigurera kontinuerlig distribution
* Skala upp
* Lägg till användarautentisering

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de här resurserna för en annan självstudie (se [Nästa steg](#next)) kan du ta bort dem genom att köra följande kommando i Cloud Shell: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## Next steps

> [!div class="checklist"]
> * Create a MySQL database in Azure
> * Connect a sample Java app to the MySQL
> * Deploy the app to Azure
> * Update and redeploy the app
> * Stream diagnostic logs from Azure
> * Manage the app in the Azure portal

Advance to the next tutorial to learn how to map a custom DNS name to the app.

> [!div class="nextstepaction"] 
> [Map an existing custom DNS name to Azure Web Apps](app-service-web-tutorial-custom-domain.md)
