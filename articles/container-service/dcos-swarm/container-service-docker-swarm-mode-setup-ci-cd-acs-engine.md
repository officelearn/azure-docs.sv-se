---
title: "CI/CD med Azure Container Service-motorn och Swarm läge | Microsoft Docs"
description: "Använda Azure Container Service-motorn med Docker Swarm-läge, ett Azure Container registret och Visual Studio Team Services för att leverera kontinuerligt ett program för flera behållare .NET Core"
services: container-service
documentationcenter: " "
author: diegomrtnzg
manager: esterdnb
tags: acs, azure-container-service, acs-engine
keywords: "Docker behållare Micro-tjänster, Swarm Azure, Visual Studio Team Services, DevOps, ACS-motorn"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 2c0e5fe4f60738fcc1aa67a78674e6f3c62e5628
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Fullständig CI/CD-pipelinen för att distribuera ett program för flera behållare på Azure Container Service med ACS-motorn och Docker Swarm-läge med hjälp av Visual Studio Team Services

*Den här artikeln är baserad på [fullständig CI/CD-pipelinen för att distribuera ett program för flera behållare på Azure Container Service med Docker Swarm med hjälp av Visual Studio Team Services](container-service-docker-swarm-setup-ci-cd.md) dokumentation*

Men i dag en av de största utmaningarna när utveckla moderna applikationer för molnet att kunna leverera dessa program kontinuerligt. I den här artikeln får du lära dig hur du implementerar en fullständig kontinuerlig integrering och distribution (CI/CD) pipeline med: 
* Azure Container Service-motorn med Docker Swarm-läge
* Azure Container Registry
* Visual Studio Team Services

Den här artikeln är baserad på ett enkelt program tillgängliga på [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), utvecklade med ASP.NET Core. Programmet består av fyra olika tjänster: tre webb-API: er och en frontwebb:

![MyShop exempelprogrammet](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Målet är att ge alltid det här programmet i ett läge för Docker Swarm-kluster med hjälp av Visual Studio Team Services. Följande bild beskrivs denna kontinuerlig leverans pipeline:

![MyShop exempelprogrammet](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Här följer en kort beskrivning av steg:

1. Koden ändringarna har bekräftats kod källdatabasen (här GitHub) 
2. GitHub utlöser en version i Visual Studio Team Services 
3. Visual Studio Team Services får den senaste versionen av källorna och skapar alla avbildningar som ska utgöra programmet 
4. Visual Studio Team Services skickar varje avbildning till en Docker-registret som skapats med Azure Container Registry-tjänsten 
5. Visual Studio Team Services utlöser en ny version 
6. Versionen kör vissa kommandon med hjälp av SSH på Azure container service master klusternod 
7. Docker Swarm-läge på klustret hämtar den senaste versionen av avbildningar 
8. Den nya versionen av programmet distribueras med Docker-stacken 

## <a name="prerequisites"></a>Krav

Innan du påbörjar de här självstudierna måste du utföra följande uppgifter:

- [Skapa ett läge för Swarm-kluster i Azure Container Service med ACS-motorn](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Anslut till Swarm-klustret i Azure Container Service](../container-service-connect.md)
- [Skapa en Azure-behållaren registernyckel](../../container-registry/container-registry-get-started-portal.md)
- [Har en Visual Studio Team Services-konto och team projekt](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Duplicera GitHub-lagringsplatsen till GitHub-konto](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Docker Swarm Orchestrator i Azure Container Service använder äldre fristående versionen av Swarm. Det integrerade [Swarm-läget](https://docs.docker.com/engine/swarm/) (i Docker 1.12 och senare) är inte en orchestrator som stöds i Azure Container Service. Därför kan vi använda [ACS-motorn](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), en community-bidragit [quickstart mallen](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), eller en Docker-lösning i den [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Steg 1: Konfigurera Visual Studio Team Services-konto 

I det här avsnittet kan du konfigurera Visual Studio Team Services-konto. Konfigurera VSTS Services-slutpunkter i ditt projekt i Visual Studio Team Services genom att klicka på den **inställningar** ikonen i verktygsfältet och välj **Services**.

![Öppna Services slutpunkt](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Ansluta till Visual Studio Team Services och Azure-konto

Upprätta en anslutning mellan projektet VSTS och ditt Azure-konto.

1. Klicka på vänster, **nya tjänstslutpunkten** > **Azure Resource Manager**.
2. För att godkänna VSTS att arbeta med din Azure-konto, Välj din **prenumeration** och på **OK**.

    ![Visual Studio Team Services - auktorisera Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Ansluta Visual Studio Team Services och GitHub

Upprätta en anslutning mellan projektet VSTS och GitHub-konto.

1. Klicka på vänster, **nya tjänstslutpunkten** > **GitHub**.
2. För att godkänna VSTS att arbeta med GitHub-konto, klickar du på **auktorisera** och följ proceduren i fönstret som öppnas.

    ![Visual Studio Team Services - auktorisera GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>Ansluta VSTS till ditt Azure Container Service-kluster

De sista stegen innan du hämtar till CI/CD-pipeline är att konfigurera externa anslutningar till Docker Swarm-kluster i Azure. 

1. Lägga till en slutpunkt av typen för Docker Swarm-kluster **SSH**. Ange SSH-anslutningsinformationen för Swarm-klustret (nod).

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

All konfiguration görs nu. I nästa steg ska skapa du CI/CD-pipeline som skapar och distribuerar programmet till Docker Swarm-kluster. 

## <a name="step-2-create-the-build-definition"></a>Steg 2: Skapa build-definition

I det här steget kan du ställa in en build-definition för projektet VSTS och definiera build-arbetsflödet för behållaren-bilder

### <a name="initial-definition-setup"></a>Inledande definition installationen

1. Om du vill skapa en build-definition, ansluta till ditt projekt i Visual Studio Team Services och klicka på **Skapa & släpper**. I den **skapa definitioner** klickar du på **+ ny**. 

    ![Visual Studio Team Services - nya skapa Definition](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Välj den **tom process**.

    ![Visual Studio Team Services - ny tom skapa Definition](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Klicka på **variabler** fliken och skapar två nya variabler: **RegistryURL** och **AgentURL**. Klistra in värden för registernyckeln och klustret agenter DNS.

    ![Visual Studio Team Services - variabler Versionskonfiguration](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. På den **skapa definitioner** öppna den **utlösare** och konfigurera versionen om du vill använda kontinuerlig integration med förgrening av MyShop projektet som du skapade i förutsättningarna. Markera **Batch ändringar**. Kontrollera att du väljer *docker-linux* som den **Förgrena specifikationen**.

    ![Visual Studio Team Services - databasen Versionskonfiguration](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Klicka slutligen på den **alternativ** och konfigurera standard agent köns **finns Linux Preview**.

    ![Visual Studio Team Services - agenten Värdkonfiguration](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definiera build-arbetsflöde
Nästa steg definiera build-arbetsflöde. Först måste du konfigurera källan för koden. Om du vill göra det väljer **GitHub** och **databasen** och **gren** (docker-linux).

![Konfigurera Visual Studio Team Services - Kodkälla](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Det finns fem behållaren avbildningar för att skapa för den *MyShop* program. Varje avbildning skapas med Dockerfile i projektet mappar:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Du behöver två Docker steg för varje bild, en för att skapa avbildningen och en push-avbildningen i registret för Azure-behållaren. 

1. Om du vill lägga till ett steg i build-arbetsflöde, klickar du på **+ Lägg till build steg** och välj **Docker**.

    ![Visual Studio Team Services – Lägg till Build steg](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. För varje bild och konfigurera ett steg som använder den `docker build` kommando.

    ![Visual Studio Team Services - Docker skapa](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Välj Azure-behållare registret, för build-åtgärden i **skapa en avbildning** åtgärd och Dockerfile som definierar varje avbildning. Ange den **Working Directory** som rotkatalog Dockerfile definiera den **avbildningsnamn**, och välj **inkludera senaste taggen**.
    
    Avbildningens namn måste vara i formatet: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Ersätt **[NAME]** med avbildningens namn:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Konfigurera ett andra steg som används för varje avbildning i `docker push` kommando.

    ![Visual Studio Team Services - Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Välj din Azure-behållaren i registret för push-åtgärden den **Push en bild** åtgärd, ange den **avbildningsnamn** som är inbyggd i föregående steg och välj **inkludera senaste taggen**.

4. När du har konfigurerat bygg- och push-stegen för var och en av fem avbildningar kan du lägga till tre fler steg i build-arbetsflöde.

   ![Visual Studio Team Services – Lägg till Kommandoradsaktivitet](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. En kommandoradsaktivitet som använder ett bash-skript för att ersätta den *RegistryURL* förekomsten i filen docker-compose.yml med variabeln RegistryURL. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services - uppdatering skriva filen med registret URL](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. En kommandoradsaktivitet som använder ett bash-skript för att ersätta den *AgentURL* förekomsten i filen docker-compose.yml med variabeln AgentURL.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. En uppgift som släpper den uppdaterade Skriv-filen som ett build-artefakt så den kan användas i versionen. Se följande skärm för mer information.

         ![Visual Studio Team Services - Publicera artefakt](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services - Publicera Skriv fil](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Klicka på **Spara & kö** att testa build-definition.

   ![Visual Studio Team-Services - spara och kön](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services - ny kö](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Om den **skapa** är korrekt, du behöver se den här skärmen:

  ![Visual Studio Team Services - genereringen lyckades](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>Steg 3: Skapa en definition för versionen

Visual Studio Team Services kan du [hantera versioner i miljöer](https://www.visualstudio.com/team-services/release-management/). Du kan aktivera kontinuerlig distribution att se till att programmet har distribuerats på dina olika miljöer (t.ex dev, testa, Förproduktion och produktion) i enkelt. Du kan skapa en miljö som representerar läge i Azure Container Service Docker Swarm-klustret.

![Visual Studio Team Services - versionen till ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Första utgåvan installationen

1. Klicka för att skapa en definition av versionen **versioner** > **+ släpper**

2. Om du vill konfigurera artefakt källan, klickar du på **artefakter** > **länka en artefakt källa**. Här kan länka den här nya versionen definitionen till build-versionen som du definierade i föregående steg. Efter att finns filen docker-compose.yml i release-processen.

    ![Visual Studio Team Services - versionen artefakter](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. För att konfigurera utlösaren versionen, klickar du på **utlösare** och välj **kontinuerlig distribution**. Ange utlösaren på samma artefakt källa. Den här inställningen garanterar att en ny version startar när versionen har slutförts.

    ![Visual Studio Team Services - versionen utlösare](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Om du vill konfigurera variablerna versionen klickar du på **variabler** och välj **+ variabeln** att skapa tre nya variabler med information i registret: **docker.username**, **docker.password**, och **docker.registry**. Klistra in värden för registernyckeln och klustret agenter DNS.

    ![Visual Studio Team Services - databasen Versionskonfiguration](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Som visas på föregående sida klickar du på den **Lås** kryssrutan i docker.password. Den här inställningen är viktigt att begränsa lösenordet.
    >

### <a name="define-the-release-workflow"></a>Definiera versionen arbetsflödet

Arbetsflödet versionen består av två aktiviteter som du lägger till.

1. Konfigurera en aktivitet för att på ett säkert sätt kopiera skriva filen till en *distribuera* mapp på Docker Swarm huvudnoden, med hjälp av SSH-anslutningen som du tidigare konfigurerat. Se följande skärm för mer information.
    
    Källmapp:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services - versionen SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Konfigurera en annan uppgift om du vill köra ett bash-kommando för att köra `docker` och `docker stack deploy` kommandon på huvudnoden. Se följande skärm för mer information.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services - versionen Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Kommandot kördes i bakgrunden använder Docker CLI och CLI Docker Compose för att utföra följande uppgifter:

    - Logga in på Azure-behållaren registret (den använder tre build-variabler som definieras i den **variabler** fliken)
    - Definiera den **DOCKER_HOST** variabeln för att arbeta med Swarm-slutpunkten (: 2375)
    - Navigera till den *distribuera* mapp som har skapats för den föregående aktiviteten säker kopia och som innehåller filen docker-compose.yml 
    - Köra `docker stack deploy` kommandon som hämtar nya bilder och skapa behållarna.

    >[!IMPORTANT]
    > Som det visas på skärmen tidigare, kan du lämna den **misslyckas på STDERR** kryssrutan avmarkerad. Den här inställningen gör att vi kan slutföras på grund av att versionen `docker-compose` flera diagnostiska meddelanden skrivs ut som behållare är stoppas eller tas bort på standardfel utdata. Om du markerar kryssrutan rapporterar Visual Studio Team Services att fel uppstod vid version, även om allt går bra.
    >
3. Spara den här nya versionen definitionen.

## <a name="step-4-test-the-cicd-pipeline"></a>Steg 4: Testa CI/CD-pipelinen

Nu när du är klar med konfigurationen, är det dags att testa den här nya CI/CD-pipeline. Det enklaste sättet att testa den är att uppdatera källkod och spara ändringarna i GitHub-lagringsplatsen. Några sekunder efter att du push-kod, ser du en ny version körs i Visual Studio Team Services. När slutförts, utlöses en ny version och distribuera den nya versionen av programmet på Azure Container Service-kluster.

## <a name="next-steps"></a>Nästa steg

* Mer information om CI/CD-skiva med Visual Studio Team Services finns i [VSTS skapa översikt](https://www.visualstudio.com/docs/build/overview).
* Mer information om ACS-motorn finns i [ACS-motorn GitHub-repo-](https://github.com/Azure/acs-engine).
* Mer information om Docker Swarm läge finns det [Docker Swarm översikt över](https://docs.docker.com/engine/swarm/).
