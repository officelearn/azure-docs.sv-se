---
title: Använda Deployment Center i Azure App Service
description: Distributionscenter i Azure DevOps förenklar konfigurationen av en robust Azure DevOps-pipeline för ditt program
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854640"
---
# <a name="deployment-center-launcher"></a>Distributionscenter starta

Distributionscenter i Azure DevOps förenklar konfigurationen upp av en robust DevOps-pipeline för ditt program. Som standard konfigurerar du en DevOps-pipeline för att distribuera dina programuppdateringar till kubernetes-klustret. Du kan utöka standard konfigurerade DevOps pipeline och lägga till de förbättrade funktionerna i DevOps - godkännanden innan du distribuerar, etablera ytterligare Azure-resurser, köra skript, uppgradera programmet eller även köra ytterligare valideringstester.

I den här kursen ska du:

> [!div class="checklist"]
> * Konfigurera en DevOps-pipeline för att distribuera dina programuppdateringar till klustret k8s
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Rensa resurserna

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få en kostnadsfri till [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

* Azure Kubernetes Service (AKS)-kluster

## <a name="create-aks-cluster"></a>Skapa AKS-kluster

1. Logga in på din [Azure-portalen](https://portal.azure.com/)

1. Välj den [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) -knappen på menyn i det övre högra hörnet i Azure Portal.

1. Kör följande kommandon för att skapa AKS-klustret.

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>Distribuera programuppdateringar till K8s kluster

1. Gå till ovanstående skapade resursgruppen.

1. Välj AKS-klustret och klicka under vänstra bladet inställningar på den **distributionscenter (förhandsversion)** . Klicka på **börjar**.

   ![settings](media/deployment-center-launcher/settings.png)

1. Välj plats för koden och klicka på **nästa**. Databaser som stöds för närvarande, **[Azure lagringsplatser](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** och **GitHub**. Du kan följa stegen nedan beroende på vad databasen.

    Azure-databaser är en uppsättning version kontrollverktyg som du kan använda för att hantera din kod. Om ditt programvaruprojekt är stort eller litet, är det en bra idé att använda versionskontroll så snart som möjligt.

    - **Azure-lagringsplatser**: Välj en databas från befintliga projekt och organisation.

        ![Azure-lagringsplatser](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Auktorisera och välj lagringsplatsen för ditt GitHub-konto.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Vi kommer att analysera lagringsplatsen och identifiera din Dockerfile. Om du vill uppdatera det, kan du redigera identifierade portnumret.

    ![Programinställningar](media/deployment-center-launcher/application-settings.png)

    Om databasen inte innehåller Dockerfile, visas ett meddelande att genomföra en. 

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Välj eller skapa ett befintligt Container Registry och klicka på **Slutför**. Pipelinen ska skapas automatiskt och en version i i kö [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Azure Pipelines är en molnbaserad tjänst som du kan använda för att automatiskt skapa och testa ditt Kodprojekt och gör den tillgänglig för andra användare. Azure Pipelines kombinerar kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för att hela tiden och konsekvent, testa och bygga din kod och skickar den till valfritt mål.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Klicka på länken för att se pågående pipelinen.

1. Lyckad loggarna visas som visas när distributionen är klar.

    ![Logs](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

Distributionscenter konfigurerar Azure DevOps organisationens CI / CD-pipeline automatiskt. Pipelinen kan utforskas och anpassas. 

1. Gå till instrumentpanelen för Deployment Center.  

1. Klicka på build-nummer i listan av lyckade loggar att visa build-pipelinen för ditt projekt. 

1. Klicka på ellipsis(...) från det övre högra hörnet. En meny visas flera alternativ som queuing en ny version, behålla build och redigerar build-pipeline. Välj den **redigera pipeline**. 

1. Du kan granska de olika uppgifterna för din version pipeline i det här fönstret. Versionen utför olika uppgifter, till exempel samla in källor från Git-lagringsplats, skapar en avbildning, push-överföra en avbildning till behållarregistret och publicera utdata som används för distributioner.

1. Välj namnet på build-pipelinen överst i build-pipeline.

1. Ändra namnet på din build pipeline till något mer beskrivande, Välj **spara och köa**, och välj sedan **spara**.

1. Välj **historik** under build-pipeline. Det här fönstret visar en översikt över de senaste build-ändringarna. Azure DevOps övervakar ändringar som görs till build-pipelinen och kan du jämföra versioner.

1. Välj **utlösare**. Du kan också kan grenar inkluderas eller uteslutas från CI-processen.

1. Välj **kvarhållning**. Du kan ange principer för att behålla eller ta bort ett antal versioner beroende på ditt scenario.

## <a name="examine-the-cd-pipeline"></a>Granska CD-pipelinen

Distributionscenter skapar och konfigurerar automatiskt de nödvändiga stegen från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar hur du konfigurerar en Azure-tjänst-anslutning för att autentisera din Azure-prenumeration med Azure DevOps. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure.

1. Välj **Pipelines**, och välj sedan **versioner**.

1. Om du vill redigera versionspipelinen, klickar du på **redigera** .

1. Välj **släppa** från **artefakter**. I föregående steg skapar du undersöks konstruktion pipelinen utdata som används för artefakten. 

1. Välj **kontinuerlig distribution** utlösare till höger om den **släppa** ikon. Den här versionen pipelinen har en aktiverad CD-utlösare som kör en distribution när en ny byggesartefakt är tillgänglig. Alternativt kan du inaktivera utlösaren för att kräva manuell körning för dina distributioner.

1. Om du vill kontrollera alla aktiviteter för din pipeline, klickar du på **uppgifter**. Versionen anger tiller-miljön, konfigurerar imagePullSecrets, installerar Helm verktyg och distribuerar Helm-diagram till K8s-kluster.

1. Om du vill visa historik för versioner, klickar du på **visa versioner**. 

1. För att visa sammanfattningen, klickar du på den **versionen**. Klicka på någon av scenen för att utforska flera menyer, till exempel en sammanfattning, version tillhörande arbetsobjekt och tester. 

1. Välj **Incheckningar**. Den här vyn visar kod incheckningar relaterade till den här distributionen. Jämför versioner om du vill se commit skillnaderna mellan distributioner.

1. Välj **Loggar**. Loggarna innehåller användbar distributionsmetod information. Under och efter distributionerna, kan du visa dem.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de relaterade resurserna som du skapade när du inte längre behövs. Använd Delete-funktioner på instrumentpanelen för DevOps-projekt.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera en DevOps-pipeline för att distribuera dina programuppdateringar till klustret k8s
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Rensa resurserna
