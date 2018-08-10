---
title: CI/CD med Azure Container Service och Swarm
description: Använda Azure Container Service med Docker Swarm, ett Azure Container Registry och Visual Studio Team Services för att kontinuerligt leverera en .NET Core-program med flera behållare
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: ac3133ac093d578c89d24bddd1cc0a7c9588c2fd
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715006"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Fullständig CI/CD-pipeline för att distribuera program med flera behållare på Azure Container Service med Docker Swarm med Visual Studio Team Services

En av de största utmaningarna när du utvecklar moderna program för molnet att kunna leverera dessa program kontinuerligt. I den här artikeln får du lära dig hur du implementerar en fullständig kontinuerlig integrering och distribution (CI/CD)-pipeline med hjälp av Azure Container Service med Docker Swarm, Azure Container Registry och Visual Studio Team Services build och versionshantering.

Den här artikeln är baserad på ett enkelt program tillgängliga på [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), utvecklade med ASP.NET Core. Programmet består av fyra olika tjänster: tre webb-API: er och en frontwebb:

![MyShop exempelprogrammet](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Målet är att kontinuerligt leverera det här programmet i ett Docker Swarm-kluster med hjälp av Visual Studio Team Services. Följande bild beskrivs denna pipeline för kontinuerlig leverans:

![MyShop exempelprogrammet](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Här är en kort beskrivning av stegen:

1. Ändringar i koden sparas i lagringsplatsen för källkod (här GitHub) 
1. GitHub utlöser en version i Visual Studio Team Services 
1. Visual Studio Team Services hämtar den senaste versionen av källorna och bygger alla avbildningar som utgör programmet 
1. Visual Studio Team Services skickar varje avbildning till ett Docker-register som skapats med hjälp av Azure Container Registry-tjänsten 
1. Visual Studio Team Services utlöser en ny version 
1. Versionen kör några kommandon med hjälp av SSH på Azure container service-kluster huvudnoden 
1. Docker Swarm på klustret hämtar den senaste versionen av avbildningar 
1. Den nya versionen av programmet distribueras med hjälp av Docker Compose 

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudiekursen måste du utföra följande uppgifter:

- [Skapa ett Swarm-kluster i Azure Container Service](container-service-deployment.md)
- [Anslut till Swarm-klustret i Azure Container Service](../container-service-connect.md)
- [Skapa ett Azure container registry](../../container-registry/container-registry-get-started-portal.md)
- [Har ett Visual Studio Team Services-konto och team-projekt skapas](https://docs.microsoft.com/vsts/organizations/accounts/create-organization-msa-or-work-student)
- [Gå till GitHub-databasen på GitHub-konto](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Du behöver också en Ubuntu (14.04 eller 16.04)-dator med Docker installerat. Den här datorn används av Visual Studio Team Services under Skapa och släpp-processer. Ett sätt att skapa den här datorn är att använda avbildningen som är tillgängliga i den [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Steg 1: Konfigurera din Visual Studio Team Services-konto 

I det här avsnittet konfigurerar du ditt Visual Studio Team Services-konto.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Konfigurera en Visual Studio Team Services Linux skapandeagent

För att skapa Docker-avbildningar och skicka dessa avbildningar till ett Azure container registry från en version av Visual Studio Team Services, som du behöver registrera en Linux-agenten. Du har dessa installationsalternativ:

* [Distribuera en agent på Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Använda Docker för att köra VSTS-agent](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Installera Docker-integrering VSTS-tillägg

Microsoft tillhandahåller ett VSTS-tillägg för att arbeta med Docker i bygger och frigöra processer. Det här tillägget är tillgängliga i den [VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Klicka på **installera** att lägga till det här tillägget till ditt VSTS-konto:

![Installera Docker-integrering](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Du uppmanas att ansluta till ditt VSTS-konto med dina autentiseringsuppgifter. 

### <a name="connect-visual-studio-team-services-and-github"></a>Ansluta Visual Studio Team Services och GitHub

Konfigurera en anslutning mellan ditt VSTS-projekt och ditt GitHub-konto.

1. I Visual Studio Team Services-projektet klickar du på den **inställningar** ikonen i verktygsfältet och väljer **Services**.

    ![Visual Studio Team Services - extern anslutning](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Till vänster, klicka på **nya tjänstslutpunkt** > **GitHub**.

    ![Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. För att auktorisera VSTS att arbeta med ditt GitHub-konto, klickar du på **auktorisera** och följer du anvisningarna i fönstret som öppnas.

    ![Visual Studio Team Services - auktorisera GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Ansluta VSTS till din Azure-behållarregister och Azure Container Service-kluster

De sista stegen innan du hämtar i CI/CD-pipeline är att konfigurera externa anslutningar till behållarregistret och Docker Swarm-kluster i Azure. 

1. I den **Services** inställningarna för ditt Visual Studio Team Services-projekt, lägga till en slutpunkt av typen **Docker-register**. 

1. I popup-fönstret som öppnas anger du URL: en och autentiseringsuppgifterna för Azure container registry.

    ![Visual Studio Team Services - Docker-register](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Lägg till en slutpunkt av typen för Docker Swarm-kluster **SSH**. Ange information för SSH-anslutning för Swarm-klustret.

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

All konfiguration görs nu. I nästa steg ska skapa du CI/CD-pipeline som skapar och distribuerar programmet till Docker Swarm-klustret. 

## <a name="step-2-create-the-build-definition"></a>Steg 2: Skapa build-definition

I det här steget ska du konfigurera en build-definitionfor ditt VSTS-projekt och definiera build-arbetsflödet för dina behållaravbildningar

### <a name="initial-definition-setup"></a>Definitionen för den inledande installationen

1. Anslut till ditt projekt i Visual Studio Team Services för att skapa en byggesdefinition, och klicka på **Build & Release**. 

1. I den **Byggesdefinitioner** klickar du på **+ ny**. Välj den **tom** mall.

    ![Visual Studio Team Services - nya Build-Definition](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Konfigurera den nya versionen med en GitHub-lagringsplatsen källa, kontrollera **kontinuerlig integrering**, och välj agent kön där du registrerade din Linux-agenten. Klicka på **skapa** att skapa build-definition.

    ![Visual Studio Team Services - skapa Byggesdefinition](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. På den **skapa definitioner** först öppnar den **databasen** fliken och konfigurera versionen om du vill använda förgrening av MyShop projektet som du skapade i förutsättningarna. Kontrollera att du väljer *acs-docs* som den **standardgrenen**.

    ![Visual Studio Team Services - databasen Versionskonfiguration](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. På den **utlösare** konfigurerar build Utlöses efter varje genomförande. Välj **kontinuerlig integrering** och **Batch ändringar**.

    ![Visual Studio Team Services - utlösaren Versionskonfiguration](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definiera build-arbetsflödet
Nästa steg definierar build-arbetsflödet. Det finns fem behållaravbildningar för att skapa för den *MyShop* program. Varje avbildning har skapats med den Dockerfile som finns i projektet mappar:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Du måste lägga till två Docker steg för varje bild, en för att skapa avbildningen och en att överföra avbildningen i Azure container registry. 

1. Om du vill lägga till ett steg i build-arbetsflöde, klickar du på **+ Lägg till byggsteg** och välj **Docker**.

    ![Visual Studio Team Services - Lägg till Byggsteg](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Konfigurera ett steg som används för varje bild i `docker build` kommando.

    ![Visual Studio Team Services - Docker Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Build-åtgärden Välj i Azure container registry, den **skapa en avbildning** åtgärden och den Dockerfile som definierar varje avbildning. Ange den **Build kontext** Dockerfile rotkatalogen och definiera den **avbildningsnamn**. 
    
    Som du ser i den föregående skärmbilden kan du börja avbildningens namn med URI för Azure container registry. (Du kan också använda en build-variabel för att Parameterisera taggen för avbildningen, till exempel build-ID i det här exemplet.)

1. Konfigurera ett andra steg som används för varje bild i `docker push` kommando.

    ![Visual Studio Team Services - Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    För push-åtgärden väljer du din Azure-behållarregister, den **Push-överför avbildningen** åtgärd, och ange den **avbildningsnamn** som skapats i föregående steg.

1. När du har konfigurerat versions- och push-steg för var och en av de fem avbildningarna kan du lägga till två fler steg i build-arbetsflöde.

    a. En kommandoradsaktivitet som använder ett bash-skript för att ersätta den *BuildNumber* förekomsten i docker-compose.yml-fil med aktuellt skapa Id. Se följande skärm för information.

    ![Visual Studio Team Services - uppdateringen Compose-fil](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. En aktivitet som släpper den uppdaterade Compose-filen som en byggesartefakt så att den kan användas i versionen. Se följande skärm för information.

    ![Visual Studio Team Services - Publicera Skriv fil](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Klicka på **spara** och namnge din build-definition.

## <a name="step-3-create-the-release-definition"></a>Steg 3: Skapa versionsdefinitionen

Visual Studio Team Services kan du [hantera versioner i alla miljöer](https://www.visualstudio.com/team-services/release-management/). Du kan aktivera kontinuerlig distribution att se till att programmet har distribuerats på olika miljöer (utveckling, testning, Förproduktion och produktion) i enkelt. Du kan skapa en ny miljö som representerar din Azure Container Service Docker Swarm-kluster.

![Visual Studio Team Services - versionen till ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Första versionen installationen

1. Klicka för att skapa en versionsdefinition **versioner** > **+ Release**

1. Om du vill konfigurera artefakt källan, klickar du på **artefakter** > **länka en artefakt källa**. Här kan länka den här nya versionsdefinition till den version som du definierade i föregående steg. På så sätt finns filen docker-compose.yml i lanseringsprocessen.

    ![Visual Studio Team Services - versionen artefakter](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. För att konfigurera utlösaren versionen, klickar du på **utlösare** och välj **kontinuerlig distribution**. Ange utlösaren på samma käll-artefakt. Den här inställningen garanterar att en ny version startar när bygget har slutförts.

    ![Visual Studio Team Services - versionen utlösare](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definiera versionen arbetsflödet

Versionen arbetsflödet består av två saker som du lägger till.

1. Konfigurera en uppgift för att på ett säkert sätt kopiera compose-filen till en *distribuera* mapp på Docker Swarm huvudnoden, med hjälp av SSH-anslutningen som du tidigare konfigurerat. Se följande skärm för information.

    ![Visual Studio Team Services - versionen SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Konfigurera en till aktivitet för att köra ett bash-kommando för att köra `docker` och `docker-compose` kommandon på huvudnoden. Se följande skärm för information.

    ![Visual Studio Team Services - versionen Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Kommandot kördes på master användningen av Docker CLI och Docker-Compose-CLI för att utföra följande uppgifter:

    - Logga in på Azure container registry (den använder tre build variab'les som definieras i den **variabler** fliken)
    - Definiera den **DOCKER_HOST** variabel att arbeta med Swarm-slutpunkten (: 2375)
    - Navigera till den *distribuera* mapp som har skapats i den föregående aktiviteten säker kopia och som innehåller filen docker-compose.yml 
    - Köra `docker-compose` kommandon som hämtar de nya bilderna stoppa tjänsterna, ta bort tjänsterna och skapa behållare.

    >[!IMPORTANT]
    > Som du ser i den föregående skärmbilden kan lämna den **misslyckas på STDERR** kryssrutan avmarkerad. Detta är ett viktigt inställningen eftersom `docker-compose` skriver ut flera diagnostiska meddelanden som behållare är stoppas eller tas bort på standardfelutdata. Om du markerar kryssrutan rapporterar Visual Studio Team Services att fel uppstod vid lanseringen, även om allt går bra.
    >
1. Spara den här nya versionsdefinitionen.


>[!NOTE]
>Den här distributionen omfattar vissa avbrott eftersom vi stoppar gamla tjänsterna och köra den nya servern. Det går att undvika detta genom att göra en blå-grön distribution.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Steg 4. Testa CI/CD-pipeline

Nu när du är klar med konfigurationen är det dags att testa den här nya CI/CD-pipeline. Det enklaste sättet att testa det är att uppdatera källkoden och sedan spara ändringarna i din GitHub-lagringsplats. Några sekunder när du har överfört koden, visas en ny version som körs i Visual Studio Team Services. När du har slutförts, utlöses en ny version och distribuerar den nya versionen av programmet på Azure Container Service-kluster.

## <a name="next-steps"></a>Nästa steg

* Mer information om CI/CD med Visual Studio Team Services finns i den [VSTS skapa översikt](https://www.visualstudio.com/docs/build/overview).
