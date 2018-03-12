---
title: "Översikt över Jenkins och Azure | Microsoft Docs"
description: "Värd Jenkins skapa och distribuera automationsserver i Azure och använda Azure beräkning och lagring-resurser för att utöka din kontinuerlig integrering och distribution (CI/CD) pipelines."
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: 08906939ecc80999de94ad7e7141081f959fb6f2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="azure-and-jenkins"></a>Azure och Jenkins

[Jenkins](https://jenkins.io/) är en populär öppen källkod automation-server som används för att ställa in kontinuerlig integration och leverans (CI/CD) till programvara-projekt. Du kan vara värd för distributionen Jenkins i Azure eller utöka din befintliga Jenkins konfigurationen med hjälp av Azure-resurser. Jenkins plugin-program finns även att förenkla CI/CD-program till Azure.

Den här artikeln ger en introduktion till Azure med Jenkins, med information om grundläggande Azure funktioner tillgängliga för Jenkins användare. Om du vill komma igång med din egen Jenkins server i Azure, finns våra [quickstart](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Värd för dina Jenkins servrar i Azure

Värden Jenkins i Azure för att centralisera build-automation och skala distributionen när behoven i din programprojekt växer. Du kan distribuera Jenkins i Azure med hjälp:
 
- [Lösningsmall Jenkins](install-jenkins-solution-template.md) i Azure Marketplace.
- [Virtuella Azure-datorer](/azure/virtual-machines/linux/overview). Se vår [kursen](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd) att skapa en Jenkins-instans på en virtuell dator.
- På en Kubernetes kluster som körs i [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), finns våra [anvisningar](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Övervaka och hantera din Azure Jenkins med [logganalys](/azure/log-analytics/log-analytics-overview), [Operations Management Suite](/azure/operations-management-suite/operations-management-suite-overview), och [Azure CLI](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Skala build-automation på begäran

Lägg till build-agenter till befintliga Jenkins distributionen för att skala din Jenkins build kapacitet som antalet versioner och komplexiteten i dina jobb och pipelines öka. Du kan köra dessa bygga agenter på virtuella Azure-datorer med hjälp av den [plugin-program för Azure VM agenter](jenkins-azure-vm-agents.md). Se vår [kursen](/azure/jenkins/jenkins-azure-vm-agents) för mer information.

En gång konfigurerats med en [Azure tjänstens huvudnamn](/azure/azure-resource-manager/resource-group-overview), Jenkins jobb och pipelines kan använda dessa autentiseringsuppgifter för att:

- Lagra och arkivera bygga artefakter [Azure Storage](/azure/storage/common/storage-introduction) med hjälp av den [plugin-program för Azure Storage](https://plugins.jenkins.io/windows-azure-storage). Granska de [Jenkins lagring anvisningar](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) vill veta mer.
- Hantera och konfigurera Azure-resurser med den [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Distribuera din kod till Azure-tjänster

Använd Jenkins plugin-program för att distribuera programmen till Azure som en del av din Jenkins CI/CD pipelines. Distribution i [Azure App Service](/azure/app-service/) och [Azure Container Service](/azure/container-service/kubernetes/) kan du steg, testa och versionen uppdateringar för dina program utan att hantera de underliggande infrastrukturen.

 Plugin-program är tillgängliga för distribution till följande tjänster och miljöer:

- [Azure-Webbapp på Linux](/azure/app-service/containers/app-service-linux-intro). Finns det [kursen](java-deploy-webapp-tutorial.md) att komma igång.
- [Azure-Webbapp](/azure/app-service/app-service-web-overview). Finns det [anvisningar](deploy-Jenkins-app-service-plugin.md) att komma igång.

