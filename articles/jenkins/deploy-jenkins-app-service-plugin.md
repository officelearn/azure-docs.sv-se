---
title: "Distribuera till Azure App Service med hjälp av plugin-programmet för Jenkins | Microsoft Docs"
description: "Lär dig hur du använder Azure App Service Jenkins plugin-programmet för att distribuera en Java-webbapp till Azure i Jenkins"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: e38c69ec55d894053792fbf284d07944d7f44dc0
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Distribuera till Azure App Service med hjälp av Jenkins plugin-programmet 

Om du vill distribuera en Java-webbapp till Azure kan du använda Azure CLI i [Jenkins Pipeline](/azure/jenkins/execute-cli-jenkins-pipeline) eller så kan du använda den [plugin-program för Azure App Service Jenkins](https://plugins.jenkins.io/azure-app-service). Jenkins plugin-programmet för version 1.0 stöder kontinuerlig distribution med hjälp av funktionen Web Apps i Azure App Service via:
* Git och FTP.
* Docker för Web Apps i Linux.

I den här guiden får du lära dig hur man:
> [!div class="checklist"]
> * Konfigurera Jenkins för att distribuera webbprogram via Git och FTP.
> * Konfigurera Jenkins för att distribuera webbprogram för behållare.

## <a name="create-and-configure-a-jenkins-instance"></a>Skapa och konfigurera en Jenkins-instans

Om du inte redan har en Jenkins Master, börja med den [lösningsmall](install-jenkins-solution-template.md), som innehåller Java Development Kit (JDK) version 8 och de obligatoriska Jenkins plugin-program:

* [Jenkins Git-plugin för klienten](https://plugins.jenkins.io/git-client) version 2.4.6 
* [Plugin-programmet för docker Commons](https://plugins.jenkins.io/docker-commons) version 1.4.0
* [Autentiseringsuppgifter för Azure](https://plugins.jenkins.io/azure-credentials) version 1.2
* [Azure Apptjänst](https://plugins.jenkins.io/azure-app-server) version 0.1

Du kan använda Jenkins plugin-programmet för att distribuera en webbapp i alla språk som stöds av webbprogram, till exempel C#, PHP, Java och Node.js. I den här självstudiekursen kommer vi att använda en [enkel Java-webbapp för Azure](https://github.com/azure-devops/javawebappsample). Om du vill duplicera lagringsplatsen till ditt GitHub-konto, Välj den **Återställningsförgreningar** -knappen i det övre högra hörnet på GitHub-gränssnittet.  
> [!NOTE]
> JDK Java och Maven krävs för att skapa Java-projektet. Installera komponenterna på Jenkins Master eller på den Virtuella datoragenten om du använder agenten för kontinuerlig integration. 

Installera komponenterna genom att logga in på Jenkins-instans med SSH och kör följande kommandon:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Installera Docker på Jenkins Master eller VM-agenten som används för att bygga om du vill distribuera till webbprogram för behållare. Instruktioner finns i [installera Docker på Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a>Lägg till en Azure-tjänstens huvudnamn i Jenkins autentiseringsuppgifter

Du behöver ett Azure tjänstens huvudnamn för att distribuera till Azure. 


1. Använd för att skapa en Azure tjänstens huvudnamn i [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) eller [Azure-portalen](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Markera på instrumentpanelen Jenkins **autentiseringsuppgifter** > **System**. Markera **globala credentials(unrestricted)**.
3. Om du vill lägga till ett huvudnamn för tjänsten Microsoft Azure, Välj **lägga till autentiseringsuppgifter**. Ange värden för den **prenumerations-ID**, **klient-ID**, **Klienthemlighet**, och **OAuth 2.0-Token för slutpunkt** fält. Ange den **ID** till **mySp**. Vi kan använda detta ID i efterföljande steg i den här artikeln.


## <a name="configure-jenkins-to-deploy-web-apps-through-git-and-ftp"></a>Konfigurera Jenkins för att distribuera webbprogram via Git och FTP

Du kan ladda upp build-artefakter (till exempel en WAR-fil i Java) för att distribuera projektet till webbprogram med hjälp av Git och FTP.

Innan du konfigurerar jobbet i Jenkins behöver du en Azure App Service-plan och ett webbprogram för att köra Java-app.


1. Skapa en Azure App Service-plan med de **lediga** prisnivån med hjälp av den `az appservice plan create` [Azure CLI kommandot](/cli/azure/appservice/plan#create). Programtjänstplanen definierar de fysiska resurserna som används som värd för dina appar. Alla program som har tilldelats en apptjänstplan dela dessa resurser. Delade resurser hjälper dig att spara på kostnader när värd för flera appar.
2. Skapa en webbapp. Du kan använda den [Azure-portalen](/azure/app-service-web/web-sites-configure) eller följande `az` Azure CLI-kommando:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Ställa in Java runtime-konfiguration som din app behöver. Följande Azure CLI-kommando konfigurerar webbprogram för att köra på en senaste JDK-8 och [Apache Tomcat](http://tomcat.apache.org/) version 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Ställ in Jenkins-jobb

1. Skapa en ny **freestyle** projektet på instrumentpanelen Jenkins.
2. Konfigurera den **källa kod Management** fält som du vill använda din lokala förgrening av den [enkel Java-webbapp för Azure](https://github.com/azure-devops/javawebappsample). Ange den **databasen URL** värde. Till exempel: http://github.com/&lt;your_ID > / javawebappsample.
3. Lägg till ett steg för att bygga projektet med Maven genom att lägga till den **köra shell** kommando. I det här exemplet behöver vi ytterligare ett kommando för att byta namn på den \*.war filen i målmappen för **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Lägg till en efter build-åtgärd genom att välja **publicera ett Azure Web App**.
5. Ange **mySp** som Azure tjänstens huvudnamn. Detta säkerhetsobjekt lagrades som den [Azure-autentiseringsuppgifterna](#service-principal) i föregående steg.
6. I den **Appkonfiguration** väljer resurs grupp och webb-app i din prenumeration. Jenkins plugin-programmet identifierar automatiskt om webbprogrammet baseras på Windows- eller Linux. För en Windows-webbapp i **publicera filer** alternativet visas.
7. Fyll i de filer som du vill distribuera. Ange till exempel WAR-paket om du använder Java. Använd det valfria **källkatalog** och **målkatalogen** parametrar för att ange källa och mål mappar att använda för filöverföring. Java-webbapp på Azure körs i en Tomcat-server. Så för Java överför du WAR-paket till webbappens mapp. Det här exemplet anger den **källkatalog** värde till **mål** och **målkatalogen** värde till **webbappar**.
8. Om du vill distribuera till en annan plats än produktion kan du också ange den **fack** namn.
9. Spara projektet och skapa den. Ditt webbprogram har distribuerats till Azure när versionen har slutförts.

### <a name="deploy-web-apps-through-ftp-by-using-jenkins-pipeline"></a>Distribuera webbprogram via FTP-genom att använda Jenkins Pipeline

Azure App Service Jenkins plugin-programmet är redo pipeline. Du kan se i följande exempel i GitHub-lagringsplatsen.

1. Öppna i GitHub-gränssnittet i **Jenkinsfile_ftp_plugin** fil. Om du vill redigera filen, Välj på pennikonen. Uppdatering av **resourceGroup** och **webAppName** definitioner för ditt webbprogram på rader 11 och 12 respektive:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Ange den **withCredentials** definitionen på rad 14 till uppgifts-ID i Jenkins-instans:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Skapa en Jenkins pipeline

1. Öppna Jenkins i en webbläsare. Välj **nytt objekt**.
2. Ange ett namn för jobbet och välj **Pipeline**. Välj **OK**.
3. Välj den **Pipeline** fliken.
4. För den **Definition** värde, Välj **Pipeline-skriptet från SCM**.
5. För den **SCM** värde, Välj **Git**. Ange GitHub-URL för din andelen vridvuxna lagringsplatsen. Till exempel: https://&lt;your_forked_repo > .git.
6. Uppdatering av **skriptsökvägen** värde till **Jenkinsfile_ftp_plugin**.
7. Välj **spara** och kör jobbet.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Konfigurera Jenkins för att distribuera webbprogram för behållare

Web Apps i Linux stöder distribution genom att använda Docker. Om du vill distribuera ditt webbprogram med hjälp av Docker som du behöver ange en Dockerfile som ditt webbprogram med en körtid-paket till en Docker-bild. Jenkins plugin-programmet sedan skapar en image, skickar den till en Docker-registret och distribuerar avbildningen till ditt webbprogram.

Web Apps i Linux stöder också traditionella distributionsmetoder, t.ex. Git och FTP, men endast för inbyggda språk (.NET Core, Node.js, PHP och Ruby). För andra språk måste du paketera programmets kod och tjänsten körning tillsammans till en Docker-avbildning och använda Docker för att distribuera.

Innan du konfigurerar jobbet i Jenkins måste ett webbprogram på Linux. Du måste också en behållare registret för att lagra och hantera dina privata Docker behållare avbildningar. Du kan använda DockerHub för att skapa behållaren registernyckel. I det här exemplet använder vi Azure Container registret.

* [Skapa ditt webbprogram på Linux](../app-service/containers/quickstart-nodejs.md).
* Azure-behållare är en hanterad [Docker registret](https://docs.docker.com/registry/) service som baseras på öppen källkod Docker registret version 2.0. [Skapa en Azure-behållaren registernyckel](/azure/container-registry/container-registry-get-started-azure-cli). Du kan också använda DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Konfigurera Jenkins jobbet för Docker

1. Skapa en ny **freestyle** projektet på instrumentpanelen Jenkins.
2. Konfigurera den **källa kod Management** fält som du vill använda din lokala förgrening av den [enkel Java-webbapp för Azure](https://github.com/azure-devops/javawebappsample). Ange den **databasen URL** värde. Till exempel: http://github.com/&lt;your_ID > / javawebappsample.
3. Lägg till ett steg för att bygga projektet med Maven genom att lägga till en **köra shell** kommando. Ta med följande rad i kommandot:
    ```bash
    mvn clean package
    ```

4. Lägg till en efter build-åtgärd genom att välja **publicera ett Azure Web App**.
5. Ange **mySp** som Azure tjänstens huvudnamn. Detta säkerhetsobjekt lagrades som den [Azure-autentiseringsuppgifterna](#service-principal) i föregående steg.
6. I den **Appkonfiguration** väljer resursgruppen och en Linux-webbapp i din prenumeration.
7. Välj **publicera via Docker**.
8. Fyll i den **Dockerfile** värdet sökväg. Du kan behålla standard värdet /Dockerfile.
För den **Docker registret URL** värde, ange URL: en med hjälp av formatet https://&lt;yourRegistry >. azurecr.io om du använder Azure-behållare registret. Om du använder DockerHub, lämnar du värdet tomt.
9. För den **registret autentiseringsuppgifter** värde, Lägg till autentiseringsuppgifterna för behållaren registret. Du kan hämta användar-ID och lösenord genom att köra följande kommandon i Azure CLI. Det första kommandot gör det möjligt för kontot:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Docker bild namn och en tagg värdet i den **Avancerat** fliken är valfria. Som standard värdet för Avbildningsnamnet hämtas från det namn som du konfigurerade i Azure-portalen i den **Dockerbehållare** inställningen. Taggen genereras från $BUILD_NUMBER.
    > [!NOTE]
    > Se till att ange avbildning i Azure-portalen eller ange en **Docker bild** värde i den **Avancerat** fliken. Det här exemplet anger den **Docker bild** värde till &lt;your_Registry >.azurecr.io/calculator och lämna den **Docker bildtagg** värdet tomt.

11. Det går inte att distribuera om du använder en inbyggd Docker avbildningen inställning. Ändra Docker-konfiguration om du vill använda en anpassad avbildning i den **Dockerbehållare** i Azure-portalen. Använd metoden överför filen för att distribuera för en inbyggd avbildning.
12. Liknar metoden överför filen, kan du välja en annan **fack** namn än **produktion**.
13. Spara och skapa projektet. Behållaren avbildningen skickas till registret och webbprogrammet har distribuerats.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Distribuera webbprogram för behållare genom att använda Jenkins Pipeline

1. Öppna i GitHub-gränssnittet i **Jenkinsfile_container_plugin** fil. Om du vill redigera filen, Välj på pennikonen. Uppdatering av **resourceGroup** och **webAppName** definitioner för ditt webbprogram på rader 11 och 12 respektive:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Ändra 13 till behållaren registret servern:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Ändra 16 att använda uppgifts-ID i Jenkins-instans:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Skapa en Jenkins pipeline    

1. Öppna Jenkins i en webbläsare. Välj **nytt objekt**.
2. Ange ett namn för jobbet och välj **Pipeline**. Välj **OK**.
3. Välj den **Pipeline** fliken.
4. För den **Definition** värde, Välj **Pipeline-skriptet från SCM**.
5. För den **SCM** värde, Välj **Git**. Ange GitHub-URL för din andelen vridvuxna lagringsplatsen. Till exempel: https://&lt;your_forked_repo > .git.
7. Uppdatering av **skriptsökvägen** värde till **Jenkinsfile_container_plugin**.
8. Välj **spara** och kör jobbet.

## <a name="verify-your-web-app"></a>Kontrollera ditt webbprogram

1. Öppna en webbläsare för att verifiera att WAR-filen har distribueras till ditt webbprogram.
2. Gå till http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Ersätt &lt;your_app_name > med namnet på ditt webbprogram. Du ser meddelandet:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Gå till http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Ersätt &lt;x > och &lt;y > med några siffror för att hämta summan av x + y. Räknaren visar summan: ![Kalkylatorn: Lägg till](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>För Azure App Service på Linux

1. Kör följande kommando i Azure CLI för att verifiera ditt webbprogram:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Följande meddelande visas:
    ```CLI
    ["calculator"]
    ```
    
2. Gå till http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Ersätt &lt;your_app_name > med namnet på ditt webbprogram. Du ser meddelandet: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Gå till http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Ersätt &lt;x > och &lt;y > med några siffror för att hämta summan av x + y.
    
## <a name="next-steps"></a>Nästa steg

I den här kursen används Azure App Service Jenkins plugin-programmet för att distribuera till Azure.

Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera Jenkins för att distribuera Azure App Service via FTP 
> * Konfigurera Jenkins för distribution till webbprogrammet för behållare 