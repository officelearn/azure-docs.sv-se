---
title: Köra Azure CLI med Jenkins | Microsoft Docs
description: Lär dig hur du använder Azure CLI för att distribuera en Java-webbapp till Azure i Jenkins-Pipeline
services: app-service\web
documentationcenter: ''
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 1796e9f76e39334c8bbdd03463a0f91e9b47cb17
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421312"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Distribuera till Azure App Service med Jenkins och Azure CLI
Om du vill distribuera en Java-webbapp till Azure, kan du använda Azure CLI i [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/). I den här självstudien skapar du en CI/CD-pipeline på en virtuell Azure-dator och lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator i Jenkins
> * Konfigurera Jenkins
> * Skapa en webbapp i Azure
> * Förbereda en GitHub-lagringsplats
> * Skapa Jenkins-pipeline
> * Kör pipelinen och verifiera webbappen

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Skapa och konfigurera Jenkins-instans
Om du inte redan har en Jenkins-huvudserver, börjar du med den [lösningsmallen](install-jenkins-solution-template.md), som innehåller de nödvändiga [Azure Credentials](https://plugins.jenkins.io/azure-credentials) plugin-programmet som standard. 

Plugin-programmet Azure Credential kan du lagra Microsoft Azure-tjänsthuvudnamn i Jenkins. I version 1.2, vi har lagt till stöd så att Jenkins-pipelinen kan få Azure-autentiseringsuppgifter. 

Kontrollera att du har version 1.2 eller senare:
* Jenkins-instrumentpanelen klickar du på **hantera Jenkins -> plugin-program Manager ->** och Sök efter **Azure Credential**. 
* Uppdatera plugin-programmet om versionen är tidigare än 1.2.

Java JDK och Maven också krävs i Jenkins-huvudserver. Om du vill installera, logga in på Jenkins-huvudserver med hjälp av SSH och kör följande kommandon:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Lägg till Azure-tjänstens huvudnamn till Jenkins-autentiseringsuppgifter

En Azure-inloggning krävs för att köra Azure CLI.

* Jenkins-instrumentpanelen klickar du på **autentiseringsuppgifter -> System ->**. Klicka på **globala credentials(unrestricted)**.
* Klicka på **Lägg till autentiseringsuppgifter** att lägga till en [Microsoft Azure-tjänstens huvudnamn](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) genom att fylla i prenumerations-ID, klient-ID, Klienthemligheten och OAuth 2.0-Token-slutpunkten. Ange ett ID för användning i senare steg.

![Lägg till autentiseringsuppgifter](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Skapa en Azure App Service för att distribuera Java-webbapp

Skapa en Azure App Service-plan med den **kostnadsfri** priser med hjälp av den [az appservice plan skapa](/cli/azure/appservice/plan#az-appservice-plan-create) CLI-kommando. App Service-planen definierar vilka fysiska resurser som används som värd för dina appar. Alla program som tilldelas till en App Service-plan delar dessa resurser. Det innebär att du kan minska kostnaderna när du har flera appar i en plan. 

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

 Använd den [az webapp skapa](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) CLI-kommando för att skapa en webbappsdefinition i den `myAppServicePlan` App Service-plan. Webbappsdefinitionen innehåller en URL för att få åtkomst till appen och konfigurerar flera alternativ för att distribuera koden till Azure. 

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

Konfigurera Java runtime-konfiguration som appen behöver med den [az appservice web config update](/cli/azure/appservice/web/config#az-appservice-web-config-update) kommando.

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
Öppna den [enkel Java-Webbapp för Azure](https://github.com/azure-devops/javawebappsample) lagringsplatsen. Om du vill Förgrena lagringsplatsen till ditt eget GitHub-konto, klickar du på den **förgrening** knappen i det övre högra hörnet.

* Öppna i GitHub-Webbgränssnittet, **Jenkinsfile** fil. Klicka på pennikonen för att redigera den här filen för att uppdatera resursgruppen och namnet på din webbapp på rad 20 och 21 respektive.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Ändra rad 23 att uppdatera uppgifts-ID i din Jenkins-instans

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Skapa Jenkins-pipeline
Öppna Jenkins i en webbläsare, klicka på **nytt objekt**. 

* Ange ett namn för jobbet och välj **Pipeline**. Klicka på **OK**.
* Klicka på den **Pipeline** fliken bredvid. 
* För **Definition**väljer **Pipeline-skriptet från SCM**.
* För **SCM**väljer **Git**.
* Ange GitHub-URL: en för din förgrenade lagringsplats: https:\<din förgrenade lagringsplats\>.git
* Klicka på **Spara**

## <a name="test-your-pipeline"></a>Testa din pipeline
* Gå till den pipeline som du skapade **skapa nu**
* En version ska lyckas på några sekunder, och du kan gå till versionen och klicka på **Konsolutdata** kan se vilka

## <a name="verify-your-web-app"></a>Kontrollera din webbapp
För att verifiera WAR har fil distribuerats till din webbapp. Öppna en webbläsare:

* Gå till http://&lt;app_name >.azurewebsites.net/api/calculator/ping  
Du kan se:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Gå till http://&lt;app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (Ersätt &lt;x > och &lt;y > med alla nummer) att hämta summan av x och y

![Kalkylatorn: Lägg till](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Distribuera till Azure Web Apps i Linux
Nu när du vet hur du använder Azure CLI i Jenkins-pipeline, kan du ändra skriptet för att distribuera till ett Azure Web Apps på Linux.

Web Apps on Linux stöder ett annat sätt att göra distribution, vilket är att använda Docker. Om du vill distribuera, måste du ange en Dockerfile som din webbapp med service runtime-paket i en dockeravbildning. Plugin-programmet kan sedan skapa avbildningen, push-överföra den till ett Docker-register och distribuera avbildningen till webbappen.

* Följ stegen [här](../app-service/containers/quickstart-nodejs.md) att skapa en Azure-Webbapp som körs på Linux.
* Installera Docker på Jenkins-instansen genom att följa anvisningarna i det här [artikeln](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Skapa ett Behållarregister i Azure portal genom att följa [här](/azure/container-registry/container-registry-get-started-azure-cli).
* I samma [enkel Java-Webbapp för Azure](https://github.com/azure-devops/javawebappsample) du förgrenade lagringsplatsen, redigera den **Jenkinsfile2** fil:
    * Rad 18-21, uppdatera till namnen på din resursgrupp, webbappen och ACR respektive. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Rad 24, uppdatering \<azsrvprincipal\> till autentiseringsuppgifts-ID
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Skapa en ny Jenkins-pipeline som du när du distribuerar till Azure-webbapp i Windows, men den här gången använder **Jenkinsfile2** i stället.
* Kör ditt nya jobb.
* Kontrollera i Azure CLI med:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Du får följande resultat:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Gå till http://&lt;app_name >.azurewebsites.net/api/calculator/ping. Du ser meddelandet: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Gå till http://&lt;app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (Ersätt &lt;x > och &lt;y > med alla nummer) att hämta summan av x och y
    
## <a name="next-steps"></a>Nästa steg
I den här självstudien har konfigurerat du en Jenkins-pipeline som checkar ut källkoden i GitHub-lagringsplatsen. Kör Maven för att skapa en war-fil och sedan använder Azure CLI för att distribuera till Azure App Service. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator i Jenkins
> * Konfigurera Jenkins
> * Skapa en webbapp i Azure
> * Förbereda en GitHub-lagringsplats
> * Skapa Jenkins-pipeline
> * Kör pipelinen och verifiera webbappen
