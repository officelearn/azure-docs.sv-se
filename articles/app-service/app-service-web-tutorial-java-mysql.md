---
title: Skapa en Java och MySQL-webbapp i Azure
description: "Lär dig hur du hämtar en Java-app som ansluter till tjänsten Azure MySQL database arbetar i Azure apptjänst."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: ad53575b655ebec5a134c8d76b963708caf14334
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Skapa en Java och MySQL-webbapp i Azure

> [!NOTE]
> Den här artikeln distribuerar en app till App Service i Windows. Du distribuerar till App Service på _Linux_, se [distribuera en behållare källan Start-app till Azure](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin).
>

Den här kursen visar hur du skapar en Java-webbapp i Azure och ansluta till en MySQL-databas. När du är klar har du en [Vårversionen Start](https://projects.spring.io/spring-boot/) program som lagrar data i [Azure-databas för MySQL](https://docs.microsoft.com/azure/mysql/overview) körs på [Azure App Service Web Apps](app-service-web-overview.md).

![Java-app som körs i Azure apptjänst](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en MySQL-databas i Azure
> * Ansluta en exempelapp till databasen
> * Distribuera appen till Azure
> * Uppdatera och distribuera om appen
> * Dataströmmen diagnostiska loggar från Azure
> * Övervaka app på Azure-portalen


## <a name="prerequisites"></a>Krav

1. [Hämta och installera Git](https://git-scm.com/)
1. [Hämta och installera JDK för Java-7 eller senare](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [Hämta, installera och starta MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Förbereda lokala MySQL 

I det här steget skapar du en databas i en lokal MySQL-server för användning i testar appen lokalt på din dator.

### <a name="connect-to-mysql-server"></a>Ansluta till MySQL-servern

Anslut till din lokala MySQL-server i ett terminalfönster. Du kan använda den här terminalfönster för att köra alla kommandon i den här självstudiekursen.

```bash
mysql -u root -p
```

Om du uppmanas att ange ett lösenord anger du lösenordet för den `root` konto. Om du inte kommer ihåg rotlösenordet, se [MySQL: hur du återställer Rotlösenordet](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Om kommandot körs utan problem, körs MySQL-servern redan. Om inte, kontrollera att den lokala MySQL-servern är igång genom att följa den [MySQL efter installationssteg](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Skapa en databas 

I den `mysql` uppmanar, skapa en databas och en tabell för att göra-objekt.

```sql
CREATE DATABASE tododb;
```

Avsluta anslutningen till servern genom att skriva `quit`.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Skapa och kör exempelappen 

I det här steget klona exempelappen källan Start, konfigurera den lokala MySQL-databas och kör det på din dator. 

### <a name="clone-the-sample"></a>Klona exemplet

Navigera till en arbetskatalog och klona lagringsplatsen exempel i fönstret terminal. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Konfigurera appen för att använda MySQL-databas

Uppdatering av `spring.datasource.password` och värde i *spring-boot-mysql-todo/src/main/resources/application.properties* med samma rotlösenord som används för att öppna MySQL-fråga:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Skapa och köra exemplet

Skapa och köra exemplet med Maven-omslutning som ingår i lagringsplatsen:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Öppna din webbläsare med `http://localhost:8080` ska se i exemplet i åtgärden. När du lägger till aktiviteter i listan använda följande SQL-kommandon MySQL-fråga för att visa data som lagras i MySQL.

```SQL
use testdb;
select * from todo_item;
```

Stoppa programmet genom att träffa `Ctrl` + `C` i terminalen. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Skapa en Azure MySQL-databas

I det här steget skapar du en [Azure-databas för MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) instans med det [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Du kan konfigurera exempelprogrammet att använda den här databasen senare under kursen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [resursgruppen](../azure-resource-manager/resource-group-overview.md) med den [az gruppen skapa](/cli/azure/group#create) kommando. En Azure-resursgrupp är en logisk behållare där relaterade resurser som webbappar, databaser och storage-konton distribueras och hanteras. 

I följande exempel skapas en resursgrupp i Norra Europa region:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

Se möjliga värden du kan använda för `--location`, använda den [az apptjänst lista-platser](/cli/azure/appservice#list-locations) kommando.

### <a name="create-a-mysql-server"></a>Skapa en MySQL-server

Molnet Shell, skapar du en server i Azure-databas för MySQL (förhandsversion) med den [az mysql-servern skapa](/cli/azure/mysql/server#create) kommando.    
Ersätta en egen unik MySQL-servernamn där du ser den `<mysql_server_name>` platshållare. Det här namnet är en del av MySQL-serverns värdnamn, `<mysql_server_name>.mysql.database.azure.com`, så den behöver vara globalt unika. I stället använda `<admin_user>` och `<admin_password>` med egna värden.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user <admin_user> --admin-password <admin_password>
```

När MySQL-servern har skapats visas Azure CLI information liknar följande exempel:

```json
{
  "administratorLogin": "admin_user",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mysql_server_name.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysql_server_name",
  "location": "northeurope",
  "name": "mysql_server_name",
  "resourceGroup": "mysqlJavaResourceGroup",
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Konfigurera server-brandväggen

Molnet Shell, skapa en brandväggsregel för MySQL-servern att tillåta klientanslutningar med hjälp av den [az mysql-brandväggsregel skapa](/cli/azure/mysql/server/firewall-rule#create) kommando. 

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure-databas för MySQL (förhandsversion) kan inte för närvarande automatiskt anslutningar från Azure-tjänster. IP-adresser i Azure tilldelas dynamiskt, är det bättre att aktivera alla IP-adresser för tillfället. Eftersom tjänsten fortsätter förhandsgranskningen, aktiveras bättre metoder för att skydda databasen.

## <a name="configure-the-azure-mysql-database"></a>Konfigurera Azure MySQL-databas

Anslut till MySQL-server i Azure i det lokala terminalfönstret. Använd värdet du angav tidigare för `<admin_user>` och `<mysql_server_name>`.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Skapa en databas 

I den `mysql` uppmanar, skapa en databas och en tabell för att göra-objekt.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Skapa en användare med behörighet

Skapa en databasanvändare och ge den alla behörigheter i den `tododb` databas. Ersätt platshållarna `<Javaapp_user>` och `<Javaapp_password>` med dina egna unika namn.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Avsluta anslutningen till servern genom att skriva `quit`.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Distribuera exemplet till Azure App Service

Skapa en Azure App Service-plan med den **lediga** priser nivå med hjälp av den [az programtjänstplan skapa](/cli/azure/appservice/plan#create) CLI-kommando. Programtjänstplan definierar de fysiska resurserna som används som värd för dina appar. Alla program som har tilldelats en programtjänstplan dela dessa resurser, så att du kan spara kostnader när värd för flera appar. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

När planen är klar, visas Azure CLI liknande utdata i följande exempel:

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

 I gränssnittet molnet använder den [az webapp skapa](/cli/azure/appservice/web#create) CLI-kommando för att skapa en web app definition i den `myAppServicePlan` App Service-plan. Web app definitionen innehåller en URL för att få åtkomst till ditt program med och konfigurerar du flera alternativ för att distribuera din kod till Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Ersätt den `<app_name>` med dina egna unika namn. Detta unika namn är en del av standarddomännamnet för webbprogram, så att namnet måste vara unikt över alla program i Azure. Du kan mappa en anpassad domän namnpost till webbprogrammet innan du ansluter till dina användare.

När web app definitionen är klar, visas Azure CLI information liknar följande exempel: 

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

I molnet Shell, ställa in Java runtime-konfiguration som din app behöver med den [uppdatering az apptjänst web-config](/cli/azure/appservice/web/config#update) kommando.

Följande kommando konfigurerar webbprogram för att köra på en senaste Java 8 JDK och [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Konfigurera appen för att använda Azure SQL-databas

Ange inställningar för webbprogram för att använda Azure MySQL-databasen som du skapade i Azure innan du kör exempelappen. Dessa egenskaper som exponeras av webbprogram som miljövariabler och åsidosätter de värden som anges i application.properties inuti paketerade webbprogrammet. 

Ange programinställningar i molnet Shell, [az webapp config appsettings](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) i CLI:

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>Hämta autentiseringsuppgifter för FTP-distribution 
Du kan distribuera programmet till Azure apptjänst på olika sätt, inklusive FTP, lokal Git, GitHub, Visual Studio Team Services och BitBucket. I det här exemplet FTP för att distribuera den. WAR-fil som skapats tidigare på din lokala dator till Azure App Service.

Använd för att fastställa vilka autentiseringsuppgifter som skickar vidare i ett ftp-kommando till Webbappen [az apptjänst web distributionsåtgärder lista-publicering-profiler](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) i molnet Shell: 

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

### <a name="upload-the-app-using-ftp"></a>Ladda upp appen med hjälp av FTP

Använda din favorit FTP-verktyget för att distribuera den. WAR-filen till den */site/wwwroot/webapps* mapp på adressen till servern från den `URL` i det föregående kommandot. Ta bort den befintliga programkatalogen (rot) och Ersätt den befintliga ROOT.war med den. WAR-fil i tidigare i kursen.

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

Bläddra till `http://<app_name>.azurewebsites.net/` och lägga till några åtgärder i listan. 

![Java-app som körs i Azure apptjänst](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Grattis!** Du kör en datadrivna Java-app i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Uppdatera programmet så att ytterligare en kolumn i todo-listan för vilken dag objektet skapades. Källan Start hanterar uppdaterar databasschemat för dig som modellen dataändringar utan att ändra din befintliga databasposter.

1. I det lokala systemet öppna *src/main/java/com/example/fabrikam/TodoItem.java* och Lägg till följande importer i klassen:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Lägg till en `String` egenskapen `timeCreated` till *src/main/java/com/example/fabrikam/TodoItem.java*, initierar med en tidsstämpel när objektet skapas. Lägg till Get/Set-metoder för den nya `timeCreated` egenskapen när du redigerar filen.

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

3. Uppdatera *src/main/java/com/example/fabrikam/TodoDemoController.java* med en rad i den `updateTodo` metod för att ange tidsstämpeln:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Lägga till stöd för det nya fältet i mallen Thymeleaf. Uppdatera *src/main/resources/templates/index.html* med ett nytt huvud för tidsstämpel och ett nytt fält att visa värdet för tidsstämpeln i varje datarad i tabellen.

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

6. FTP-den uppdaterade. WAR som tidigare kan ta bort den befintliga *plats/wwwroot/webbappar/ROOT* directory och *ROOT.war*, och sedan ladda upp den uppdaterade. WAR-filen som ROOT.war. 

När du uppdaterar appen med en **Skapad** kolumnen visas nu. När du lägger till en ny uppgift appen kommer att fylla i tidsstämpeln automatiskt. Din befintliga aktiviteter förblir oförändrad och arbeta med appen även om den underliggande datamodellen har ändrats. 

![Java-app som har uppdaterats med en ny kolumn](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Dataströmmen diagnostikloggar 

Du kan hämta loggarna för konsolen skickas direkt till terminalen när Java-programmet körs i Azure App Service. På så sätt kan du få samma diagnostiska meddelanden för att felsöka programfel.

Starta loggen strömning med den [az webapp loggen pilslut](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) i molnet Shell.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Hantera Azure-webbapp

Gå till Azure portal för att se att webbappen som du skapade.

Logga in på [https://portal.azure.com](https://portal.azure.com).

Klicka på **App Services** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-tutorial-java-mysql/access-portal.png)

Sidan **Översikt** visas som standard på webbappens blad. På den här sidan får du en översikt över hur det går för appen. Här kan utföra du också hanteringsuppgifter som att stoppa, starta, starta om och ta bort. På flikarna till vänster på bladet kan du se olika konfigurationssidor som du kan öppna.

![App Service-blad på Azure Portal](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

Flikarna på bladet innehåller många bra funktioner som du kan lägga till i webbappen. I listan nedan kan du se några av möjligheterna:
* Mappa ett anpassat DNS-namn
* Bind ett anpassat SSL-certifikat
* Konfigurera kontinuerlig distribution
* Skala upp
* Lägg till användarautentisering

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver dessa resurser för en annan självstudiekursen (se [nästa steg](#next)), kan du ta bort dem genom att köra följande kommando i molnet Shell: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Nästa steg

> [!div class="checklist"]
> * Skapa en MySQL-databas i Azure
> * Ansluta en Java-exempelapp till MySQL
> * Distribuera appen till Azure
> * Uppdatera och distribuera om appen
> * Dataströmmen diagnostiska loggar från Azure
> * Hantera appen i Azure-portalen

Gå vidare till nästa kurs information om hur du mappar en anpassad DNS-namn till appen.

> [!div class="nextstepaction"] 
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md)
