---
title: Distribuera till Azure App Service med hjälp av Jenkins-plugin-programmet
description: Lär dig hur du använder Azure App Service Jenkins plugin-programmet för att distribuera en Java-webbapp till Azure i Jenkins
ms.topic: article
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/31/2018
ms.openlocfilehash: f54e4e8f64fe444f264b547d5af475c533c5723f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441688"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Distribuera till Azure App Service med hjälp av Jenkins-plugin-programmet 

Om du vill distribuera en Java-webbapp till Azure måste du använda Azure CLI i [Jenkins Pipeline](/azure/jenkins/execute-cli-jenkins-pipeline) eller så kan du använda den [plugin-programmet Jenkins för Azure App Service](https://plugins.jenkins.io/azure-app-service). Jenkins-plugin-programmet version 1.0 har stöd för kontinuerlig distribution med hjälp av funktionen Web Apps i Azure App Service via:
* Git- eller FTP.
* Docker för Web Apps på Linux.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera Jenkins för att distribuera Webbappar via Git eller FTP.
> * Konfigurera Jenkins för att distribuera Web App for Containers.

## <a name="create-and-configure-a-jenkins-instance"></a>Skapa och konfigurera en Jenkins-instans

Om du inte redan har en Jenkins-huvudserver, börjar du med den [lösningsmallen](install-jenkins-solution-template.md), som innehåller Java Development Kit (JDK) version 8 och följande obligatoriska Jenkins plugin-program:

* [Plugin-program för Jenkins Git-klient](https://plugins.jenkins.io/git-client) version 2.4.6 
* [Docker Commons plugin-programmet](https://plugins.jenkins.io/docker-commons) version 1.4.0
* [Autentiseringsuppgifter för Azure](https://plugins.jenkins.io/azure-credentials) version 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-service) version 0.1

Du kan använda plugin-programmet Jenkins för att distribuera en webbapp på valfritt språk som stöds av Web Apps, till exempel C#, PHP, Java och Node.js. I den här självstudien använder vi en [enkel Java-webbapp för Azure](https://github.com/azure-devops/javawebappsample). Om du vill Förgrena lagringsplatsen till ditt eget GitHub-konto, Välj den **förgrening** knappen i det övre högra hörnet av GitHub-gränssnittet.  
> [!NOTE]
> Java JDK och Maven krävs för att skapa Java-projekt. Installera komponenterna på Jenkins-huvudserver eller på VM-agenten om du använder agenten för kontinuerlig integrering. 

Installera komponenterna, logga in på Jenkins-instans med SSH och kör följande kommandon:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Distribuera till Web App for Containers genom att installera Docker på Jenkins-huvudserver eller på VM-agenten som används för kompilering. Anvisningar finns i [installera Docker på Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a> Lägg till ett Azure-tjänstobjekt till Jenkins-autentiseringsuppgifter

Du behöver ett Azure-tjänsthuvudnamn för att distribuera till Azure. 


1. Du kan skapa en Azure-tjänstens huvudnamn med den [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) eller [Azure-portalen](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. På Jenkins-instrumentpanelen väljer **autentiseringsuppgifter** > **System**. Välj **globala credentials(unrestricted)**.
3. Om du vill lägga till ett huvudnamn för tjänsten Microsoft Azure, Välj **Lägg till autentiseringsuppgifter**. Ange värden för den **prenumerations-ID**, **klient-ID**, **Klienthemlighet**, och **OAuth 2.0-Tokenslutpunkten** fält. Ange den **ID** fältet **mySp**. Vi kan använda detta ID i efterföljande steg i den här artikeln.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Konfigurera Jenkins för att distribuera Web Apps genom att överföra filer

Du kan ladda upp din byggartefakter (till exempel en WAR-fil i Java) för att distribuera projektet till Web Apps, med hjälp av Git eller FTP.

Innan du konfigurerar jobbet i Jenkins behöver du en Azure App Service-plan och en webbapp för att köra Java-app.


1. Skapa en Azure App Service-plan med den **kostnadsfri** prisnivån med hjälp av den `az appservice plan create` [Azure CLI-kommando](/cli/azure/appservice/plan#az-appservice-plan-create). App Service-planen definierar vilka fysiska resurser som används som värd för dina appar. Alla program som har tilldelats en App Service-plan delar dessa resurser. Delade resurser hjälper dig att minska kostnaderna när flera appar.
2. Skapa en webbapp. Du kan använda den [Azure-portalen](/azure/app-service-web/web-sites-configure) eller följande `az` Azure CLI-kommando:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Ställa in Java runtime-konfiguration som appen behöver. Följande Azure CLI-kommando konfigurerar webbappen att köra på en nyligen JDK 8 och [Apache Tomcat](http://tomcat.apache.org/) version 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Konfigurera Jenkins-jobb

1. Skapa en ny **freestyle** projekt på Jenkins-instrumentpanelen.
2. Konfigurera den **Source Code Management** fält som du vill använda din lokala förgrening av den [enkel Java-webbapp för Azure](https://github.com/azure-devops/javawebappsample). Ange den **URL för databasen** värde. Till exempel: http://github.com/&lt; your_ID > / javawebappsample.
3. Lägg till ett steg för att skapa projektet med hjälp av Maven genom att lägga till den **köra shell** kommando. I det här exemplet behöver vi en extra kommando för att byta namn på den \*WAR-filen i målmappen för **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Lägg till en åtgärd för post-build genom att välja **publicera ett Azure Web Apps**.
5. Ange **mySp** som huvudsaklig Azure-tjänst. Den här huvudnamn lagrades som den [Azure Credentials](#service-principal) i föregående steg.
6. I den **Appkonfiguration** väljer resurs grupp och web app i din prenumeration. Jenkins-plugin-programmet identifierar automatiskt om webbappen är baserad på Windows eller Linux. För en Windows-webbapp i **publicera filer** alternativ visas.
7. Fyll i de filer som du vill distribuera. Till exempel ange WAR-paketet om du använder Java. Använd det valfria **källkatalog** och **målkatalogen** parametrar för att ange käll- och Målmapparna för filöverföring. Java-webbapp i Azure körs i en Tomcat-servern. Så för Java överföra du dina WAR-paket till webbappens mapp. För det här exemplet ställer du in den **källkatalog** värde att **target** och **målkatalogen** värde att **webapps**.
8. Om du vill distribuera till en plats än produktion kan du också ange den **fack** namn.
9. Spara projektet och skapa den. Din webbapp har distribuerats till Azure när versionen har slutförts.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Distribuera Web Apps genom att ladda upp filer med hjälp av Jenkins-Pipeline

Azure App Service Jenkins plugin-programmet är redo för pipeline. Du kan referera till följande exempel på GitHub-lagringsplats.

1. Öppna i GitHub-gränssnittet i **Jenkinsfile_ftp_plugin** fil. Välj pennikonen för att redigera filen. Uppdatera den **resourceGroup** och **webAppName** definitioner för din webbapp på linjer 11 och 12, respektive:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Ange den **withCredentials** definitionen på rad 14 credential-ID: t i din Jenkins-instans:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Skapa en Jenkins-pipeline

1. Öppna Jenkins i en webbläsare. Välj **Nytt objekt**.
2. Ange ett namn för jobbet och välj **Pipeline**. Välj **OK**.
3. Välj den **Pipeline** fliken.
4. För den **Definition** värde, väljer **Pipeline-skriptet från SCM**.
5. För den **SCM** värde, väljer **Git**. Ange GitHub-URL: en för din förgrenade lagringsplats. Till exempel: https://&lt;your_forked_repo > .git.
6. Uppdatera den **skriptets sökväg** värde att **Jenkinsfile_ftp_plugin**.
7. Välj **spara** och kör jobbet.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Konfigurera Jenkins för att distribuera Web App for Containers

Web Apps på Linux stöder distributioner med hjälp av Docker. För att distribuera din webbapp med Docker, måste du ange en Dockerfile som din webbapp med en service runtime-paket i en dockeravbildning. Plugin-programmet Jenkins sedan skapas avbildningen, skickar den till ett Docker-register och distribuerar avbildningen till webbappen.

Web Apps på Linux stöder också traditionella distributionsmetoder, t.ex. Git och FTP, men endast för inbyggda språk (.NET Core, Node.js, PHP och Ruby). För övriga språk måste du paketera din kod och tjänsten körning av program tillsammans i en dockeravbildning och använda Docker för att distribuera.

Innan du konfigurerar jobbet i Jenkins, behöver du en webbapp on Linux. Du måste också ett behållarregister för att lagra och hantera dina privata Docker-behållaravbildningar. Du kan använda DockerHub för att skapa behållarregistret. I det här exemplet använder vi Azure Container Registry.

* [Skapa din webbapp i Linux](../app-service/containers/quickstart-nodejs.md).
* Azure Container Registry är en hanterad [Docker-register](https://docs.docker.com/registry/) tjänst som är baserad på öppen källkod av Docker-register version 2.0. [Skapa ett Azure container registry](/azure/container-registry/container-registry-get-started-azure-cli). Du kan också använda DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Konfigurera Jenkins-jobbet för Docker

1. Skapa en ny **freestyle** projekt på Jenkins-instrumentpanelen.
2. Konfigurera den **Source Code Management** fält som du vill använda din lokala förgrening av den [enkel Java-webbapp för Azure](https://github.com/azure-devops/javawebappsample). Ange den **URL för databasen** värde. Till exempel: http://github.com/&lt; your_ID > / javawebappsample.
3. Lägg till ett steg för att skapa projektet med hjälp av Maven genom att lägga till en **köra shell** kommando. Inkludera följande rad i kommandot:
    ```bash
    mvn clean package
    ```

4. Lägg till en åtgärd för post-build genom att välja **publicera ett Azure Web Apps**.
5. Ange **mySp** som huvudsaklig Azure-tjänst. Den här huvudnamn lagrades som den [Azure Credentials](#service-principal) i föregående steg.
6. I den **Appkonfiguration** väljer resursgruppens namn och en Linux-webbapp i din prenumeration.
7. Välj **publicera via Docker**.
8. Fyll i den **Dockerfile** värdet för åtgärdssökvägen. Du kan behålla standard värdet /Dockerfile.
För den **Docker registry URL** värde, ange URL: en med hjälp av formatet https://&lt;yourRegistry >. azurecr.io om du använder Azure Container Registry. Om du använder DockerHub kan lämna värdet tomt.
9. För den **autentiseringsuppgifter för registret** värde, Lägg till autentiseringsuppgift för behållarregistret. Du kan få det användar-ID och lösenord genom att köra följande kommandon i Azure CLI. Det första kommandot gör det möjligt för administratörskontot:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Docker-avbildning namn och tagg värdet i den **Avancerat** fliken är valfria. Som standard hämtas värdet för namnet på avbildningen från avbildningens namn som du konfigurerade i Azure-portalen i den **Dockerbehållare** inställningen. Taggen genereras från $BUILD_NUMBER.
    > [!NOTE]
    > Se till att ange avbildningens namn i Azure-portalen eller ange en **Docker-avbildning** värde i den **Avancerat** fliken. För det här exemplet ställer du in den **Docker-avbildning** värde att &lt;your_Registry >.azurecr.io/calculator och lämna den **Docker bildtagg** värdet tomt.

11. Det går inte att avsnittet distribuera om du använder en inbyggd inställning för Docker-avbildning. Ändra konfigurationen av Docker för att använda en anpassad avbildning i den **Dockerbehållare** i Azure-portalen. Använd metoden som överför fil för att distribuera för en inbyggd avbildning.
12. Liknar metoden som överför fil, du kan välja en annan **fack** namn än **produktion**.
13. Spara och skapa projektet. Behållaravbildningen skickas till ditt register och webbappen har distribuerats.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Distribuera Web App for Containers med hjälp av Jenkins-Pipeline

1. Öppna i GitHub-gränssnittet i **Jenkinsfile_container_plugin** fil. Välj pennikonen för att redigera filen. Uppdatera den **resourceGroup** och **webAppName** definitioner för din webbapp på linjer 11 och 12, respektive:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Ändra rad 13 till din container registry-server:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Ändra rad 16 använder autentiseringsuppgifts-ID i Jenkins-instansen:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Skapa en Jenkins-pipeline    

1. Öppna Jenkins i en webbläsare. Välj **Nytt objekt**.
2. Ange ett namn för jobbet och välj **Pipeline**. Välj **OK**.
3. Välj den **Pipeline** fliken.
4. För den **Definition** värde, väljer **Pipeline-skriptet från SCM**.
5. För den **SCM** värde, väljer **Git**. Ange GitHub-URL: en för din förgrenade lagringsplats. Till exempel: https://&lt;your_forked_repo > .git.
7. Uppdatera den **skriptets sökväg** värde att **Jenkinsfile_container_plugin**.
8. Välj **spara** och kör jobbet.

## <a name="verify-your-web-app"></a>Kontrollera din webbapp

1. Kontrollera att WAR-filen har distribuerats till din webbapp, öppna en webbläsare.
2. Gå till http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Ersätt &lt;your_app_name > med namnet på din webbapp. Du ser meddelandet:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Gå till http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Ersätt &lt;x > och &lt;y > med alla värden för att hämta summan av x + y. Kalkylatorn visar summan: ![Kalkylatorn: Lägg till](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>För Azure App Service på Linux

1. Kör följande kommando för att verifiera din webbapp i Azure CLI:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Följande meddelande visas:
    ```CLI
    ["calculator"]
    ```
    
2. Gå till http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Ersätt &lt;your_app_name > med namnet på din webbapp. Du ser meddelandet: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Gå till http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Ersätt &lt;x > och &lt;y > med alla värden för att hämta summan av x + y.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av plugin-programmet Jenkins

Om du stöter på buggar med Jenkins plugin-program kan du rapportera problemet i den [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du plugin-programmet Jenkins för Azure App Service för att distribuera till Azure.

Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera Jenkins för att distribuera Azure App Service med FTP 
> * Konfigurera Jenkins för distribution till Web App for Containers 
