---
title: Distribuera till Azure App Service med plugin-programmet Jenkins
description: Lär dig använda plugin-programmet Jenkins i Azure App Service för att distribuera en Java-webbapp till Azure i Jenkins
ms.service: jenkins
keywords: jenkins, azure, devops, app service
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: b364dfb033c3af640892bb305d7df3c916dd3fef
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43095775"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Distribuera till Azure App Service med plugin-programmet Jenkins 

Om du vill distribuera en Java-webbapp till Azure kan du använda Azure CLI i [Jenkins Pipeline](/azure/jenkins/execute-cli-jenkins-pipeline) eller [plugin-programmet Jenkins för Azure App Service](https://plugins.jenkins.io/azure-app-service). Plugin-programmet Jenkins version 1.0 har stöd för kontinuerlig distribution med Web Apps-funktionen i Azure App Service via:
* Git eller FTP.
* Docker för Web Apps på Linux.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera Jenkins för distribution av Web Apps via Git eller FTP.
> * Konfigurera Jenkins för distribution av Web App for Containers.

## <a name="create-and-configure-a-jenkins-instance"></a>Skapa och konfigurera en Jenkins-instans

Om du inte redan har ett Jenkins-original börjar du med [lösningsmallen](install-jenkins-solution-template.md), som innehåller Java Development Kit (JDK) version 8 och följande obligatoriska Jenkins-plugin-program:

* [Klient-plugin-programmet Jenkins Git](https://plugins.jenkins.io/git-client) version 2.4.6 
* [Plugin-programmet Docker Commons](https://plugins.jenkins.io/docker-commons) version 1.4.0
* [Azure Credentials](https://plugins.jenkins.io/azure-credentials) version 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-service) version 0.1

Du kan använda plugin-programmet Jenkins för att distribuera en webbapp på valfritt språk som stöds av Web Apps, till exempel C#, PHP, Java eller Node.js. I den här självstudien använder vi en [enkel Java-webbapp för Azure](https://github.com/azure-devops/javawebappsample). Om du vill förgrena lagringsplatsen till ditt eget GitHub-konto väljer du knappen **Fork** (Förgrening) i det övre högra hörnet i GitHub-gränssnittet.  
> [!NOTE]
> Java JDK och Maven krävs för att skapa Java-projektet. Installera komponenterna på Jenkins-originalet, eller på VM-agenten om du använder agenten för kontinuerlig integrering. 

Installera komponenterna genom att logga in till Jenkins-instansen med SSH och köra följande kommandon:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Om du vill distribuera till Web App for Containers installerar du Docker på Jenkins-originalet eller på den VM-agent som används för kompileringen. Instruktioner finns i [Installera Docker på Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a> Lägg till ett Azure-tjänsthuvudnamn till autentiseringsuppgifterna för Jenkins

Du behöver ett Azure-tjänsthuvudnamn för att kunna distribuera till Azure. 


1. Skapa en Azure-tjänsthuvudnamn med [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) eller [Azure-portalen](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. På Jenkins-instrumentpanelen väljer du **Autentiseringsuppgifter** > **System**. Välj sedan **Globala autentiseringsuppgifter (obegränsade)**.
3. Lägg till ett tjänsthuvudnamn för Microsoft Azure genom att välja **Lägg till autentiseringsuppgifter**. Ange värden i fälten **Prenumerations-ID**, **Klient-ID**, **Klienthemlighet** och **OAuth 2.0 Token Endpoint**. Ange fältet **ID** till **mySp**. Vi använder detta ID i efterföljande steg i den här artikeln.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Konfigurera Jenkins för distribution av Web Apps med filuppladdning

Distribuera ditt projekt till Web Apps genom att ladda upp dina versionsartefakter (till exempel en WAR-fil i Java) med Git eller FTP.

Innan du konfigurerar jobbet i Jenkins behöver du en Azure App Service-plan och en webbapp för att köra Java-appen.


1. Skapa en Azure App Service-plan med den **kostnadsfria** prisnivån med `az appservice plan create` [Azure CLI-kommandot](/cli/azure/appservice/plan#az-appservice-plan-create). I App Service-planen definieras de fysiska resurser som används som värd för dina appar. Alla program som har tilldelats en App Service-plan delar de här resurserna. Delade resurser hjälper dig att minska kostnaderna vid värdskap för flera appar.
2. Skapa en webbapp. Du kan använda [Azure-portalen](/azure/app-service-web/web-sites-configure) eller följande `az` Azure CLI-kommando:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Konfigurera den Java Runtime-konfiguration som appen behöver. Följande Azure CLI-kommando konfigurerar webbappen att köras på en ny Java 8 JDK och [Apache Tomcat](http://tomcat.apache.org/) 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Konfigurera Jenkins-jobbet

1. Skapa ett nytt **freestyle**-projekt på Jenkins-instrumentpanelen.
2. Konfigurera fältet **Source Code Management** (Källkodshantering) att använda din lokala förgrening av den [enkla Java-webbappen för Azure](https://github.com/azure-devops/javawebappsample). Ange värdet **lagringsplats-URL**. Till exempel: http://github.com/&lt; ditt_ID >/javawebappsample.
3. Lägg till ett steg för att kompilera projektet med hjälp av Maven genom att lägga till kommandot **Execute shell**. I det här exemplet behöver vi ett extra kommando för att byta namn på \*.war-filen i målmappen till **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Lägg till en efterkompileringsåtgärd genom att välja **Publicera en Azure Web App**.
5. Ange **mySp** som Azure-tjänstens huvudnamn. Detta huvudnamn lagrades som [Azure-autentiseringsuppgifter](#service-principal) i ett tidigare steg.
6. I avsnittet **Appkonfiguration** väljer du resursgruppen och webbappen i din prenumeration. Plugin-programmet Jenkins känner automatiskt av om webbappen är baserad på Windows eller Linux. För en Windows-webbapp visas alternativet **Publicera filer**.
7. Fyll i de filer som du vill distribuera. Ange till exempel WAR-paketet om du använder Java. Använd de valfria parametrarna för **källkatalog** och **målkatalog** för att ange käll- och målmappar för filuppladdning. Java-webbappen i Azure körs på en Tomcat-server. För Java laddar du därför upp ditt WAR-paket till webbappens mapp. För det här exemplet anger du värdet för **Källkatalog** till **target** (mål) och värdet för **Målkatalog** till **webapps**.
8. Om du vill distribuera till en annan plats än produktion kan du också ange namnet för **Slot** (fack).
9. Spara projektet och kompilera det. Din webbapp distribueras till Azure när kompileringen är klar.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Distribuera Web Apps genom att ladda upp filer med Jenkins Pipeline

Plugin-programmet Jenkins i Azure App Service är klart för pipeline. Du kan referera till följande exempel på GitHub-lagringsplatsen.

1. Gå till GitHub-gränssnittet och öppna filen **Jenkinsfile_ftp_plugin**. Välj pennikonen för att redigera filen. Uppdatera definitionerna för **resourceGroup** och **webAppName** för din webbapp på rad 11 respektive 12:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Ange definitionen **withCredentials** på rad 14 som autentiseringsuppgifts-ID:t i din Jenkins-instans:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Skapa en Jenkins-pipeline

1. Öppna Jenkins i en webbläsare. Välj **Nytt objekt**.
2. Ange ett namn för jobbet och välj **Pipeline**. Välj **OK**.
3. Välj fliken **Pipeline**.
4. För värdet **Definition** väljer du **Pipeline-skript från SCM**.
5. För värdet **SCM** väljer du **Git**. Ange GitHub-URL:en för din förgrenade lagringsplats. Exempel: https://&lt;din_förgrenade_lagringsplats>.git.
6. Uppdatera värdet för **Skriptets sökväg** till **Jenkinsfile_ftp_plugin**.
7. Välj **Spara** och kör jobbet.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Konfigurera Jenkins för distribution av Web App för Containers

Web Apps på Linux har stöd för distributioner med Docker. För att kunna distribuera webbappen med Docker måste du ange en Dockerfile som paketerar webbappen med en tjänst-CLR till en Docker-avbildning. Plugin-programmet Jenkins skapar sedan avbildningen, skickar den till ett Docker-register och distribuerar avbildningen till webbappen.

Web Apps på Linux kan också användas med traditionella distributionsmetoder som Git och FTP, men endast för inbyggda språk (.NET Core, Node.js, PHP och Ruby). För andra språk än dessa måste du paketera programkoden och tjänst-CLR:en tillsammans i en Docker-avbildning och använda Docker för distributionen.

Innan du konfigurerar jobbet i Jenkins behöver du ha en webbapp på Linux. Du behöver också ha ett containerregister för lagring och hantering av dina privata avbildningar av Docker-containrar. Du kan skapa containerregistret med DockerHub. I det här exemplet använder vi Azure Container Registry.

* [Skapa din webbapp i Linux](../app-service/containers/quickstart-nodejs.md).
* Azure Container Registry är en hanterad [Docker Registry](https://docs.docker.com/registry/)-tjänst baserad på den öppna källkoden Docker Registry version 2.0. [Skapa ett Azure-containerregister](/azure/container-registry/container-registry-get-started-azure-cli). Du kan även använda DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Konfigurera Jenkins-jobbet för Docker

1. Skapa ett nytt **freestyle**-projekt på Jenkins-instrumentpanelen.
2. Konfigurera fältet **Source Code Management** (Källkodshantering) att använda din lokala förgrening av den [enkla Java-webbappen för Azure](https://github.com/azure-devops/javawebappsample). Ange värdet **lagringsplats-URL**. Till exempel: http://github.com/&lt; ditt_ID >/javawebappsample.
3. Lägg till ett steg för att kompilera projektet med hjälp av Maven genom att lägga till kommandot **Execute shell**. Inkludera följande rad i kommandot:
    ```bash
    mvn clean package
    ```

4. Lägg till en efterkompileringsåtgärd genom att välja **Publicera en Azure Web App**.
5. Ange **mySp** som Azure-tjänstens huvudnamn. Detta huvudnamn lagrades som [Azure-autentiseringsuppgifter](#service-principal) i ett tidigare steg.
6. I avsnittet **Appkonfiguration** väljer du resursgruppen och en Linux-webbapp i din prenumeration.
7. Välj **Publicera via Docker**.
8. Fyll i värdet för **Dockerfile**-sökväg. Du kan behålla standardvärdet /Dockerfile.
För värdet **URL för Docker-register** anger du webbadressen i formatet https://&lt;dittRegister>. azurecr.io om du använder Azure Container Registry. Om du använder DockerHub lämnar du värdet tomt.
9. För värdet **autentiseringsuppgifter för registret** lägger du till autentiseringsuppgiften för containerregistret. Du kan få användar-ID och lösenord genom att köra följande kommandon i Azure CLI. Det första kommandot ger administratörskontot:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Docker-avbildningens namn och taggvärde på fliken **Avancerat** är valfria. Som standard hämtas värdet för avbildningens namn från det avbildningsnamn som du konfigurerade i Azure-portalen i inställningen för **Docker Container**. Taggen genereras från $BUILD_NUMBER.
    > [!NOTE]
    > Du måste ange avbildningens namn i Azure-portalen eller ange ett värde för **Docker-avbildning** på fliken **Avancerat**. I det här exemplet ställer du in värdet **Docker-avbildning** till &lt;ditt_Register>.azurecr.io/calculator och lämnar värdet **Docker Image Tab** (Docker-avbildningstagg) tomt.

11. Det går inte att distribuera om du använder en inbyggd inställning för Docker-avbildning. Ändra Docker-konfigurationen om du vill använda en anpassad avbildning i inställningen **Docker Container** i Azure-portalen. För en inbyggd avbildning använder du filuppladdningsmetoden för distributionen.
12. På liknande sätt som med filuppladdningsmetoden kan du välja ett annat namn för **Slot** (fack) än **produktion**.
13. Spara och kompilera projektet. Containeravbildningen skickas till ditt register och webbappen distribueras.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Distribuera Web App för containrar med Jenkins Pipeline

1. Gå till GitHub-gränssnittet och öppna filen **Jenkinsfile_container_plugin**. Välj pennikonen för att redigera filen. Uppdatera definitionerna för **resourceGroup** och **webAppName** för din webbapp på rad 11 respektive 12:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Ändra rad 13 till din container registry-server:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Ändra rad 16 så att autentiseringsuppgifts-ID i Jenkins-instansen används:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Skapa en Jenkins-pipeline    

1. Öppna Jenkins i en webbläsare. Välj **Nytt objekt**.
2. Ange ett namn för jobbet och välj **Pipeline**. Välj **OK**.
3. Välj fliken **Pipeline**.
4. För värdet **Definition** väljer du **Pipeline-skript från SCM**.
5. För värdet **SCM** väljer du **Git**. Ange GitHub-URL:en för din förgrenade lagringsplats. Exempel: https://&lt;din_förgrenade_lagringsplats>.git.
7. Uppdatera värdet **Skriptets sökväg** till **Jenkinsfile_container_plugin**.
8. Välj **Spara** och kör jobbet.

## <a name="verify-your-web-app"></a>Verifiera din webbapp

1. Om du vill kontrollera att WAR-filen har distribuerats till din webbapp öppnar du en webbläsare.
2. Gå till http://&lt;ditt_appnamn>.azurewebsites.net/api/calculator/ping. Ersätt &lt;ditt_appnamn> med namnet på din webbapp. Det här meddelandet visas:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Gå till http://&lt;ditt_appnamn>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Ersätt &lt;x> och &lt;y> med valfria värden för att hämta summan av x + y. Kalkylatorn visar summan: ![Calculator: add](./media/execute-cli-jenkins-pipeline/calculator-add.png) (Kalkylator: lägg till)

### <a name="for-azure-app-service-on-linux"></a>För Azure App Service i Linux

1. Verifiera din webbapp genom att köra följande kommando i Azure CLI:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Följande meddelande visas:
    ```CLI
    ["calculator"]
    ```
    
2. Gå till http://&lt;ditt_appnamn>.azurewebsites.net/api/calculator/ping. Ersätt &lt;ditt_appnamn> med namnet på din webbapp. Det här meddelandet visas: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Gå till http://&lt;ditt_appnamn>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Ersätt &lt;x> och &lt;y> med valfria värden för att hämta summan av x + y.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du plugin-programmet Jenkins för Azure App Service för att distribuera till Azure.

Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera Jenkins för distribution av Azure App Service med FTP 
> * Konfigurera Jenkins för distribution av Web App för containrar 
