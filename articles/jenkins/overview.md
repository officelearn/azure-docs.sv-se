---
title: Översikt över Jenkins och Azure
description: Använd Azure som värd för en Jenkins-automationsserver för versions- och distributionshantering och använd Azures beräknings- och lagringsresurser för att utöka dina kontinuerliga pipelines för integration och distribution (CI/CD).
ms.service: jenkins
keywords: jenkins, azure, devops, översikt
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: overview
ms.date: 07/25/2018
ms.openlocfilehash: 1f09e1711cbbbd8f05a982e620b1e09184320d13
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078974"
---
# <a name="azure-and-jenkins"></a>Azure och Jenkins

[Jenkins](https://jenkins.io/) är en populär automationsserver med öppen källkod för hantering av kontinuerlig integration och leverans (CI/CD) i programvaruprojekt. Du kan använda Azure som värd för din Jenkins-distribution eller utöka din befintliga Jenkins-konfiguration med hjälp av Azure-resurser. Du har också tillgång till Jenkins-plugin-program som underlättar den kontinuerliga integrationen och leveransen för dina program till Azure.

Den här artikeln är en introduktion till Azure med Jenkins och beskriver de viktigaste Azure-funktioner som är tillgängliga för Jenkins-användare. Mer information om hur du kommer igång med din egen Jenkins-server i Azure finns i [Skapa en Jenkins-server på Azure](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Använda Azure som värd för Jenkins-servrar

Med Azure som värd för dina Jenkins-servrar kan du centralisera versionsautomatiseringen och skala distributionen i takt med att behoven för dina programvaruprojekt ökar. Du kan distribuera Jenkins i Azure med hjälp av:
 
- [Jenkins-lösningsmallen](install-jenkins-solution-template.md) på Azure Marketplace.
- [Virtuella Azure-datorer](/azure/virtual-machines/linux/overview). Anvisningar som beskriver hur du skapar en Jenkins-instans på en virtuell dator finns i vår [självstudie](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd).
- Läs vår [instruktionsartikel](/azure/container-service/kubernetes/container-service-kubernetes-jenkins) som beskriver ett Kubernetes-kluster som körs i [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough).

Övervaka och hantera Azure Jenkins-distributionen med [Log Analytics](/azure/log-analytics/log-analytics-overview) och [Azure CLI](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Skala versionsautomatiseringen på begäran

Lägg till versionsagenter i din befintliga Jenkins-distribution och skala upp Jenkins-versionshanteringen i takt med att antalet versioner och komplexiteten i dina jobb och pipelines ökar. Du kan köra dessa versionsagenter på virtuella datorer i Azure med hjälp av [plugin-programmet för VM-agenter i Azure](jenkins-azure-vm-agents.md). Se vår [självstudie](/azure/jenkins/jenkins-azure-vm-agents) för mer information.

När de har konfigurerats med ett [Azure-tjänstobjekt](/azure/azure-resource-manager/resource-group-overview) kan Jenkins-jobb och Jenkins-pipelines använda den här informationen för att:

- På ett säkert sätt lagra och arkivera versionsartefakter i [Azure Storage](/azure/storage/common/storage-introduction) med hjälp av [Azure Storage-plugin-programmet](https://plugins.jenkins.io/windows-azure-storage). Mer information finns i [instruktionsartikeln om Jenkins-lagring](/azure/storage/common/storage-java-jenkins-continuous-integration-solution).
- Hantera och konfigurera Azure-resurser med [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Distribuera din kod till Azure-tjänster

Använd Jenkins-plugin-programmet för att distribuera dina program till Azure som en del av dina Jenkins-pipelines för CI/CD. Genom att distribuera till [Azure App Service](/azure/app-service/) och [Azure Container Service](/azure/container-service/kubernetes/) kan du mellanlagra, testa och ge ut uppdateringar för dina program utan att hantera den underliggande infrastrukturen.

 Plugin-program är tillgängliga för distribution till följande tjänster och miljöer:

- [Azure App Service i Linux](/azure/app-service/containers/app-service-linux-intro). Gå [självstudiekursen](java-deploy-webapp-tutorial.md) för att komma igång.
- [Azure App Service](/azure/app-service/overview). Läs [instruktionsartikeln](deploy-Jenkins-app-service-plugin.md) för att komma igång.