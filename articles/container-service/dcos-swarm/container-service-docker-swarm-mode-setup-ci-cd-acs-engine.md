---
title: (INAKTUELL) CI/CD med Azure Container Service Engine och Swarm-läge
description: Använda Azure Container Service Engine med Docker Swarm Mode, ett Azure Container Registry och Azure DevOps för att kontinuerligt leverera en .NET Core-program med flera behållare
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 8aa62e4ed65f8223071786ac165f8343cb6901d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60430669"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(INAKTUELL) Fullständig CI/CD-pipeline för att distribuera program med flera behållare på Azure Container Service med ACS-motor och Docker Swarm-läge med hjälp av Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Den här artikeln är baserad på [fullständig CI/CD-pipeline för att distribuera program med flera behållare på Azure Container Service med Docker Swarm med Azure DevOps](container-service-docker-swarm-setup-ci-cd.md) dokumentation*

Nuförtiden, håller en av de största utmaningarna när du utvecklar moderna program för molnet på att kunna leverera dessa program kontinuerligt. I den här artikeln får du lära dig hur du implementerar en fullständig kontinuerlig integrering och distribution (CI/CD) med: 
* Azure Container Service Engine med Docker Swarm-läge
* Azure Container Registry
* Azure DevOps

Den här artikeln är baserad på ett enkelt program tillgängliga på [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), utvecklade med ASP.NET Core. Programmet består av fyra olika tjänster: tre webb-API: er och en frontwebb:

![MyShop exempelprogrammet](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Målet är att kontinuerligt leverera det här programmet i ett Docker Swarm Mode-kluster med Azure DevOps. Följande bild beskrivs denna pipeline för kontinuerlig leverans:

![MyShop exempelprogrammet](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Här är en kort beskrivning av stegen:

1. Ändringar i koden sparas i lagringsplatsen för källkod (här GitHub) 
2. GitHub utlöser en version i Azure DevOps 
3. Azure DevOps hämtar den senaste versionen av källorna och bygger alla avbildningar som utgör programmet 
4. Azure DevOps skickar varje avbildning till ett Docker-register som skapats med hjälp av Azure Container Registry-tjänsten 
5. Azure DevOps utlöser en ny version 
6. Versionen kör några kommandon med hjälp av SSH på Azure container service-kluster huvudnoden 
7. Docker Swarm-läge på klustret hämtar den senaste versionen av avbildningar 
8. Den nya versionen av programmet distribueras med hjälp av Docker-Stack 

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du påbörjar den här självstudiekursen måste du utföra följande uppgifter:

- [Skapa ett Swarm Mode-kluster i Azure Container Service med ACS-motor](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Anslut till Swarm-klustret i Azure Container Service](../container-service-connect.md)
- [Skapa ett Azure container registry](../../container-registry/container-registry-get-started-portal.md)
- [Har ett Azure DevOps-organisation och projekt som har skapats](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Gå till GitHub-databasen på GitHub-konto](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Docker Swarm Orchestrator i Azure Container Service använder äldre fristående versionen av Swarm. Det integrerade [Swarm-läget](https://docs.docker.com/engine/swarm/) (i Docker 1.12 och senare) är inte en orchestrator som stöds i Azure Container Service. Därför använder vi [ACS Engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), en communityn har bidragit med [snabbstartsmall](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), eller en Docker-lösning i den [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Steg 1: Konfigurera din Azure DevOps-organisation 

I det här avsnittet konfigurerar du din Azure DevOps-organisation. För att konfigurera slutpunkter för Azure DevOps-tjänster, i ditt Azure DevOps-projekt, klickar du på den **inställningar** ikonen i verktygsfältet och väljer **Services**.

![Öppna Services-slutpunkt](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Anslut Azure DevOps och Azure-konto

Konfigurera en anslutning mellan ditt Azure DevOps-projekt och ditt Azure-konto.

1. Till vänster, klicka på **nya tjänstslutpunkt** > **Azure Resource Manager**.
2. För att auktorisera Azure DevOps att arbeta med ditt Azure-konto, Välj din **prenumeration** och klicka på **OK**.

    ![Azure DevOps - auktorisera Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Ansluta Azure DevOps- och GitHub

Konfigurera en anslutning mellan ditt Azure DevOps-projekt och ditt GitHub-konto.

1. Till vänster, klicka på **nya tjänstslutpunkt** > **GitHub**.
2. För att auktorisera Azure DevOps att arbeta med ditt GitHub-konto, klickar du på **auktorisera** och följer du anvisningarna i fönstret som öppnas.

    ![Azure DevOps - auktorisera GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Ansluta Azure DevOps till ditt Azure Container Service-kluster

De sista stegen innan du hämtar i CI/CD-pipeline är att konfigurera externa anslutningar till Docker Swarm-kluster i Azure. 

1. Lägg till en slutpunkt av typen för Docker Swarm-kluster **SSH**. Ange information för SSH-anslutning för Swarm-klustret (huvudnoden).

    ![Azure DevOps - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

All konfiguration görs nu. I nästa steg ska skapa du CI/CD-pipeline som skapar och distribuerar programmet till Docker Swarm-klustret. 

## <a name="step-2-create-the-build-pipeline"></a>Steg 2: Skapa build-pipeline

I det här steget ska du konfigurera en build-pipeline för ditt Azure DevOps-projekt och definiera build-arbetsflödet för dina behållaravbildningar

### <a name="initial-pipeline-setup"></a>Inledande pipeline-installationen

1. Om du vill skapa en build-pipeline, ansluta till ditt Azure DevOps-projekt och klicka på **Build & Release**. I den **Byggesdefinitioner** klickar du på **+ ny**. 

    ![Azure DevOps - nya skapa Pipeline](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Välj den **tom process**.

    ![Azure DevOps - ny tom skapa Pipeline](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Klicka sedan på den **variabler** fliken och skapa två nya variabler: **RegistryURL** och **AgentURL**. Klistra in värdena för ditt register och klustret agenter DNS.

    ![Azure DevOps - variabler Versionskonfiguration](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. På den **skapa definitioner** öppnar den **utlösare** fliken och konfigurera versionen om du vill använda kontinuerlig integrering med förgrening av MyShop projektet som du skapade i förutsättningarna. Välj **Batch ändringar**. Kontrollera att du väljer *docker-linux* som den **grenen specifikationen**.

    ![Azure DevOps - Versionskonfiguration för databasen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Klicka slutligen på den **alternativ** fliken och konfigurera standard-agenten kön att **finns Linux-förhandsvisningen**.

    ![Azure DevOps - Agentkonfiguration för värd](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definiera build-arbetsflödet
Nästa steg definierar build-arbetsflödet. Du måste först konfigurera källan för koden. Om du vill göra det väljer **GitHub** och din **databasen** och **gren** (docker-linux).

![Azure DevOps - konfigurera Kodkälla](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Det finns fem behållaravbildningar för att skapa för den *MyShop* program. Varje avbildning har skapats med den Dockerfile som finns i projektet mappar:

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Du behöver två Docker steg för varje bild, en för att skapa avbildningen och en att överföra avbildningen i Azure container registry. 

1. Om du vill lägga till ett steg i build-arbetsflöde, klickar du på **+ Lägg till byggsteg** och välj **Docker**.

    ![Azure DevOps - Lägg till Byggsteg](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Konfigurera ett steg som används för varje bild i `docker build` kommando.

    ![Skapa Azure DevOps - Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Välj ditt Azure-Behållarregister för build-åtgärden i **skapa en avbildning** åtgärden och den Dockerfile som definierar varje avbildning. Ange den **Working Directory** som rotkatalogen Dockerfile som definierar den **avbildningsnamn**, och välj **inkludera senaste taggen**.
    
    Avbildningens namn måste vara i följande format: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Ersätt **[NAME]** med avbildningens namn:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Konfigurera ett andra steg som används för varje bild i `docker push` kommando.

    ![Azure DevOps - Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    För push-åtgärden väljer du din Azure-behållarregister, den **Push-överför avbildningen** åtgärd, ange den **avbildningsnamn** som skapats i föregående steg och välj **inkludera senaste taggen**.

4. När du har konfigurerat versions- och push-steg för var och en av de fem avbildningarna kan du lägga till tre fler steg i build-arbetsflöde.

   ![Azure DevOps - Lägg till Kommandoradsaktivitet](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. En kommandoradsaktivitet som använder ett bash-skript för att ersätta den *RegistryURL* förekomsten i filen docker-compose.yml med variabeln RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps - uppdatering Compose-fil med Registry URL](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. En kommandoradsaktivitet som använder ett bash-skript för att ersätta den *AgentURL* förekomsten i filen docker-compose.yml med variabeln AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. En aktivitet som släpper den uppdaterade Compose-filen som en byggesartefakt så att den kan användas i versionen. Se följande skärm för information.

      ![Azure DevOps - publicera artefakter](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Publicera Azure DevOps - Compose-fil](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Klicka på **spara och köa** att testa din build-pipeline.

   ![Azure DevOps - spara och kö](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps - ny kö](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Om den **skapa** är korrekt, du behöver se den här skärmen:

   ![Azure DevOps - genereringen lyckades](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Steg 3: Skapa versionspipelinen

Azure DevOps kan du [hantera versioner i alla miljöer](https://www.visualstudio.com/team-services/release-management/). Du kan aktivera kontinuerlig distribution att se till att programmet har distribuerats på olika miljöer (utveckling, testning, Förproduktion och produktion) i enkelt. Du kan skapa en miljö som representerar ditt Azure Container Service Docker Swarm Mode-kluster.

![Azure DevOps - versionen till ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Första versionen installationen

1. Klicka för att skapa en releasepipeline **versioner** > **+ Release**

2. Om du vill konfigurera artefakt källan, klickar du på **artefakter** > **länka en artefakt källa**. Här kan länka den här nya releasepipeline till den version som du definierade i föregående steg. Efter det är tillgängligt i lanseringsprocessen filen docker-compose.yml.

    ![Azure DevOps - versionen artefakter](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. För att konfigurera utlösaren versionen, klickar du på **utlösare** och välj **kontinuerlig distribution**. Ange utlösaren på samma käll-artefakt. Den här inställningen garanterar att en ny version startar när bygget har slutförts.

    ![Azure DevOps - versionen utlösare](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Om du vill konfigurera variablerna versionen klickar du på **variabler** och välj **+ variabeln** att skapa tre nya variabler med information i registret: **docker.username**, **docker.password**, och **docker.registry**. Klistra in värdena för ditt register och klustret agenter DNS.

    ![Azure DevOps - Versionskonfiguration för databasen](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > På den föregående skärmen visas klickar du på den **Lås** kryssrutan i docker.password. Den här inställningen är viktigt att begränsa lösenordet.
    >

### <a name="define-the-release-workflow"></a>Definiera versionen arbetsflödet

Versionen arbetsflödet består av två saker som du lägger till.

1. Konfigurera en uppgift för att på ett säkert sätt kopiera compose-filen till en *distribuera* mapp på Docker Swarm huvudnoden, med hjälp av SSH-anslutningen som du tidigare konfigurerat. Se följande skärm för information.
    
    Källmapp: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps - Release SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Konfigurera en till aktivitet för att köra ett bash-kommando för att köra `docker` och `docker stack deploy` kommandon på huvudnoden. Se följande skärm för information.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps - versionen Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Kommandot körs i bakgrunden använder Docker CLI och Docker-Compose-CLI för att utföra följande uppgifter:

   - Logga in på Azure container registry (den använder tre build-variabler som definieras i den **variabler** fliken)
   - Definiera den **DOCKER_HOST** variabel att arbeta med Swarm-slutpunkten (: 2375)
   - Navigera till den *distribuera* mapp som har skapats i den föregående aktiviteten säker kopia och som innehåller filen docker-compose.yml 
   - Köra `docker stack deploy` kommandon som hämtar de nya bilderna och skapa behållarna.

     >[!IMPORTANT]
     > Som du ser på den föregående skärmen kan lämna den **misslyckas på STDERR** kryssrutan avmarkerad. Den här inställningen gör att vi kan slutföras på grund av att versionen `docker-compose` skriver ut flera diagnostiska meddelanden som behållare är stoppas eller tas bort på standardfelutdata. Om du markerar kryssrutan rapporterar Azure DevOps att fel uppstod vid lanseringen, även om allt går bra.
     >
3. Spara den här nya releasepipeline.

## <a name="step-4-test-the-cicd-pipeline"></a>Steg 4: Testa CI/CD-pipeline

Nu när du är klar med konfigurationen är det dags att testa den här nya CI/CD-pipeline. Det enklaste sättet att testa det är att uppdatera källkoden och sedan spara ändringarna i din GitHub-lagringsplats. Några sekunder när du har överfört koden, visas en ny version som körs i Azure DevOps. När du har slutförts, utlöses en ny version och distribueras den nya versionen av programmet på Azure Container Service-kluster.

## <a name="next-steps"></a>Nästa steg

* Läs mer om CI/CD med Azure DevOps, den [Azure DevOps Build översikt](https://www.visualstudio.com/docs/build/overview).
* Läs mer om ACS-motor, den [ACS Engine GitHub-lagringsplatsen](https://github.com/Azure/acs-engine).
* Mer information om Docker Swarm-läge finns i den [översikt över Docker Swarm-läge](https://docs.docker.com/engine/swarm/).
