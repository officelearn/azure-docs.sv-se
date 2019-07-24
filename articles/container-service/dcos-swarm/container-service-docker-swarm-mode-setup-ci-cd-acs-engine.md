---
title: FÖRÅLDRAD CI/CD med Azure Container Service motor och Swarm läge
description: Använd Azure Container Service motor med Docker Swarm-läge, en Azure Container Registry och Azure DevOps för att leverera kontinuerligt ett .NET Core-program med flera behållare
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: fd502a308d6298dc2941461632a2832ac336c45c
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849873"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>FÖRÅLDRAD Full CI/CD-pipeline för att distribuera ett program med flera behållare på Azure Container Service med ACS-motorn och Docker Swarm-läge med Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Den här artikeln baseras på [en komplett CI/CD-pipeline för att distribuera ett program med flera behållare på Azure Container Service med Docker Swarm med hjälp av Azure DevOps](container-service-docker-swarm-setup-ci-cd.md) -dokumentationen*

Nuförtiden, en av de största utmaningarna när man utvecklar moderna program för molnet, kan leverera dessa program kontinuerligt. I den här artikeln får du lära dig hur du implementerar en fullständig kontinuerlig integrering och distribution (CI/CD)-pipeline med: 
* Azure Container Service motor med Docker Swarm läge
* Azure Container Registry
* Azure DevOps

Den här artikeln baseras på ett enkelt program som är tillgängligt på [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)och som har utvecklats med ASP.net Core. Programmet består av fyra olika tjänster: tre webb-API: er och en front webb:

![Exempel program för köp](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Målet är att leverera det här programmet kontinuerligt i ett Docker Swarm läges kluster med Azure DevOps. Följande bild innehåller information om den här kontinuerliga leverans pipelinen:

![Exempel program för köp](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Här är en kort beskrivning av stegen:

1. Kod ändringar allokeras till käll kod lagrings platsen (här, GitHub) 
2. GitHub utlöser en version i Azure DevOps 
3. Azure DevOps hämtar den senaste versionen av källorna och skapar alla avbildningar som skapar programmet 
4. Azure DevOps push-överför varje avbildning till ett Docker-register som skapats med tjänsten Azure Container Registry 
5. Azure DevOps utlöser en ny version 
6. Versionen kör vissa kommandon med SSH på Azure Container Service Cluster Master-noden 
7. Docker Swarm läge i klustret hämtar den senaste versionen av avbildningarna 
8. Den nya versionen av programmet distribueras med Docker-stack 

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien måste du utföra följande uppgifter:

- [Skapa ett Swarm läges kluster i Azure Container Service med ACS-motorn](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Anslut till Swarm-klustret i Azure Container Service](../container-service-connect.md)
- [Skapa ett Azure Container Registry](../../container-registry/container-registry-get-started-portal.md)
- [Ha en Azure DevOps-organisation och projekt skapat](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Förgrena GitHub-lagringsplatsen till ditt GitHub-konto](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Docker Swarm Orchestrator i Azure Container Service använder äldre fristående versionen av Swarm. Det integrerade [Swarm-läget](https://docs.docker.com/engine/swarm/) (i Docker 1.12 och senare) är inte en orchestrator som stöds i Azure Container Service. Därför använder vi [ACS-motorn](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), en community-baserad [snabb starts mall](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)eller en Docker-lösning på [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Steg 1: Konfigurera din Azure DevOps-organisation 

I det här avsnittet konfigurerar du din Azure DevOps-organisation. Om du vill konfigurera Azure DevOps Services-slutpunkter klickar du på ikonen **Inställningar** i verktygsfältet i ditt Azure DevOps-projekt och väljer **tjänster**.

![Open Services-slutpunkt](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Anslut Azure-DevOps och Azure-konto

Konfigurera en anslutning mellan ditt Azure DevOps-projekt och ditt Azure-konto.

1. Klicka på **ny tjänst slut punkt** > **Azure Resource Manager**till vänster.
2. För att godkänna att Azure-DevOps fungerar med ditt Azure-konto väljer du din **prenumeration** och klickar på **OK**.

    ![Azure-DevOps – auktorisera Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Anslut Azure DevOps och GitHub

Konfigurera en anslutning mellan ditt Azure DevOps-projekt och ditt GitHub-konto.

1. Klicka på **ny tjänst slut punkt** > **GitHub**till vänster.
2. För att godkänna att Azure-DevOps fungerar med ditt GitHub-konto klickar du på **auktorisera** och följer proceduren i fönstret som öppnas.

    ![Azure-DevOps – auktorisera GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Anslut Azure-DevOps till ditt Azure Container Service-kluster

De senaste stegen innan du kom in i CI/CD-pipeline är att konfigurera externa anslutningar till ditt Docker Swarm-kluster i Azure. 

1. Lägg till en slut punkt av typen **SSH**för Docker Swarm-klustret. Ange sedan SSH-anslutningssträngen för ditt Swarm-kluster (huvud nod).

    ![Azure-DevOps – SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

All konfiguration görs nu. I nästa steg skapar du CI/CD-pipeline som bygger och distribuerar programmet till Docker Swarm-klustret. 

## <a name="step-2-create-the-build-pipeline"></a>Steg 2: Skapa pipeline för bygge

I det här steget skapar du en pipeline för bygge för ditt Azure DevOps-projekt och definierar Bygg arbets flödet för behållar avbildningarna

### <a name="initial-pipeline-setup"></a>Installation av första pipelinen

1. Om du vill skapa en pipeline för bygge ansluter du till ditt Azure DevOps-projekt och klickar på **build & release**. Klicka på **+ ny**i avsnittet **Bygg definitioner** . 

    ![Azure DevOps – ny versions pipeline](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Välj den **tomma processen**.

    ![Azure-DevOps – ny tom versions pipeline](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Klicka sedan på fliken **variabler** och skapa två nya variabler: **RegistryURL** och **AgentURL**. Klistra in värdena för registret och kluster agenterna DNS.

    ![Konfiguration av variabler för Azure DevOps-build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. På sidan **versions definitioner** öppnar du fliken  utlösare och konfigurerar versionen för att använda kontinuerlig integrering med den förgrening som du skapade i kraven. Välj sedan **batch-ändringar**. Se till att du väljer *Docker-Linux* som **gren specifikation**.

    ![Azure-DevOps – konfiguration av databas för skapande](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Klicka slutligen på fliken **alternativ** och konfigurera standard agent kön till för **hands versionen av värdbaserad Linux**.

    ![Konfiguration av Azure DevOps-värd agent](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definiera Bygg arbets flödet
I nästa steg definieras Bygg arbets flödet. Först måste du konfigurera kodens källa. Om du vill göra det väljer du **GitHub** och ditt **lager** och din **gren** (Docker-Linux).

![Azure-DevOps – konfigurera kod källa](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Det finns fem behållar avbildningar som kan byggas för ditt *Shop* -program. Varje avbildning skapas med hjälp av Dockerfile som finns i projektfilerna:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Du behöver två Docker-steg för varje avbildning, en för att bygga avbildningen och en för att skicka avbildningen i Azure Container Registry. 

1. Lägg till ett steg i bygg arbets flödet genom att klicka på **+ Lägg till build-steget** och välja **Docker**.

    ![Azure-DevOps – Lägg till bygg steg](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Konfigurera ett steg som använder `docker build` kommandot för varje bild.

    ![Azure DevOps – Docker-version](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    För build-åtgärden väljer du Azure Container Registry, åtgärden för att **bygga en avbildning** och Dockerfile som definierar varje bild. Ange **arbets katalogen** som rot katalogen för Dockerfile, definiera avbildnings **namnet**och välj **ta med den senaste taggen**.
    
    Avbildningens namn måste ha formatet: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Ersätt **[Name]** med avbildnings namnet:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Konfigurera ett andra steg som använder `docker push` kommandot för varje bild.

    ![Azure DevOps – Docker push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    För push-åtgärden väljer du ditt Azure Container Registry, åtgärden **push a image** , anger avbildnings **namnet** som har skapats i föregående steg och väljer inkludera den **senaste taggen**.

4. När du har konfigurerat build-och push-stegen för var och en av de fem avbildningarna lägger du till tre steg i bygg arbets flödet.

   ![Azure-DevOps – Lägg till kommando rads uppgift](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. En kommando rads uppgift som använder ett bash-skript för att ersätta *RegistryURL* -förekomsten i filen filen Docker. yml med RegistryURL-variabeln. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure-DevOps – uppdatera Skriv filen med register webb adressen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. En kommando rads uppgift som använder ett bash-skript för att ersätta *AgentURL* -förekomsten i filen filen Docker. yml med AgentURL-variabeln.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. En uppgift som släpper den uppdaterade Skriv filen som en Bygg artefakt så att den kan användas i versionen. Se följande skärm för mer information.

      ![Azure-DevOps – publicera artefakt](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure-DevOps – publicera Skriv filen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Klicka på **spara & kö** för att testa din Bygg-pipeline.

   ![Azure-DevOps – Spara och köa](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure-DevOps – ny kö](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Om **versionen** är korrekt måste du se den här skärmen:

   ![Azure-DevOps – build lyckades](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Steg 3: Skapa lanserings pipelinen

Med Azure-DevOps kan du [hantera versioner i olika miljöer](https://www.visualstudio.com/team-services/release-management/). Du kan aktivera kontinuerlig distribution för att se till att ditt program har distribuerats i dina olika miljöer (till exempel utveckling, testning, för produktion och produktion) på ett smidigt sätt. Du kan skapa en miljö som representerar ditt Azure Container Service Docker Swarm läges kluster.

![Azure DevOps – version till ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Installation av inledande version

1. Om du vill skapa en versions pipeline  > klickar du på releases **+ release**

2. Konfigurera artefakt källan genom att klicka på **artefakter** > **Länka en artefakt källa**. Här länkar du den nya versions pipelinen till den version som du definierade i föregående steg. Därefter är filen filen Docker. yml tillgänglig i versions processen.

    ![Azure DevOps – versions artefakter](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Om du vill konfigurera versions utlösaren klickar du på utlösare och väljer **kontinuerlig distribution**. Ange utlösaren på samma artefakt källa. Den här inställningen säkerställer att en ny version startar när skapandet är klar.

    ![Azure-DevOps – versions utlösare](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Om du vill konfigurera reversions-variablerna klickar du på **variabler** och väljer **+ variabel** för att skapa tre nya variabler med informationen i registret: **Docker. användar namn**, **Docker. password**och **Docker. Registry**. Klistra in värdena för registret och kluster agenterna DNS.

    ![Azure-DevOps – konfiguration av databas för skapande](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Som du ser på föregående skärm, klickar du på kryss rutan **Lås** i Docker. password. Den här inställningen är viktig för att begränsa lösen ordet.
    >

### <a name="define-the-release-workflow"></a>Definiera arbets flödet för lansering

Versions arbets flödet består av två uppgifter som du lägger till.

1. Konfigurera en aktivitet för att på ett säkert sätt kopiera Skriv filen till en *Deploy* -mapp på den Docker Swarm-huvudnoden med hjälp av ssh-anslutningen som du konfigurerade tidigare. Se följande skärm för mer information.
    
    Källmapp:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure-DevOps – version SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Konfigurera en andra aktivitet för att köra ett bash-kommando `docker` för `docker stack deploy` att köra och kommandon på huvud-noden. Se följande skärm för mer information.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps-release bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Kommandot som körs på huvud servern använder Docker CLI och Docker-CLI för att utföra följande uppgifter:

   - Logga in på Azure Container Registry (det använder tre Bygg variabler som definieras på fliken **variabler** )
   - Definiera variabeln **DOCKER_HOST** så att den fungerar med Swarm-slutpunkten (: 2375)
   - Navigera till mappen *Deploy* som skapades av föregående säkerhets kopierings aktivitet och som innehåller filen filen Docker. yml 
   - Kör `docker stack deploy` kommandon som hämtar de nya avbildningarna och skapar behållarna.

     >[!IMPORTANT]
     > Som du ser på föregående skärm, lämnar kryss rutan **misslyckad stderr** omarkerad. Med den här inställningen kan vi slutföra publicerings processen på `docker-compose` grund av att flera diagnostiska meddelanden skrivs ut, till exempel att behållare stoppas eller tas bort, på standard fel utdata. Om du markerar kryss rutan kan Azure DevOps rapportera fel som uppstått under versionen, även om alla går bra.
     >
3. Spara den nya versions pipelinen.

## <a name="step-4-test-the-cicd-pipeline"></a>Steg 4: Testa CI/CD-pipeline

Nu när du är färdig med konfigurationen är det dags att testa den nya CI/CD-pipelinen. Det enklaste sättet att testa det är att uppdatera käll koden och spara ändringarna i GitHub-lagringsplatsen. Några sekunder efter att du har push-överfört koden ser du en ny version som körs i Azure DevOps. När åtgärden har slutförts utlöses en ny version och distribueras till den nya versionen av programmet på Azure Container Service klustret.

## <a name="next-steps"></a>Nästa steg

* Mer information om CI/CD med Azure DevOps finns i [Översikt över Azure DevOps-version](https://www.visualstudio.com/docs/build/overview).
* Mer information om ACS-motorn finns i [ACS-motorn GitHub lagrings platsen](https://github.com/Azure/acs-engine).
* Mer information om Docker Swarm läge finns i [Översikt över Docker Swarm läge](https://docs.docker.com/engine/swarm/).
