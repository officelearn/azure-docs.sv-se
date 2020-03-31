---
title: (FÖRÅLDRAD) CI/CD med Azure Container Service och Swarm
description: Använd Azure Container Service med Docker Swarm, ett Azure-behållarregister och Azure DevOps för att kontinuerligt leverera ett .NET Core-program med flera behållare för att kontinuerligt leverera ett .NET Core-program med flera behållare
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549061"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(FÖRÅLDRAD) Fullständig CI/CD-pipeline för att distribuera ett program med flera behållare på Azure Container Service med Docker Swarm med Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

En av de största utmaningarna när du utvecklar moderna applikationer för molnet är att kunna leverera dessa program kontinuerligt. I den här artikeln får du lära dig hur du implementerar en fullständig kontinuerlig integrations- och distributionspipelline (CI/CD) med Azure Container Service med Docker Swarm, Azure Container Registry och Azure Pipelines management.


![MyShop exempel ansökan](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Målet är att leverera det här programmet kontinuerligt i ett Docker Swarm-kluster med Hjälp av Azure DevOps Services. Följande bild beskriver denna kontinuerliga leveranspipeline:

![MyShop exempel ansökan](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Här är en kort förklaring av stegen:

1. Kodändringar har gjorts i källkodsdatabasen (här GitHub) 
1. GitHub utlöser en version i Azure DevOps Services 
1. Azure DevOps Services hämtar den senaste versionen av källorna och skapar alla avbildningar som utgör programmet 
1. Azure DevOps Services skickar varje avbildning till ett Docker-register som skapats med azure-registret för behållarregister 
1. Azure DevOps Services utlöser en ny version 
1. Versionen kör vissa kommandon med SSH på Azure-klusterklusternoden för behållartjänst 
1. Docker Swarm på klustret drar den senaste versionen av avbildningarna 
1. Den nya versionen av programmet distribueras med Docker Compose 

## <a name="prerequisites"></a>Krav

Innan du startar den här självstudien måste du utföra följande uppgifter:

- [Skapa ett Swarm-kluster i Azure Container Service](container-service-deployment.md)
- [Anslut till Swarm-klustret i Azure Container Service](../container-service-connect.md)
- [Skapa ett Azure-containerregister](../../container-registry/container-registry-get-started-portal.md)
- [Har en Azure DevOps Services organisation och projekt skapas](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Gaffel github-databasen till ditt GitHub-konto](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Du behöver också en Ubuntu (14,04 eller 16,04) maskin med Docker installerat. Den här datorn används av Azure DevOps Services under Azure Pipelines-processerna. Ett sätt att skapa den här datorn är att använda avbildningen som är tillgänglig på Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Steg 1: Konfigurera din Azure DevOps Services-organisation 

I det här avsnittet konfigurerar du din Azure DevOps Services-organisation.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Konfigurera en Azure DevOps Services Linux-byggagent

Om du vill skapa Docker-avbildningar och skicka dessa avbildningar till ett Azure-behållarregister från en Azure DevOps Services-version måste du registrera en Linux-agent. Du har följande installationsalternativ:

* [Distribuera en agent på Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Använda Docker för att köra Azure DevOps Services-agenten](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Installera tillägget Docker Integration Azure DevOps Services

Microsoft tillhandahåller ett Azure DevOps Services-tillägg för att arbeta med Docker i Azure Pipelines-processer. Det här tillägget är tillgängligt på [Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Klicka på **Installera** om du vill lägga till det här tillägget i din Azure DevOps Services-organisation:

![Installera Docker-integreringen](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Du uppmanas att ansluta till din Azure DevOps Services-organisation med dina autentiseringsuppgifter. 

### <a name="connect-azure-devops-services-and-github"></a>Anslut Azure DevOps-tjänster och GitHub

Konfigurera en anslutning mellan ditt Azure DevOps Services-projekt och ditt GitHub-konto.

1. I projektet Azure DevOps Services klickar du på ikonen **Inställningar** i verktygsfältet och väljer **Tjänster**.

    ![Azure DevOps-tjänster – extern anslutning](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Till vänster klickar du på **Ny tjänstslutpunkt** > **GitHub**.

    ![Azure DevOps-tjänster – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Om du vill auktorisera Azure DevOps Services så att de fungerar med ditt GitHub-konto klickar du på **Auktorisera** och följer proceduren i fönstret som öppnas.

    ![Azure DevOps-tjänster – auktorisera GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Anslut Azure DevOps Services till ditt Azure-behållarregister och Azure Container Service-kluster

De sista stegen innan du kommer in i CI/CD-pipelinen är att konfigurera externa anslutningar till behållarregistret och Docker Swarm-klustret i Azure. 

1. Lägg till en tjänstslutpunkt av typen **Docker-registret**i **tjänstinställningarna** för azure DevOps Services- 

1. I popup-fönstret som öppnas anger du URL:en och autentiseringsuppgifterna för ditt Azure-behållarregister.

    ![Azure DevOps Services - Docker-registret](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. För Docker Swarm-klustret lägger du till en slutpunkt av typen **SSH**. Ange sedan SSH-anslutningsinformationen för din Swarm-kluster.

    ![Azure DevOps-tjänster – SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

All konfiguration är klar nu. I nästa steg skapar du CI/CD-pipelinen som bygger och distribuerar programmet till Docker Swarm-klustret. 

## <a name="step-2-create-the-build-pipeline"></a>Steg 2: Skapa byggpipelinen

I det här steget konfigurerar du en byggpipeline för ditt Azure DevOps Services-projekt och definierar byggarbetsflödet för dina behållaravbildningar

### <a name="initial-pipeline-setup"></a>Inledande pipeline-installation

1. Om du vill skapa en build pipeline ansluter du till ditt Azure DevOps Services-projekt och klickar på **Bygg & Release**. 

1. Klicka på **+ Nytt**i avsnittet **Byggdefinitioner** . Välj mallen **Tom.**

    ![Azure DevOps - Pipeline för nybyggnation](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Konfigurera den nya versionen med en GitHub-databaskälla, kontrollera **Kontinuerlig integrering**och välj agentkön där du registrerade din Linux-agent. Klicka på **Skapa** om du vill skapa byggpipelinen.

    ![Azure DevOps Services – Skapa build pipeline](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Öppna först fliken **Databas** på sidan **Byggdefinitioner** och konfigurera versionen så att den använder gaffeln i MyShop-projektet som du skapade i förutsättningarna. Se till att du väljer *acs-docs* som **standardgren**.

    ![Azure DevOps Services – Skapa databaskonfiguration](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Konfigurera den version som ska utlösas efter varje genomförande på fliken **Utlösare.** Välj **Kontinuerlig integrering** och **Batchändringar**.

    ![Azure DevOps Services – konfiguration av build-utlösare](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definiera arbetsflödet för byggversion
Nästa steg definierar byggarbetsflödet. Det finns fem behållaravbildningar att bygga för *MyShop-programmet.* Varje bild skapas med dockerfilen i projektmapparna:

* ProdukterApi
* Proxy
* BetygApi
* RekommendationerApi
* ShopFront (butik)

Du måste lägga till två Docker-steg för varje avbildning, ett för att skapa avbildningen och ett för att driva avbildningen i Azure-behållarregistret. 

1. Om du vill lägga till ett steg i byggarbetsflödet klickar du på **+ Lägg till byggsteg** och väljer **Docker**.

    ![Azure DevOps Services – Lägg till byggsteg](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Konfigurera ett steg som använder `docker build` kommandot för varje avbildning.

    ![Azure DevOps Services - Docker Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    För byggåtgärden väljer du ditt Azure-behållarregister, åtgärden **Skapa en avbildning** och Dockerfile som definierar varje avbildning. Ange **build-kontexten** som Dockerfile-rotkatalogen och definiera **bildnamnet**. 
    
    Som visas på föregående skärm startar du avbildningsnamnet med URI-kort i ditt Azure-behållarregister. (Du kan också använda en byggvariabel för att parameterisera taggen för avbildningen, till exempel byggidentifieraren i det här exemplet.)

1. Konfigurera ett andra steg som använder `docker push` kommandot för varje avbildning.

    ![Azure DevOps-tjänster – Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    För push-åtgärden väljer du ditt Azure-behållarregister, **åtgärden Push a image** och anger **avbildningsnamnet** som är byggt i föregående steg.

1. När du har konfigurerat bygg- och push-stegen för var och en av de fem avbildningarna lägger du till ytterligare två steg i byggarbetsflödet.

    a. En kommandoradsuppgift som använder ett bash-skript för att ersätta *BuildNumber-förekomsten* i filen docker-compose.yml med det aktuella bygg-ID:t. Mer information finns på följande skärm.

    ![Azure DevOps Services – uppdatera skriv-fil](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. En uppgift som släpper den uppdaterade Compose-filen som en byggartefakt så att den kan användas i versionen. Mer information finns på följande skärm.

    ![Azure DevOps Services - Publicera compose-fil](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Klicka på **Spara** och namnge din byggpipeline.

## <a name="step-3-create-the-release-pipeline"></a>Steg 3: Skapa versionspipelinen

Med Azure DevOps Services kan du [hantera versioner i olika miljöer](https://www.visualstudio.com/team-services/release-management/). Du kan aktivera kontinuerlig distribution för att se till att ditt program distribueras i dina olika miljöer (till exempel utveckling, test, förproduktion och produktion) på ett smidigt sätt. Du kan skapa en ny miljö som representerar ditt Azure Container Service Docker Swarm-kluster.

![Azure DevOps Services – släpp till ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Inledande versionsinställning

1. Om du vill skapa en versionspipeline klickar du på **Releases** > **+ Release**

1. Om du vill konfigurera artefaktkällan klickar du **på artefakter** > **Länka en artefaktkälla**. Här länkar du den nya versionspipelinen till den version som du definierade i föregående steg. På så sätt är docker-compose.yml-filen tillgänglig i utgivningsprocessen.

    ![Azure DevOps Services - Släpp artefakter](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Om du vill konfigurera **utlösningsutlösaren** klickar du på Utlösare och väljer **Kontinuerlig distribution**. Ställ in utlösaren på samma artefaktkälla. Den här inställningen säkerställer att en ny version startar så snart versionen har slutförts.

    ![Azure DevOps Services – utlösare av utgivning](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definiera utgivningsarbetsflödet

Utgivningsarbetsflödet består av två uppgifter som du lägger till.

1. Konfigurera en uppgift för att på ett säkert sätt kopiera skrivfilen till en *distributionsmapp* på huvudnoden Docker Swarm med hjälp av SSH-anslutningen som du konfigurerat tidigare. Mer information finns på följande skärm.

    ![Azure DevOps Services - Släpp SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Konfigurera en andra uppgift för att `docker` `docker-compose` köra ett bash-kommando för att köra och kommandon på huvudnoden. Mer information finns på följande skärm.

    ![Azure DevOps-tjänster – släpp bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Kommandot som körs på huvudhanteraren använder Docker CLI och Docker-Compose CLI för att utföra följande uppgifter:

   - Logga in på Azure-behållarregistret (den använder tre byggvariab'les som definieras på fliken **Variabler)**
   - Definiera **den DOCKER_HOST** variabeln som ska fungera med slutpunkten Swarm (:2375)
   - Navigera till *distributionsmappen* som skapades av föregående säker kopieringsuppgift och som innehåller filen docker-compose.yml 
   - Kör `docker-compose` kommandon som hämtar de nya avbildningarna, stoppar tjänsterna, tar bort tjänsterna och skapar behållarna.

     >[!IMPORTANT]
     > Som visas på föregående skärm lämnar du kryssrutan **Misslyckas på STDERR** avmarkerad. Det här är en `docker-compose` viktig inställning, eftersom flera diagnostikmeddelanden, till exempel behållare stoppas eller tas bort, på standardfelutdata. Om du markerar kryssrutan rapporterar Azure DevOps Services att fel uppstod under versionen, även om allt går bra.
     >
1. Spara den här nya versionspipelinen.


>[!NOTE]
>Den här distributionen innehåller vissa driftstopp eftersom vi stoppar de gamla tjänsterna och kör den nya. Det är möjligt att undvika detta genom att göra en blå-grön distribution.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Steg 4. Testa CI/CD-pipelinen

Nu när du är klar med konfigurationen är det dags att testa den här nya CI/CD-pipelinen. Det enklaste sättet att testa det är att uppdatera källkoden och genomföra ändringarna i din GitHub-databas. Några sekunder efter att du har pushat koden visas en ny version som körs i Azure DevOps Services. När den har slutförts kommer en ny version att utlösas och distribuera den nya versionen av programmet på Azure Container Service-klustret.

## <a name="next-steps"></a>Efterföljande moment

* Mer information om CI/CD med Azure DevOps Services finns i artikeln dokumentation för [Azure Pipelines.](/azure/devops/pipelines/?view=azure-devops)
