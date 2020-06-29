---
title: FÖRÅLDRAD CI/CD med Azure Container Service och Swarm
description: Använd Azure Container Service med Docker Swarm, en Azure Container Registry och Azure DevOps för att leverera kontinuerligt ett .NET Core-program med flera behållare
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: ded7d1b6a5f353c009b5c56bcaabe96baf424c51
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445414"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>FÖRÅLDRAD Full CI/CD-pipeline för att distribuera ett program med flera behållare på Azure Container Service med Docker Swarm med hjälp av Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

En av de största utmaningarna när man utvecklar moderna program för molnet kan leverera dessa program kontinuerligt. I den här artikeln får du lära dig hur du implementerar en fullständig kontinuerlig integrering och distribution (CI/CD) genom att använda Azure Container Service med Docker Swarm, Azure Container Registry och hantering av pipelines i Azure.


![Exempel program för köp](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Målet är att leverera det här programmet kontinuerligt i ett Docker Swarm-kluster med hjälp av Azure DevOps Services. Följande bild innehåller information om den här kontinuerliga leverans pipelinen:

![Exempel program för köp](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Här är en kort beskrivning av stegen:

1. Kod ändringar allokeras till käll kod lagrings platsen (här, GitHub) 
1. GitHub utlöser en version i Azure DevOps Services 
1. Azure DevOps Services hämtar den senaste versionen av källorna och skapar alla avbildningar som skapar programmet 
1. Azure DevOps Services push-överför varje avbildning till ett Docker-register som skapats med tjänsten Azure Container Registry 
1. Azure DevOps Services utlöser en ny version 
1. Versionen kör vissa kommandon med SSH på Azure Container Service Cluster Master-noden 
1. Docker-Swarm i klustret hämtar den senaste versionen av avbildningarna 
1. Den nya versionen av programmet distribueras med hjälp av Docker Compose 

## <a name="prerequisites"></a>Krav

Innan du påbörjar den här självstudien måste du utföra följande uppgifter:

- [Skapa ett Swarm-kluster i Azure Container Service](container-service-deployment.md)
- [Anslut till Swarm-klustret i Azure Container Service](../container-service-connect.md)
- [Skapa ett Azure-containerregister](../../container-registry/container-registry-get-started-portal.md)
- [Ha en Azure DevOps Services-organisation och projekt skapat](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Förgrena GitHub-lagringsplatsen till ditt GitHub-konto](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Du behöver också en Ubuntu-dator (14,04 eller 16,04) med Docker installerat. Den här datorn används av Azure DevOps Services under processerna för Azures pipeliner. Ett sätt att skapa den här datorn är att använda avbildningen som är tillgänglig på Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Steg 1: Konfigurera din Azure DevOps Services-organisation 

I det här avsnittet konfigurerar du Azure DevOps Services-organisationen.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Konfigurera en Azure DevOps Services Linux build-agent

Om du vill skapa Docker-avbildningar och skicka avbildningarna till ett Azure Container Registry från en Azure DevOps Services-version måste du registrera en Linux-Agent. Du har följande installations alternativ:

* [Distribuera en agent på Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Använd Docker för att köra Azure DevOps Services-agenten](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Installera tillägget för Docker-integrering av Azure DevOps Services

Microsoft tillhandahåller ett Azure DevOps Services-tillägg för att arbeta med Docker i Azure pipeline-processer. Det här tillägget är tillgängligt i [Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Klicka på **Installera** för att lägga till tillägget i Azure DevOps Services-organisationen:

![Installera Docker-integreringen](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Du uppmanas att ansluta till din Azure DevOps Services-organisation med dina autentiseringsuppgifter. 

### <a name="connect-azure-devops-services-and-github"></a>Anslut Azure DevOps Services och GitHub

Konfigurera en anslutning mellan ditt Azure DevOps Services-projekt och ditt GitHub-konto.

1. I ditt Azure DevOps Services-projekt klickar du på ikonen **Inställningar** i verktygsfältet och väljer **tjänster**.

    ![Azure DevOps Services – extern anslutning](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Klicka på **ny tjänst slut punkt**  >  **GitHub**till vänster.

    ![Azure DevOps-tjänster – GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Om du vill godkänna att Azure DevOps Services fungerar med ditt GitHub-konto klickar du på **auktorisera** och följer proceduren i fönstret som öppnas.

    ![Azure DevOps Services – auktorisera GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Anslut Azure DevOps Services till ditt Azure Container Registry och Azure Container Service kluster

De senaste stegen innan du kom in i CI/CD-pipeline är att konfigurera externa anslutningar till behållar registret och ditt Docker Swarm-kluster i Azure. 

1. I **tjänste** inställningarna för ditt Azure DevOps Services-projekt lägger du till en tjänst slut punkt av typen **Docker-register**. 

1. I popup-fönstret som öppnas anger du webb adressen och autentiseringsuppgifterna för ditt Azure Container Registry.

    ![Azure DevOps Services – Docker-register](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Lägg till en slut punkt av typen **SSH**för Docker Swarm-klustret. Ange information om SSH-anslutningen för ditt Swarm-kluster.

    ![Azure DevOps Services – SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

All konfiguration görs nu. I nästa steg skapar du CI/CD-pipeline som bygger och distribuerar programmet till Docker Swarm-klustret. 

## <a name="step-2-create-the-build-pipeline"></a>Steg 2: skapa en pipeline för bygge

I det här steget skapar du en pipeline för ditt Azure DevOps Services-projekt och definierar Bygg arbets flödet för behållar avbildningarna

### <a name="initial-pipeline-setup"></a>Installation av första pipelinen

1. Om du vill skapa en pipeline för bygge ansluter du till ditt Azure DevOps Services-projekt och klickar på **build & release**. 

1. Klicka på **+ ny**i avsnittet **Bygg definitioner** . Välj den **tomma** mallen.

    ![Azure DevOps – ny versions pipeline](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Konfigurera den nya versionen med en GitHub databas källa, kontrol lera **kontinuerlig integrering**och välj den agent kön där du registrerade Linux-agenten. Klicka på **skapa** för att skapa en pipeline för bygge.

    ![Azure DevOps Services – Skapa en pipeline för bygge](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. På sidan **Bygg definitioner** öppnar du först fliken **databas** och konfigurerar versionen så att den använder den förgrening som du skapade i kraven. Se till att du väljer *ACS-dok* som **standard gren**.

    ![Azure DevOps Services – konfiguration av databas för skapande](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. På fliken **utlösare** konfigurerar du att build ska utlösas efter varje genomförande. Välj **kontinuerlig integrering** och **batch-ändringar**.

    ![Azure DevOps Services – Bygg utlösarens konfiguration](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definiera Bygg arbets flödet
I nästa steg definieras Bygg arbets flödet. Det finns fem behållar avbildningar som kan byggas för ditt *Shop* -program. Varje avbildning skapas med hjälp av Dockerfile som finns i projektfilerna:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Du måste lägga till två Docker-steg för varje avbildning, en för att bygga avbildningen och en för att skicka avbildningen i Azure Container Registry. 

1. Lägg till ett steg i bygg arbets flödet genom att klicka på **+ Lägg till build-steget** och välja **Docker**.

    ![Azure DevOps Services – Lägg till bygg steg](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Konfigurera ett steg som använder kommandot för varje bild `docker build` .

    ![Azure DevOps Services – Docker-version](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    För build-åtgärden väljer du Azure Container Registry, åtgärden för att **bygga en avbildning** och den Dockerfile som definierar varje avbildning. Ange **Bygg kontexten** som rot katalogen för Dockerfile och definiera **avbildnings namnet**. 
    
    Som du ser på föregående skärm, startar du avbildnings namnet med URI: n för Azure Container Registry. (Du kan också använda en build-variabel för att Parameterisera tag gen, till exempel build-identifieraren i det här exemplet.)

1. Konfigurera ett andra steg som använder kommandot för varje bild `docker push` .

    ![Azure DevOps Services – Docker push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    För push-åtgärden väljer du ditt Azure Container Registry, åtgärden **push a image** och anger **avbildnings namnet** som har skapats i föregående steg.

1. När du har konfigurerat build-och push-stegen för var och en av de fem avbildningarna lägger du till två steg i bygg arbets flödet.

    a. En kommando rads uppgift som använder ett bash-skript för att ersätta *BuildNumber* -förekomsten i filen filen Docker. yml med det aktuella versions-ID: t. Se följande skärm för mer information.

    ![Azure DevOps Services – uppdatering av Skriv fil](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. En uppgift som släpper den uppdaterade Skriv filen som en Bygg artefakt så att den kan användas i versionen. Se följande skärm för mer information.

    ![Azure DevOps Services – publicera skapa fil](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Klicka på **Spara** och namnge din Bygg-pipeline.

## <a name="step-3-create-the-release-pipeline"></a>Steg 3: skapa en version av pipelinen

Med Azure DevOps Services kan du [hantera versioner i olika miljöer](https://azure.microsoft.com/services/devops/pipelines/). Du kan aktivera kontinuerlig distribution för att se till att ditt program har distribuerats i dina olika miljöer (till exempel utveckling, testning, för produktion och produktion) på ett smidigt sätt. Du kan skapa en ny miljö som representerar ditt Azure Container Service Docker Swarm-kluster.

![Azure DevOps Services – version till ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Installation av inledande version

1. Om du vill skapa en versions pipeline klickar du på **releases**  >  **+ release**

1. Konfigurera artefakt källan genom att klicka på **artefakter**  >  **Länka en artefakt källa**. Här länkar du den nya versions pipelinen till den version som du definierade i föregående steg. Detta gör att filen filen Docker. yml är tillgänglig i versions processen.

    ![Azure DevOps Services – versions artefakter](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Om du vill konfigurera versions utlösaren klickar du på **utlösare** och väljer **kontinuerlig distribution**. Ange utlösaren på samma artefakt källa. Den här inställningen ser till att en ny version startar så snart versionen har slutförts.

    ![Azure DevOps Services – versions utlösare](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definiera arbets flödet för lansering

Versions arbets flödet består av två uppgifter som du lägger till.

1. Konfigurera en aktivitet för att på ett säkert sätt kopiera Skriv filen till en *Deploy* -mapp på den Docker Swarm-huvudnoden med hjälp av ssh-anslutningen som du konfigurerade tidigare. Se följande skärm för mer information.

    ![Azure DevOps Services – version SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Konfigurera en andra aktivitet för att köra ett bash-kommando för att köra `docker` och `docker-compose` kommandon på huvud-noden. Se följande skärm för mer information.

    ![Azure DevOps Services – version bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Kommandot som körs på huvud servern använder Docker CLI och Docker-CLI för att utföra följande uppgifter:

   - Logga in på Azure Container Registry (det använder tre Bygg variabler som definieras på fliken **variabler** )
   - Definiera variabeln **DOCKER_HOST** för att arbeta med Swarm-slutpunkten (: 2375)
   - Navigera till mappen *Deploy* som skapades av föregående säkerhets kopierings aktivitet och som innehåller filen filen Docker. yml 
   - Kör `docker-compose` kommandon som hämtar de nya avbildningarna, stoppar tjänsterna, tar bort tjänsterna och skapar behållarna.

     >[!IMPORTANT]
     > Låt kryss rutan **misslyckad stderr** vara omarkerad, som du ser på föregående skärm. Detta är en viktig inställning, eftersom `docker-compose` skriver ut flera diagnostiska meddelanden, t. ex. att behållare stoppas eller tas bort, på standard fel utdata. Om du markerar kryss rutan rapporterar Azure DevOps Services fel som uppstått under versionen, även om alla går bra.
     >
1. Spara den nya versions pipelinen.


>[!NOTE]
>Den här distributionen innehåller vissa avbrott eftersom vi stoppar de gamla tjänsterna och kör den nya. Du kan undvika detta genom att göra en blå-grön-distribution.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Steg 4. Testa CI/CD-pipeline

Nu när du är färdig med konfigurationen är det dags att testa den nya CI/CD-pipelinen. Det enklaste sättet att testa det är att uppdatera käll koden och spara ändringarna i GitHub-lagringsplatsen. Några sekunder efter att du har push-överfört koden ser du en ny version som körs i Azure DevOps Services. När den har slutförts kommer en ny version att utlösas och distribuerar den nya versionen av programmet på Azure Container Service klustret.

## <a name="next-steps"></a>Efterföljande moment

* Mer information om CI/CD med Azure DevOps Services finns i artikeln om [pipeline-dokumentation för Azure](/azure/devops/pipelines/?view=azure-devops) .
