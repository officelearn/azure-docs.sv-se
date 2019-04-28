---
title: Skapa kontinuerlig integrering Azure pipeline - Team Data Science Process
description: 'DevOps för artificiell intelligens (AI) program: Skapa pipeline för kontinuerlig integrering på Azure med Docker och Kubernetes'
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/22/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: d99149f8112c19a07208523a1ee26ba1c36e5362
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103584"
---
# <a name="creating-continuous-integration-pipeline-on-azure-using-docker-kubernetes-and-python-flask-application"></a>Skapa pipeline för kontinuerlig integrering på Azure med Docker, Kubernetes och Python Flask-program
Det finns ofta två strömmar av arbete, Dataexperter att skapa machine learning-modeller och App-utvecklare att bygga programmet och göra den tillgänglig att slutanvändare kan använda för ett AI-program. I den här artikeln visar vi hur du implementerar en kontinuerlig integrering (CI) / kontinuerlig leverans (CD) pipeline för ett AI-program. AI-program är en kombination av programkoden inbäddad med en tränats machine learning (ML)-modell. Vi hämtar en pretrained modell från en privat Azure blob storage-konto, det kan vara ett AWS S3-konto i den här artikeln. Vi använder en enkel python flask-webbapp för artikeln.

> [!NOTE]
> Detta är en CI/CD kan utföras på flera olika sätt. Det finns alternativ till verktygen och andra förutsättningar som anges nedan. När vi utvecklar ytterligare innehåll, publicerar vi de.
>
>

## <a name="github-repository-with-document-and-code"></a>GitHub-databaser med dokument och kod
Du kan ladda ned källkoden från [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). En [detaljerad självstudien](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) är också tillgänglig.

## <a name="pre-requisites"></a>Förutsättningar
Följande är förutsättningar för att kunna följa CI/CD-pipeline som beskrivs nedan:
* [Azure DevOps-organisation](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure Container Service (AKS)-kluster som kör Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Azure Container Registry (ACR)-konto](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Installera Kubectl för att köra kommandon mot Kubernetes-kluster.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Vi behöver detta för att hämta konfigurationen från ACS-kluster. 
* Förgrena lagringsplatsen till ditt GitHub-konto.

## <a name="description-of-the-cicd-pipeline"></a>Beskrivning av CI/CD-pipeline
Pipeline-sparkar ut för varje ny incheckning, kör test-sviten om test-pass tar den senaste versionen, paketerar den i en Docker-behållare. Behållaren distribueras sedan med hjälp av Azure Container Service (ACS) och avbildningar lagras säkert i Azure Container Registry (ACR). ACS med Kubernetes för att hantera behållarkluster men du kan välja Docker Swarm eller Mesos.

Programmet hämtar på ett säkert sätt den senaste modellen från ett Azure Storage-konto och paket som som en del av programmet. Det distribuerade programmet har kod och ML-modell paketerad som enskild behållare. Detta frikopplas de appen utvecklare och dataexperter, att se till att deras produktionsapp alltid körs den senaste koden med senaste ML-modell.

Pipeline-arkitekturen anges nedan. 

![Arkitektur](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Stegen i CI/CD-pipeline
1. Utvecklare arbeta i IDE med sina programkoden.
2. De kan koppla koden till källkontroll för valfri (Azure DevOps har bra stöd för olika käll-kontroller)
3. Data Science fungerar separat, om hur du utvecklar sin modell.
4. När glada, de publicerar modellen till en modell, använder vi i det här fallet blob storage-kontot. 
5. En version har startats i Azure DevOps utifrån genomförandet i GitHub.
6. Azure DevOps-Build pipeline hämtar den senaste modellen från Blob-behållare och skapar en behållare.
7. Azure DevOps skickar avbildningen till lagringsplatsen för privat avbildning i Azure Container Registry
8. Releasepipeline har startats på ett visst schema (varje natt).
9. Senaste avbildning från ACR hämtas och distribueras över Kubernetes-kluster i ACS.
10. Användare-begäran för appen går igenom DNS-server.
11. DNS-servern skickar sin begäran till belastningsutjämnaren och skickar svaret tillbaka till användaren.

## <a name="next-steps"></a>Nästa steg
* Referera till den [självstudien](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) att följa informationen och implementera en egen CI/CD-pipeline för ditt program.

## <a name="references"></a>Referenser
* [Team Data Science Process (TDSP)](https://aka.ms/tdsp)
* [Azure Machine Learning (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Azure DevOps](https://www.visualstudio.com/vso/)
* [Azure Kubernetes-tjänster (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
