---
title: Köra Azure CLI med Jenkins
description: Lär dig hur du använder Azure CLI för att distribuera en Java-webbapp till Azure i Jenkins Pipeline
ms.service: jenkins
keywords: jenkins, azure, devops, app service, cli
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 6/7/2017
ms.openlocfilehash: 05e21083b060e00602f58e1a17f52f40ced7d34e
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075217"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Distribuera till Azure App Service med Jenkins och Azure CLI
För distribution av en Java-webbapp till Azure kan du använda Azure CLI i [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/). I den här självstudien skapar du en CI/CD-pipeline på en virtuell Azure-dator och lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator i Jenkins
> * Konfigurera Jenkins
> * Skapa en webbapp i Azure
> * Förbereda en GitHub-lagringsplats
> * Skapa en Jenkins-pipeline
> * Köra pipelinen och verifiera webbappen

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Skapa och konfigurera en Jenkins-instans
Om du inte redan har en Jenkins-huvudserver börjar du med [lösningsmallen](install-jenkins-solution-template.md), som innehåller det nödvändiga plugin-programmet [Azure Credentials](https://plugins.jenkins.io/azure-credentials) som standard. 

Med plugin-programmet Azure Credentials kan du lagra autentiseringsuppgifter för Microsoft Azure-tjänsthuvudnamn i Jenkins. I version 1.2 har vi lagt till stöd för Jenkins Pipeline att hämta Azure-autentiseringsuppgifterna. 

Kontrollera att du har version 1.2 eller senare:
* I Jenkins-instrumentpanelen klickar du på **Hantera Jenkins -> Plugin-hanterare ->** och söker reda på **Azure Credential**. 
* Uppdatera plugin-programmet om versionen är tidigare än 1.2.

Java JDK och Maven krävs också på Jenkins-huvudservern. Om du vill installera loggar du in på Jenkins-huvudservern med SSH och kör följande kommandon:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Lägg till ett Azure-tjänsthuvudnamn till autentiseringsuppgifterna för Jenkins

En Azure-autentiseringsuppgift krävs för att Azure CLI ska kunna köras.

* I instrumentpanelen för Jenkins klickar du på **Autentiseringsuppgifter -> System**. Klicka på **Globala autentiseringsuppgifter (obegränsade)**.
* Klicka på **Lägg till autentiseringsuppgifter** för att lägga till ett [Microsoft Azure-tjänsthuvudnamn](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) genom att fylla i prenumerations-ID, klient-ID, klienthemlighet och OAuth 2.0 Token Endpoint. Ange ett ID för användning i senare steg.

![Ange autentiseringsuppgifter](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Skapa en Azure App Service för distribution av Java-webbappen

Skapa en Azure App Service-plan med den **kostnadsfria** prisnivån med hjälp av CLI-kommandot [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create). App Service-planen definierar vilka fysiska resurser som används som värd för dina appar. Alla program som tilldelas till en App Service-plan delar dessa resurser. Det innebär att du kan minska kostnaderna när du har flera appar i en plan. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
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

 Använd CLI-kommandot [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) för att skapa en webbappsdefinition i App Service-planen `myAppServicePlan`. Webbappsdefinitionen innehåller en URL för att få åtkomst till appen och konfigurerar flera alternativ för att distribuera koden till Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
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

Konfigurera den Java Runtime-konfiguration som appen behöver med kommandot [az appservice web config update](/cli/azure/webapp/config#az-appservice-web-config-update).

Följande kommando konfigurerar webbappen att köra på en sen Java 8 JDK och [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Förbereda en GitHub-lagringsplats
Öppna lagringsplatsen [Enkel Java-Webbapp för Azure](https://github.com/azure-devops/javawebappsample). Förgrena lagringsplatsen till ditt eget GitHub-konto genom att klicka på knappen **Fork** (Förgrening) i det övre högra hörnet.

* Öppna i GitHub-webbgränssnittet och öppna filen **Jenkinsfile**. Klicka på pennikonen för att redigera filen. Uppdatera resursgruppen och namnet på din webbapp på rad 20 och 21.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Ändra rad 23 för att uppdatera autentiseringsuppgifts-ID i Jenkins-instansen

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Skapa en Jenkins-pipeline
Öppna Jenkins i en webbläsare och klicka på **Nytt objekt**. 

* Ange ett namn för jobbet och välj **Pipeline**. Klicka på **OK**.
* Klicka på fliken **Pipeline**. 
* För **Definition** väljer du **Pipeline-skript från SCM**.
* För **SCM** väljer du **Git**.
* Ange GitHub-URL:en för din förgrenade lagringsplats: https:\<din förgrenade lagringsplats\>.git
* Klicka på **Spara**

## <a name="test-your-pipeline"></a>Testa din pipeline
* Gå till den pipeline som du skapade och klicka på **Build Now** (Kompilera nu)
* En kompilering ska vara klar på några sekunder, och du kan gå till kompileringen och klicka på **Konsolutdata** för att se mer information

## <a name="verify-your-web-app"></a>Verifiera din webbapp
För att kontrollera att WAR-filen har distribuerats till din webbapp. Öppna en webbläsare:

* Gå till http://&lt;appnamn>.azurewebsites.net/api/calculator/ping  
Du ser:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Gå till http://&lt;appnamn>.azurewebsites.net/api/calculator/add?x=&lt;x> & y =&lt;y> (Ersätt &lt;x> och &lt;y> med valfritt tal) för att hämta summan av x och y

![Kalkylator: lägg till](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Distribuera till Azure Web App i Linux
Nu när du vet hur du använder Azure CLI i din Jenkins-pipeline kan du ändra skriptet för att distribuera till en Azure Web App i Linux.

Web App i Linux har stöd för ett annat distributionssätt: via Docker. För att kunna distribuera måste du tillhandahålla en Dockerfile som paketerar webbappen med en tjänst-CLR till en Docker-avbildning. Plugin-programmet skapar sedan avbildningen, skickar den till ett Docker-register och distribuerar avbildningen till webbappen.

* Följ stegen [här](../app-service/containers/quickstart-nodejs.md) för att skapa en Azure Web App som körs på Linux.
* Installera Docker på Jenkins-instansen genom att följa anvisningarna i den här [artikeln](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Skapa ett containerregister med Azure-portalen med hjälp av anvisningarna [här](/azure/container-registry/container-registry-get-started-azure-cli).
* På den [enkla Java-webbapp för Azure](https://github.com/azure-devops/javawebappsample)-lagringsplats som du förgrenade redigerar du filen **Jenkinsfile2**:
    * Rad 18–21: uppdatera till namnen på din resursgrupp, webbapp och ACR. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Rad 24: uppdatera \<azsrvprincipal\> till ditt autentiseringsuppgifts-ID
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Skapa en ny Jenkins-pipeline som du gjorde när du distribuerade till Azure-webbappen i Windows, men den här gången använder du **Jenkinsfile2** i stället.
* Kör det nya jobbet.
* Kontrollera i Azure CLI med:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Du får följande utdata:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Gå till http://&lt;appnamn>.azurewebsites.net/api/calculator/ping. Det här meddelandet visas: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Gå till http://&lt;appnamn>.azurewebsites.net/api/calculator/add?x=&lt;x> & y =&lt;y> (Ersätt &lt;x> och &lt;y> med valfritt tal) för att hämta summan av x och y
    
## <a name="next-steps"></a>Nästa steg
I den här självstudien konfigurerade du en Jenkins-pipeline som checkar ut källkoden i GitHub-lagringsplatsen. Maven körs för att skapa en WAR-fil, och sedan används Azure CLI för distribution till Azure App Service. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator i Jenkins
> * Konfigurera Jenkins
> * Skapa en webbapp i Azure
> * Förbereda en GitHub-lagringsplats
> * Skapa en Jenkins-pipeline
> * Köra pipelinen och verifiera webbappen
