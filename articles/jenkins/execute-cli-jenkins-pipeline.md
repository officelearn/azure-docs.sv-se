---
title: Köra Azure CLI med Jenkins | Microsoft Docs
description: Lär dig hur du använder Azure CLI för att distribuera en Java-webbapp till Azure i Jenkins Pipeline
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
ms.openlocfilehash: 2b568bd22858a42178e2821e0e97a3b4ebdfccd5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
ms.locfileid: "28926938"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Distribuera till Azure App Service med Jenkins och Azure CLI
Om du vill distribuera en Java-webbapp till Azure kan du använda Azure CLI i [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/). I den här självstudien skapar du en CI/CD-pipeline på en virtuell Azure-dator och lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator i Jenkins
> * Konfigurera Jenkins
> * Skapa en webbapp i Azure
> * Förbereda en GitHub-databas
> * Skapa Jenkins pipeline
> * Kör pipeline och verifiera webbappen

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Skapa och konfigurera Jenkins instans
Om du inte redan har en Jenkins master börja med den [Lösningsmall](install-jenkins-solution-template.md), som innehåller de nödvändiga [Azure-autentiseringsuppgifterna](https://plugins.jenkins.io/azure-credentials) plugin-programmet som standard. 

Azure-autentiseringsuppgifter plugin-programmet kan du lagra Microsoft Azure service principal autentiseringsuppgifter i Jenkins. I version 1.2, vi har lagt till stöd så att Jenkins-Pipeline kan hämta autentiseringsuppgifter för Azure. 

Se till att du har version 1.2 eller senare:
* I instrumentpanelen Jenkins klickar du på **Plugin-hanteraren -> Hantera Jenkins ->** och Sök efter **Azure autentiseringsuppgifter**. 
* Uppdatera plugin-programmet om versionen är tidigare än 1.2.

JDK Java och Maven också krävs i Jenkins master. För att installera, logga in på Jenkins master med SSH och kör följande kommandon:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Lägg till Azure-tjänstens huvudnamn i Jenkins autentiseringsuppgifter

Azure autentiseringsuppgifter krävs för att köra Azure CLI.

* I instrumentpanelen Jenkins klickar du på **autentiseringsuppgifter -> System ->**. Klicka på **globala credentials(unrestricted)**.
* Klicka på **lägga till autentiseringsuppgifter** att lägga till en [Microsoft Azure-tjänstens huvudnamn](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) genom att fylla i prenumerations-ID, klient-ID, Klienthemligheten och OAuth 2.0-Token för slutpunkt. Ange ett ID för användning i senare steg.

![Lägga till autentiseringsuppgifter](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Skapa en Azure App Service för att distribuera Java-webbapp

Skapa en Azure App Service-plan med den **lediga** priser nivå med hjälp av den [az programtjänstplan skapa](/cli/azure/appservice/plan#az_appservice_plan_create) CLI-kommando. Programtjänstplan definierar de fysiska resurserna som används som värd för dina appar. Alla program som har tilldelats en programtjänstplan dela dessa resurser, så att du kan spara kostnader när värd för flera appar. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
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

 Använd den [az webapp skapa](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) CLI-kommando för att skapa en web app definition i den `myAppServicePlan` App Service-plan. Web app definitionen innehåller en URL för att få åtkomst till ditt program med och konfigurerar du flera alternativ för att distribuera din kod till Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
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

Ställa in Java runtime-konfiguration som din app behöver med den [uppdatering az apptjänst web-config](/cli/azure/appservice/web/config#az_appservice_web_config_update) kommando.

Följande kommando konfigurerar webbprogram för att köra på en senaste Java 8 JDK och [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Förbereda en GitHub-databas
Öppna den [enkel Java-Webbapp för Azure](https://github.com/azure-devops/javawebappsample) lagringsplatsen. Om du vill duplicera lagringsplatsen till ditt GitHub-konto, klickar du på den **Återställningsförgreningar** -knappen i det övre högra hörnet.

* Öppna i GitHub-webbgränssnittet, **Jenkinsfile** fil. Klicka på pennikonen om du vill redigera den här filen om du vill uppdatera den resursgrupp och namnet på ditt webbprogram på rad 20 och 21 respektive.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Ändra raden 23 att uppdatera uppgifts-ID i Jenkins-instans

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Skapa Jenkins pipeline
Öppna Jenkins i en webbläsare, klicka på **nytt objekt**. 

* Ange ett namn för jobbet och välj **Pipeline**. Klicka på **OK**.
* Klicka på den **Pipeline** fliken nästa. 
* För **Definition**väljer **Pipeline-skriptet från SCM**.
* För **SCM**väljer **Git**.
* Ange GitHub-URL för din andelen vridvuxna lagringsplatsen: https:\<din andelen vridvuxna lagringsplatsen\>.git
* Klicka på **spara**

## <a name="test-your-pipeline"></a>Testa din pipeline
* Gå till pipelinen som du skapade **skapa nu**
* En version ska lyckas i några sekunder och du kan gå att bygga och klicka på **konsolens utdata** att visa detaljer

## <a name="verify-your-web-app"></a>Kontrollera ditt webbprogram
Om du vill verifiera WAR distribuerats filen till ditt webbprogram. Öppna en webbläsare:

* Go to http://&lt;app_name>.azurewebsites.net/api/calculator/ping  
Du ser:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Gå till http://&lt;appnamn >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (ersätta &lt;x > och &lt;y > med några siffror) att hämta summan av x och y

![Kalkylatorn: Lägg till](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Distribuera till Azure-Webbapp på Linux
Nu när du vet hur du använder Azure CLI i Jenkins-pipeline kan du ändra skriptet för att distribuera till en Azure-Webbapp på Linux.

Web App på Linux stöder olika sätt att göra distribution, vilket är att använda Docker. Om du vill distribuera, som du behöver ange en Dockerfile som ditt webbprogram med tjänsten runtime-paket till en Docker-bild. Plugin-programmet skapa avbildningen, push-installera den en Docker-registret och därefter distribuera avbildningen till ditt webbprogram.

* Följ stegen [här](../app-service/containers/quickstart-nodejs.md) att skapa en Azure-Webbapp som körs på Linux.
* Installera Docker på Jenkins-instans genom att följa anvisningarna i det här [artikel](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Skapa en behållare registret i Azure-portalen med hjälp av stegen [här](/azure/container-registry/container-registry-get-started-azure-cli).
* I samma [enkel Java-Webbapp för Azure](https://github.com/azure-devops/javawebappsample) lagringsplatsen forked du kan redigera den **Jenkinsfile2** fil:
    * Rad 18 21, uppdatera till namnen på din resursgrupp, webbprogram och ACR respektive. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Raden 24, uppdatera \<azsrvprincipal\> till din uppgifts-ID
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Skapa en ny Jenkins pipeline som du när du distribuerar till Azure-webbapp i Windows, men den här gången använder **Jenkinsfile2** i stället.
* Kör ditt nya jobb.
* Om du vill verifiera i Azure CLI kör du:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Du får följande resultat:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Gå till http://&lt;programnamn >.azurewebsites.net/api/calculator/ping. Du ser meddelandet: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Gå till http://&lt;appnamn >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (ersätta &lt;x > och &lt;y > med några siffror) att hämta summan av x och y
    
## <a name="next-steps"></a>Nästa steg
Du har konfigurerat en Jenkins pipeline som checkar ut källkoden i GitHub-repo i den här självstudiekursen. Kör Maven för att skapa en war-fil och sedan använder Azure CLI för att distribuera till Azure App Service. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator i Jenkins
> * Konfigurera Jenkins
> * Skapa en webbapp i Azure
> * Förbereda en GitHub-databas
> * Skapa Jenkins pipeline
> * Kör pipeline och verifiera webbappen
