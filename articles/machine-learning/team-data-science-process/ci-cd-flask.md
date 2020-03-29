---
title: Skapa en CI/CD-pipeline med Azure Pipelines - Team Data Science Process
description: Skapa en kontinuerlig integration och kontinuerlig leveranspipeline för AI-applikationer (Artificial Intelligence) med Docker och Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721837"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Skapa CI/CD-pipelines för AI-appar med Azure Pipelines, Docker och Kubernetes

Ett AI-program (Artificial Intelligence) är programkod inbäddad med en förtränad maskininlärningsmodell (ML). Det finns alltid två strömmar av arbete för ett AI-program: Dataforskare bygger ML-modellen och apputvecklare bygger appen och exponerar den för slutanvändare att konsumera. I den här artikeln beskrivs hur du implementerar en ci/cd-pipeline (continuous integration and continuous delivery) för ett AI-program som bäddar in ML-modellen i appens källkod. Exempelkoden och självstudien använder ett Python Flask-webbprogram och hämtar en förtränad modell från ett privat Azure blob storage-konto. Du kan också använda ett AWS S3-lagringskonto.

> [!NOTE]
> Följande process är ett av flera sätt att göra CI / CD. Det finns alternativ till detta verktyg och förutsättningar.

## <a name="source-code-tutorial-and-prerequisites"></a>Källkod, självstudiekurs och förutsättningar

Du kan hämta [källkod](https://github.com/Azure/DevOps-For-AI-Apps) och en [detaljerad handledning](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) från GitHub. Följ självstudiestegen för att implementera en CI/CD-pipeline för ditt eget program.

Om du vill använda den hämtade källkoden och självstudiekursen behöver du följande förutsättningar: 

- [Källkoddatabasen](https://github.com/Azure/DevOps-For-AI-Apps) kluvet till ditt GitHub-konto
- En [Azure DevOps-organisation](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- Ett [AKS-kluster (Azure Container Service for Kubernetes)](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) för att köra kommandon och hämta konfiguration från AKS-klustret 
- Ett [ACR-konto (Azure Container Registry)](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Sammanfattning av CI/CD-rörledning

Varje ny Git commit startar Build pipeline. Build hämtar säkert den senaste ML-modellen från ett blob-lagringskonto och paketerar den med appkoden i en enda behållare. Den här frikopplingen av programutveckling och datavetenskap säkerställer att produktionsappen alltid kör den senaste koden med den senaste ML-modellen. Om appen klarar testningen lagrar pipelinen byggavbildningen på ett säkert sätt i en Docker-behållare i ACR. Versionspipelinen distribuerar sedan behållaren med AKS. 

## <a name="cicd-pipeline-steps"></a>Ci/CD-pipelinesteg

I följande diagram och steg beskrivs CI/CD-pipelinearkitekturen:

![CI/CD-pipelinearkitektur](./media/ci-cd-flask/architecture.png)

1. Utvecklare arbetar med programkoden i den IDE som de själva väljer.
2. Utvecklarna genomför koden till Azure Repos, GitHub eller annan Git-källkontrollprovider. 
3. Separat arbetar datavetar med att utveckla sin ML-modell.
4. Dataforskarna publicerar den färdiga modellen till en modelldatabas, i det här fallet ett bloblagringskonto. 
5. Azure Pipelines startar en version baserad på Git-committ.
6. Build-pipelinen hämtar den senaste ML-modellen från blob-lagring och skapar en behållare.
7. Pipelinen skickar byggavbildningen till den privata avbildningsdatabasen i ACR.
8. Release-pipelinen startar baserat på den lyckade versionen.
9. Pipelinen hämtar den senaste avbildningen från ACR och distribuerar den över Kubernetes-klustret på AKS.
10. Användarbegäranden för appen går via DNS-servern.
11. DNS-servern skickar begäranden till en belastningsutjämnare och skickar tillbaka svaren till användarna.

## <a name="see-also"></a>Se även

- [Team Data Science Process (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Services (AKS)](/azure/aks/intro-kubernetes)
