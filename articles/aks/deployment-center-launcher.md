---
title: Distributionscenter för Azure Kubernetes
description: Distributionscenter i Azure DevOps förenklar inrättandet av en robust Azure DevOps-pipeline för ditt program
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: b0a9597e370648faab3787218c7d038798dbd455
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048116"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Distributionscenter för Azure Kubernetes

Distributionscenter i Azure DevOps förenklar inrättandet av en robust Azure DevOps-pipeline för ditt program. Som standard konfigurerar Distribution Center en Azure DevOps-pipeline för att distribuera programuppdateringar till Kubernetes-klustret. Du kan utöka standardkonfigurationerade Azure DevOps-pipelinen och även lägga till rikare funktioner: möjligheten att få godkännande innan du distribuerar, etablerar ytterligare Azure-resurser, kör skript, uppgraderar ditt program och till och med kör fler valideringstester.

I den här kursen ska du:

> [!div class="checklist"]
> * Konfigurera en Azure DevOps-pipeline för att distribuera programuppdateringar till Kubernetes-klustret.
> * Undersök pipelinen för kontinuerlig integrering (CI).
> * Undersök den kontinuerliga leveranspipelinen (CD).
> * Rensa upp resurserna.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Ett AKS-kluster (Azure Kubernetes Service).

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

1. Logga in på din [Azure-portal](https://portal.azure.com/).

1. Välj alternativet [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) till höger i menyraden i Azure-portalen.

1. Om du vill skapa AKS-klustret kör du följande kommandon:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Distribuera programuppdateringar till ett Kubernetes-kluster

1. Gå till resursgruppen som du skapade i föregående avsnitt.

1. Välj AKS-klustret och välj sedan **Distributionscenter (förhandsgranskning)** på det vänstra bladet. Välj **Kom igång**.

   ![settings](media/deployment-center-launcher/settings.png)

1. Välj platsen för koden och välj **Nästa**. Välj sedan en av de databaser som stöds: **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** eller **GitHub**.

    Azure Repos är en uppsättning verktyg för versionskontroll som hjälper dig att hantera din kod. Om ditt programvaruprojekt är stort eller litet är det en bra idé att använda versionskontrollen så tidigt som möjligt.

    - **Azure Repos**: Välj en databas från ditt befintliga projekt och din befintliga organisation.

        ![Azure-lagringsplatser](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Auktorisera och välj databasen för ditt GitHub-konto.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Distributionscenter analyserar databasen och identifierar Din Dockerfile. Om du vill uppdatera Dockerfile kan du redigera det identifierade portnumret.

    ![Programinställningar](media/deployment-center-launcher/application-settings.png)

    Om databasen inte innehåller Dockerfile visas ett meddelande om att genomföra en.

    ![Dockerfile (dockerfile)](media/deployment-center-launcher/dockerfile.png)

1. Markera ett befintligt behållarregister eller skapa ett och välj sedan **Slutför**. Pipelinen skapas automatiskt och köar en version i [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Azure Pipelines är en molntjänst som du kan använda för att automatiskt skapa och testa ditt kodprojekt och göra det tillgängligt för andra användare. Azure Pipelines kombinerar kontinuerlig integrering och kontinuerlig leverans för att ständigt och konsekvent testa och skapa din kod och leverera den till alla mål.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Välj länken för att se den pågående pipelinen.

1. Du ser de lyckade loggarna när distributionen är klar.

    ![Loggar](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

Deployment Center konfigurerar automatiskt din Azure DevOps-organisations CI/CD-pipeline. Rörledningen kan utforskas och anpassas.

1. Gå till instrumentpanelen i Distributionscenter.  

1. Välj byggnumret i listan över lyckade loggar för att visa byggpipelinen för projektet.

1. Välj ellipsen (...) i det övre högra hörnet. En meny visar flera alternativ, till exempel att köa en ny version, behålla en version och redigera byggpipelinen. Välj **Redigera pipeline**. 

1. Du kan granska de olika aktiviteterna för byggpipeline i den här rutan. Build utför olika uppgifter, till exempel samla in källor från Git-databasen, skapa en avbildning, skicka en avbildning till behållarregistret och publicera utdata som används för distributioner.

1. Välj namnet på byggpipelinen högst upp i pipelinen.

1. Ändra byggpipelinenamnet till något mer beskrivande, välj **Spara & kö**och välj sedan **Spara**.

1. Under byggpipelinen väljer du **Historik**. I den här rutan visas en granskningsspårning för de senaste byggändringarna. Azure DevOps övervakar alla ändringar som görs i byggpipelinen och låter dig jämföra versioner.

1. Välj **Utlösare**. Du kan inkludera eller utesluta grenar från CI-processen.

1. Välj **Kvarhållning**. Du kan ange principer för att behålla eller ta bort ett antal versioner, beroende på ditt scenario.

## <a name="examine-the-cd-pipeline"></a>Granska CD-pipelinen

Deployment Center skapar och konfigurerar automatiskt relationen mellan din Azure DevOps-organisation och din Azure-prenumeration. Stegen är att konfigurera en Azure-tjänstanslutning för att autentisera din Azure-prenumeration med Azure DevOps. Den automatiserade processen skapar också en versionspipeline, som ger kontinuerlig leverans till Azure.

1. Välj **Pipelines**och välj sedan **Releases**.

1. Om du vill redigera versionspipelinen väljer du **Redigera**.

1. Välj **Släpp** i listan **Artefakter.** I föregående steg producerar den konstruktionspipeline som du undersökte utdata som används för artefakten. 

1. Välj **utlösaren För kontinuerlig distribution** till höger om alternativet **Släpp.** Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution när en ny versionsartefakt är tillgänglig. Du kan också inaktivera utlösaren för att kräva manuell körning för dina distributioner.

1. Om du vill granska alla aktiviteter för pipelinen väljer du **Uppgifter**. Versionen anger rorkultmiljön, konfigurerar parametern, `imagePullSecrets` installerar Helm-verktyg och distribuerar Helm-diagrammen till Kubernetes-klustret.

1. Om du vill visa versionshistoriken väljer du **Visa versioner**.

1. Om du vill visa sammanfattningen väljer du **Släpp**. Välj något av stegen för att utforska flera menyer, till exempel en versionssammanfattning, associerade arbetsobjekt och tester. 

1. Välj **Incheckningar**. I den här vyn visas kod commits relaterade till den här distributionen. Jämför versioner för att se commit-skillnaderna mellan distributioner.

1. Välj **Loggar**. Loggarna innehåller användbar distributionsinformation som du kan visa under och efter distributioner.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de relaterade resurser som du skapade när du inte behöver dem längre. Använd borttagningsfunktionen på instrumentpanelen DevOps Projects.

## <a name="next-steps"></a>Nästa steg

Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda den här CI/CD-modellen som mall för dina andra pipelines.
