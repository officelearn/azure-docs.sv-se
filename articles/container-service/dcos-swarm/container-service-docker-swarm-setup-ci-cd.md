---
title: CI/CD med Azure Container Service och Swarm
description: Använda Azure Container Service med Docker Swarm, ett Azure Container registret och Visual Studio Team Services för att leverera kontinuerligt ett program för flera behållare .NET Core
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 81a07fdfe1c862bc30fb9d567db9a393c0610990
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Fullständig CI/CD-pipelinen för att distribuera ett program för flera behållare på Azure Container Service med Docker Swarm med hjälp av Visual Studio Team Services

En av de största utmaningarna när utveckla moderna applikationer för molnet att kunna leverera dessa program kontinuerligt. Lär dig hur du implementerar en fullständig kontinuerlig integrering och distribution (CI/CD) pipeline med Docker Swarm Azure Container registret och Visual Studio Team Services build Azure Container Service och versionshantering i den här artikeln.

Den här artikeln är baserad på ett enkelt program tillgängliga på [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), utvecklade med ASP.NET Core. Programmet består av fyra olika tjänster: tre webb-API: er och en frontwebb:

![MyShop exempelprogrammet](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Målet är att ge alltid det här programmet i ett Docker Swarm-kluster med hjälp av Visual Studio Team Services. Följande bild beskrivs denna kontinuerlig leverans pipeline:

![MyShop exempelprogrammet](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Här följer en kort beskrivning av steg:

1. Koden ändringarna har bekräftats kod källdatabasen (här GitHub) 
2. GitHub utlöser en version i Visual Studio Team Services 
3. Visual Studio Team Services får den senaste versionen av källorna och skapar alla avbildningar som ska utgöra programmet 
4. Visual Studio Team Services skickar varje avbildning till en Docker-registret som skapats med Azure Container Registry-tjänsten 
5. Visual Studio Team Services utlöser en ny version 
6. Versionen kör vissa kommandon med hjälp av SSH på Azure container service master klusternod 
7. Docker Swarm på klustret hämtar den senaste versionen av avbildningar 
8. Den nya versionen av programmet distribueras med Docker Compose 

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar de här självstudierna måste du utföra följande uppgifter:

- [Skapa ett Swarm-kluster i Azure Container Service](container-service-deployment.md)
- [Anslut till Swarm-klustret i Azure Container Service](../container-service-connect.md)
- [Skapa en Azure-behållaren registernyckel](../../container-registry/container-registry-get-started-portal.md)
- [Har en Visual Studio Team Services-konto och team projekt](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Duplicera GitHub-lagringsplatsen till GitHub-konto](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Du måste också en Ubuntu (14.04 eller 16.04)-dator med Docker installerad. Den här datorn används av Visual Studio Team Services under version och utgåva. Ett sätt att skapa den här datorn är att använda avbildningen som är tillgängliga i den [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Steg 1: Konfigurera Visual Studio Team Services-konto 

I det här avsnittet kan du konfigurera Visual Studio Team Services-konto.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Konfigurera en Visual Studio Team Services Linux build-agent

För att skapa avbildningar av Docker och skicka dessa avbildningar till en Azure-behållaren registret från en version av Visual Studio Team Services, som du behöver registrera en Linux-agenten. Du har dessa installationsalternativ:

* [Distribuera en agent på Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Använda Docker för att köra VSTS-agent](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Installera tillägget Docker integrering VSTS

Microsoft tillhandahåller ett VSTS tillägg för att arbeta med Docker i version och utgåva processer. Det här tillägget är tillgängligt i den [VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Klicka på **installera** att lägga till det här tillägget i VSTS kontot:

![Installera Docker-integrering](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Du uppmanas att ansluta till ditt VSTS-konto med dina autentiseringsuppgifter. 

### <a name="connect-visual-studio-team-services-and-github"></a>Ansluta Visual Studio Team Services och GitHub

Upprätta en anslutning mellan projektet VSTS och GitHub-konto.

1. I ditt projekt i Visual Studio Team Services, klickar du på den **inställningar** ikonen i verktygsfältet och välj **Services**.

    ![Visual Studio Team Services - extern anslutning](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. Klicka på vänster, **nya tjänstslutpunkten** > **GitHub**.

    ![Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. För att godkänna VSTS att arbeta med GitHub-konto, klickar du på **auktorisera** och följ proceduren i fönstret som öppnas.

    ![Visual Studio Team Services - auktorisera GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Ansluta VSTS till ditt Azure-behållaren registret och Azure Container Service-kluster

De sista stegen innan du hämtar till CI/CD-pipeline är att konfigurera externa anslutningar till behållaren registret och Docker Swarm-kluster i Azure. 

1. I den **Services** inställningarna för ditt projekt i Visual Studio Team Services, lägga till en tjänstslutpunkt av typen **Docker registret**. 

2. I popup-fönstret som öppnas, anger du URL och autentiseringsuppgifter för Azure-behållaren registret.

    ![Visual Studio Team Services - Docker-registret](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Lägga till en slutpunkt av typen för Docker Swarm-kluster **SSH**. Ange SSH-anslutningsinformationen för Swarm-klustret.

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

All konfiguration görs nu. I nästa steg ska skapa du CI/CD-pipeline som skapar och distribuerar programmet till Docker Swarm-kluster. 

## <a name="step-2-create-the-build-definition"></a>Steg 2: Skapa build-definition

I det här steget du ställer in en build definitionfor VSTS-projektet och definiera build-arbetsflödet för bilderna behållare

### <a name="initial-definition-setup"></a>Inledande definition installationen

1. Om du vill skapa en build-definition, ansluta till ditt projekt i Visual Studio Team Services och klicka på **Skapa & släpper**. 

2. I den **skapa definitioner** klickar du på **+ ny**. Välj den **tom** mall.

    ![Visual Studio Team Services - nya skapa Definition](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. Konfigurera den nya versionen med en källa för GitHub-lagringsplatsen, kontrollera **kontinuerlig integration**, och välj agent kön där du har registrerat din Linux-agenten. Klicka på **skapa** att skapa build-definition.

    ![Visual Studio Team Services - skapa Build-Definition](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. På den **skapa definitioner** sidan, öppnar du först den **databasen** och konfigurera versionen om du vill använda förgrening av MyShop projektet som du skapade i förutsättningarna. Kontrollera att du väljer *acs-dokumenten* som den **standardförgrening**.

    ![Visual Studio Team Services - databasen Versionskonfiguration](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. På den **utlösare** konfigurerar build som utlöses efter varje genomförande. Välj **kontinuerlig integration** och **Batch ändringar**.

    ![Visual Studio Team Services - konfiguration av Build-utlösare](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definiera build-arbetsflöde
Nästa steg definiera build-arbetsflöde. Det finns fem behållaren avbildningar för att skapa för den *MyShop* program. Varje avbildning skapas med Dockerfile i projektet mappar:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Du måste lägga till två Docker steg för varje bild, en för att skapa avbildningen och en push-avbildningen i registret för Azure-behållaren. 

1. Om du vill lägga till ett steg i build-arbetsflöde, klickar du på **+ Lägg till build steg** och välj **Docker**.

    ![Visual Studio Team Services – Lägg till Build steg](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. För varje bild och konfigurera ett steg som använder den `docker build` kommando.

    ![Visual Studio Team Services - Docker skapa](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Välj din Azure-behållaren i registret för build-åtgärden i **skapa en avbildning** åtgärd och Dockerfile som definierar varje avbildning. Ange den **Build-kontexten** som Dockerfile rotkatalogen och definiera de **avbildningsnamn**. 
    
    Starta Avbildningsnamnet med URI för Azure-behållaren registret som visas i den föregående skärmbilden. (Du kan också använda en build-variabel till parameterstyra taggen bild, till exempel build-ID i det här exemplet.)

3. Konfigurera ett andra steg som används för varje avbildning i `docker push` kommando.

    ![Visual Studio Team Services - Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Välj din Azure-behållaren i registret för push-åtgärden i **Push en bild** åtgärd, och ange den **avbildningsnamn** som är inbyggd i föregående steg.

4. När du har konfigurerat bygg- och push-stegen för var och en av fem avbildningar kan du lägga till två fler steg i build-arbetsflöde.

    a. En kommandoradsaktivitet som använder ett bash-skript för att ersätta den *BuildNumber* förekomsten i filen docker-compose.yml med aktuellt skapa Id. Se följande skärm för mer information.

    ![Visual Studio Team Services - uppdatering Skriv fil](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. En uppgift som släpper den uppdaterade Skriv-filen som ett build-artefakt så den kan användas i versionen. Se följande skärm för mer information.

    ![Visual Studio Team Services - Publicera Skriv fil](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Klicka på **spara** och namnet build-definition.

## <a name="step-3-create-the-release-definition"></a>Steg 3: Skapa en definition för versionen

Visual Studio Team Services kan du [hantera versioner i miljöer](https://www.visualstudio.com/team-services/release-management/). Du kan aktivera kontinuerlig distribution att se till att programmet har distribuerats på dina olika miljöer (t.ex dev, testa, Förproduktion och produktion) i enkelt. Du kan skapa en ny miljö som representerar dina Azure Container Service Docker Swarm-kluster.

![Visual Studio Team Services - versionen till ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Första utgåvan installationen

1. Klicka för att skapa en definition av versionen **versioner** > **+ släpper**

2. Om du vill konfigurera artefakt källan, klickar du på **artefakter** > **länka en artefakt källa**. Här kan länka den här nya versionen definitionen till build-versionen som du definierade i föregående steg. På så sätt finns filen docker-compose.yml i release-processen.

    ![Visual Studio Team Services - versionen artefakter](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. För att konfigurera utlösaren versionen, klickar du på **utlösare** och välj **kontinuerlig distribution**. Ange utlösaren på samma artefakt källa. Den här inställningen garanterar att en ny version startar så fort versionen har slutförts.

    ![Visual Studio Team Services - versionen utlösare](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definiera versionen arbetsflödet

Arbetsflödet versionen består av två aktiviteter som du lägger till.

1. Konfigurera en aktivitet för att på ett säkert sätt kopiera skriva filen till en *distribuera* mapp på Docker Swarm huvudnoden, med hjälp av SSH-anslutningen som du tidigare konfigurerat. Se följande skärm för mer information.

    ![Visual Studio Team Services - versionen SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Konfigurera en annan uppgift om du vill köra ett bash-kommando för att köra `docker` och `docker-compose` kommandon på huvudnoden. Se följande skärm för mer information.

    ![Visual Studio Team Services - versionen Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Kommandot som kördes på master använda Docker CLI och Docker Compose CLI för att göra följande:

    - Logga in på Azure-behållaren registret (den använder tre build variab'les som definieras i den **variabler** fliken)
    - Definiera den **DOCKER_HOST** variabeln för att arbeta med Swarm-slutpunkten (: 2375)
    - Navigera till den *distribuera* mapp som har skapats för den föregående aktiviteten säker kopia och som innehåller filen docker-compose.yml 
    - Köra `docker-compose` kommandon som hämtar nya bilder stoppa tjänsterna, ta bort tjänsterna och skapa behållarna.

    >[!IMPORTANT]
    > Som det visas i den föregående skärmbilden, lämnar den **misslyckas på STDERR** kryssrutan avmarkerad. Detta är ett viktigt, eftersom `docker-compose` flera diagnostiska meddelanden skrivs ut som behållare är stoppas eller tas bort på standardfel utdata. Om du markerar kryssrutan rapporterar Visual Studio Team Services att fel uppstod vid version, även om allt går bra.
    >
3. Spara den här nya versionen definitionen.


>[!NOTE]
>Den här distributionen innehåller vissa driftstopp eftersom vi stoppar gamla tjänsterna och köra en ny. Det går att undvika detta genom att göra en blå-grön-distribution.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Steg 4. Testa CI/CD-pipelinen

Nu när du är klar med konfigurationen, är det dags att testa den här nya CI/CD-pipeline. Det enklaste sättet att testa den är att uppdatera källkod och spara ändringarna i GitHub-lagringsplatsen. Några sekunder efter att du push-kod, ser du en ny version körs i Visual Studio Team Services. När har slutförts ska utlösas när en ny version och distribuerar den nya versionen av programmet på Azure Container Service-kluster.

## <a name="next-steps"></a>Nästa steg

* Mer information om CI/CD-skiva med Visual Studio Team Services finns i [VSTS skapa översikt](https://www.visualstudio.com/docs/build/overview).
