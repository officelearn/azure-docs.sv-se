---
title: (FÖRÅLDRAD) CI/CD med Azure Container Service Engine och Swarm Mode
description: Använd Azure Container Service Engine med Docker Swarm Mode, ett Azure-behållarregister och Azure DevOps för att kontinuerligt leverera ett .NET Core-program med flera behållare för att kontinuerligt leverera ett .NET Core-program med flera behållare
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277921"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(FÖRÅLDRAD) Fullständig CI/CD-pipeline för att distribuera ett program med flera behållare på Azure Container Service med ACS Engine och Docker Swarm Mode med Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Den här artikeln är baserad på [fullständig CI/CD-pipeline för att distribuera ett program med flera behållare på Azure Container Service med Docker Swarm med Hjälp av Azure DevOps-dokumentation](container-service-docker-swarm-setup-ci-cd.md)*

Numera är en av de största utmaningarna när du utvecklar moderna applikationer för molnet att kunna leverera dessa program kontinuerligt. I den här artikeln får du lära dig hur du implementerar en fullständig pipeline för kontinuerlig integrering och distribution (CI/CD) med hjälp av: 
* Azure Container Service Engine med Docker Swarm-läge
* Azure Container Registry
* Azure DevOps


![MyShop exempel ansökan](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Målet är att leverera det här programmet kontinuerligt i ett Docker Swarm Mode-kluster med Hjälp av Azure DevOps. Följande bild beskriver denna kontinuerliga leveranspipeline:

![MyShop exempel ansökan](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Här är en kort förklaring av stegen:

1. Kodändringar har gjorts i källkodsdatabasen (här GitHub) 
2. GitHub utlöser en version i Azure DevOps 
3. Azure DevOps hämtar den senaste versionen av källorna och bygger alla avbildningar som utgör programmet 
4. Azure DevOps skickar varje avbildning till ett Docker-register som skapats med azure-registret för behållarregister 
5. Azure DevOps utlöser en ny version 
6. Versionen kör vissa kommandon med SSH på Azure-klusterklusternoden för behållartjänst 
7. Docker Swarm Mode på klustret drar den senaste versionen av bilderna 
8. Den nya versionen av programmet distribueras med Docker Stack 

## <a name="prerequisites"></a>Krav

Innan du startar den här självstudien måste du utföra följande uppgifter:

- [Skapa ett kluster i swarmläge i Azure Container Service med ACS Engine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Anslut till Swarm-klustret i Azure Container Service](../container-service-connect.md)
- [Skapa ett Azure-containerregister](../../container-registry/container-registry-get-started-portal.md)
- [Har en Azure DevOps-organisation och ett azure-projekt skapat](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Gaffel github-databasen till ditt GitHub-konto](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Docker Swarm Orchestrator i Azure Container Service använder äldre fristående versionen av Swarm. Det integrerade [Swarm-läget](https://docs.docker.com/engine/swarm/) (i Docker 1.12 och senare) är inte en orchestrator som stöds i Azure Container Service. Därför använder vi [ACS Engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), en [snabbstartsmall](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)med bidragit community eller en Docker-lösning på [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Steg 1: Konfigurera din Azure DevOps-organisation 

I det här avsnittet konfigurerar du din Azure DevOps-organisation. Om du vill konfigurera Slutpunkter för Azure DevOps Services klickar du på ikonen **Inställningar** i verktygsfältet i azure DevOps-projektet och väljer **Tjänster**.

![Slutpunkt för Öppna tjänster](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Anslut Azure DevOps och Azure-konto

Konfigurera en anslutning mellan ditt Azure DevOps-projekt och ditt Azure-konto.

1. Till vänster klickar du på **Ny tjänstslutpunkt** > **Azure Resource Manager**.
2. Om du vill att Azure DevOps ska fungera med ditt Azure-konto väljer du din **prenumeration** och klickar på **OK**.

    ![Azure DevOps – auktorisera Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Anslut Azure DevOps och GitHub

Konfigurera en anslutning mellan ditt Azure DevOps-projekt och ditt GitHub-konto.

1. Till vänster klickar du på **Ny tjänstslutpunkt** > **GitHub**.
2. Om du vill auktorisera Azure DevOps så att de fungerar med ditt GitHub-konto klickar du på **Auktorisera** och följer proceduren i fönstret som öppnas.

    ![Azure DevOps - Auktorisera GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Ansluta Azure DevOps till ditt Azure Container Service-kluster

De sista stegen innan du kommer in i CI/CD-pipelinen är att konfigurera externa anslutningar till Docker Swarm-klustret i Azure. 

1. För Docker Swarm-klustret lägger du till en slutpunkt av typen **SSH**. Ange sedan SSH-anslutningsinformationen för din Swarm-kluster (huvudnod).

    ![Azure DevOps - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

All konfiguration är klar nu. I nästa steg skapar du CI/CD-pipelinen som bygger och distribuerar programmet till Docker Swarm-klustret. 

## <a name="step-2-create-the-build-pipeline"></a>Steg 2: Skapa byggpipelinen

I det här steget konfigurerar du en byggpipeline för ditt Azure DevOps-projekt och definierar byggarbetsflödet för dina behållaravbildningar

### <a name="initial-pipeline-setup"></a>Inledande pipeline-installation

1. Om du vill skapa en build pipeline ansluter du till ditt Azure DevOps-projekt och klickar på **Skapa & Release**. Klicka på **+ Nytt**i avsnittet **Byggdefinitioner** . 

    ![Azure DevOps - Pipeline för nybyggnation](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Välj den **tomma processen**.

    ![Azure DevOps - Ny tom build pipeline](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Klicka sedan på fliken **Variabler** och skapa två nya variabler: **RegistryURL** och **AgentURL**. Klistra in värdena för dns-dns för register- och klusteragenter.

    ![Konfiguration av Azure DevOps – skapa variabler](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Öppna fliken **Utlösare** på sidan **Byggdefinitioner** och konfigurera bygget så att den kan använda kontinuerlig integrering med gaffeln i MyShop-projektet som du skapade i förutsättningarna. Välj sedan **Batchändringar**. Se till att du väljer *docker-linux* som **branchspecifikation**.

    ![Azure DevOps – skapa databaskonfiguration](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Klicka slutligen på fliken **Alternativ** och konfigurera standardagentkön till **värdbaserade Linux Preview**.

    ![Azure DevOps – konfiguration av värdagenter](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definiera arbetsflödet för byggversion
Nästa steg definierar byggarbetsflödet. Först måste du konfigurera kodens källa. För att göra det väljer du **GitHub** och din **databas** och **gren** (docker-linux).

![Azure DevOps - Konfigurera kodkälla](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Det finns fem behållaravbildningar att bygga för *MyShop-programmet.* Varje bild skapas med dockerfilen i projektmapparna:

* ProdukterApi
* Proxy
* BetygApi
* RekommendationerApi
* ShopFront (butik)

Du behöver två Docker-steg för varje avbildning, ett för att skapa avbildningen och ett för att skicka avbildningen i Azure-behållarregistret. 

1. Om du vill lägga till ett steg i byggarbetsflödet klickar du på **+ Lägg till byggsteg** och väljer **Docker**.

    ![Azure DevOps - Lägg till byggsteg](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Konfigurera ett steg som använder `docker build` kommandot för varje avbildning.

    ![Azure DevOps - Docker Build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    För byggåtgärden väljer du ditt Azure Container-register, åtgärden **Skapa en avbildning** och Dockerfile som definierar varje avbildning. Ange **arbetskatalogen** som Dockerfile-rotkatalog, definiera **bildnamnet**och välj **Inkludera senaste tagg**.
    
    Bildnamnet måste vara i det ```$(RegistryURL)/[NAME]:$(Build.BuildId)```här formatet: . Ersätt **[NAMN]** med bildnamnet:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Konfigurera ett andra steg som använder `docker push` kommandot för varje avbildning.

    ![Azure DevOps - Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    För push-åtgärden väljer du ditt Azure-behållarregister, **åtgärden Push a image,** anger **avbildningsnamnet** som är byggt i föregående steg och väljer **Inkludera senaste tagg**.

4. När du har konfigurerat bygg- och push-stegen för var och en av de fem avbildningarna lägger du till ytterligare tre steg i byggarbetsflödet.

   ![Azure DevOps - Lägg till kommandoradsuppgift](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. En kommandoradsuppgift som använder ett bash-skript för att ersätta *RegistryURL-förekomsten* i filen docker-compose.yml med variabeln RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps - Uppdatera skriv-fil med register-URL](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. En kommandoradsuppgift som använder ett bash-skript för att ersätta *AgentURL-förekomsten* i filen docker-compose.yml med variabeln AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. En uppgift som släpper den uppdaterade Compose-filen som en byggartefakt så att den kan användas i versionen. Mer information finns på följande skärm.

      ![Azure DevOps - Publicera artefakt](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps - Publicera compose-fil](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Klicka på **Spara & kö** för att testa din byggpipeline.

   ![Azure DevOps - Spara och köa](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps - Ny kö](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Om **Build** är korrekt måste du se den här skärmen:

   ![Azure DevOps - Build lyckades](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Steg 3: Skapa versionspipelinen

Med Azure DevOps kan du [hantera versioner i olika miljöer](https://www.visualstudio.com/team-services/release-management/). Du kan aktivera kontinuerlig distribution för att se till att ditt program distribueras i dina olika miljöer (till exempel utveckling, test, förproduktion och produktion) på ett smidigt sätt. Du kan skapa en miljö som representerar ditt Azure Container Service Docker Swarm Mode-kluster.

![Azure DevOps - Släpp till ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Inledande versionsinställning

1. Om du vill skapa en versionspipeline klickar du på **Releases** > **+ Release**

2. Om du vill konfigurera artefaktkällan klickar du **på artefakter** > **Länka en artefaktkälla**. Här länkar du den nya versionspipelinen till den version som du definierade i föregående steg. Därefter är docker-compose.yml-filen tillgänglig i utgivningsprocessen.

    ![Azure DevOps - Släpp artefakter](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Om du vill konfigurera **utlösningsutlösaren** klickar du på Utlösare och väljer **Kontinuerlig distribution**. Ställ in utlösaren på samma artefaktkälla. Den här inställningen säkerställer att en ny version startar när bygget har slutförts.

    ![Azure DevOps - Utlösare av utgivning](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Om du vill konfigurera versionsvariablerna klickar du på **Variabler** och väljer **+Variabel** för att skapa tre nya variabler med informationen i registret: **docker.username**, **docker.password**och **docker.registry**. Klistra in värdena för dns-dns för register- och klusteragenter.

    ![Azure DevOps – skapa databaskonfiguration](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Som visas på föregående skärm klickar du på kryssrutan **Lås** i docker.password. Den här inställningen är viktig för att begränsa lösenordet.
    >

### <a name="define-the-release-workflow"></a>Definiera utgivningsarbetsflödet

Utgivningsarbetsflödet består av två uppgifter som du lägger till.

1. Konfigurera en uppgift för att på ett säkert sätt kopiera skrivfilen till en *distributionsmapp* på huvudnoden Docker Swarm med hjälp av SSH-anslutningen som du konfigurerat tidigare. Mer information finns på följande skärm.
    
    Källa mapp:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps - Släpp SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Konfigurera en andra uppgift för att `docker` `docker stack deploy` köra ett bash-kommando för att köra och kommandon på huvudnoden. Mer information finns på följande skärm.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps - Release Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Kommandot som körs på huvudhanteraren använder Docker CLI och Docker-Compose CLI för att utföra följande uppgifter:

   - Logga in i Azure-behållarregistret (den använder tre byggvariabler som har definierats på fliken **Variabler)**
   - Definiera **den DOCKER_HOST** variabeln som ska fungera med slutpunkten Swarm (:2375)
   - Navigera till *distributionsmappen* som skapades av föregående säker kopieringsuppgift och som innehåller filen docker-compose.yml 
   - Kör `docker stack deploy` kommandon som hämtar de nya avbildningarna och skapar behållarna.

     >[!IMPORTANT]
     > Som visas på föregående skärm lämnar du kryssrutan **Misslyckas på STDERR** avmarkerad. Den här inställningen gör det möjligt `docker-compose` för oss att slutföra utgivningsprocessen på grund av utskrifter av flera diagnostiska meddelanden, till exempel behållare som stannar eller tas bort, på standardfelutdata. Om du markerar kryssrutan rapporterar Azure DevOps att fel uppstod under versionen, även om allt går bra.
     >
3. Spara den här nya versionspipelinen.

## <a name="step-4-test-the-cicd-pipeline"></a>Steg 4: Testa CI/CD-rörledningen

Nu när du är klar med konfigurationen är det dags att testa den här nya CI/CD-pipelinen. Det enklaste sättet att testa det är att uppdatera källkoden och genomföra ändringarna i din GitHub-databas. Några sekunder efter att du har pushat koden visas en ny version som körs i Azure DevOps. När den har slutförts utlöses en ny version och distribueras den nya versionen av programmet på Azure Container Service-klustret.

## <a name="next-steps"></a>Nästa steg

* Mer information om CI/CD med Azure DevOps finns i artikeln dokumentation för [Azure Pipelines.](/azure/devops/pipelines/?view=azure-devops)
* Mer information om ACS Engine finns i [ACS Engine GitHub repo](https://github.com/Azure/acs-engine).
* Mer information om Docker Swarm-läget finns i [översikten över Docker Swarm-läge](https://docs.docker.com/engine/swarm/).
