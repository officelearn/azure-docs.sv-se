---
title: 'DevOps för artificiell intelligens (AI)-program: skapa pipeline för kontinuerlig integrering på Azure med Docker, Kubernetes och Python Flask-program'
description: 'DevOps för artificiell intelligens (AI)-program: skapa pipeline för kontinuerlig integrering på Azure med Docker och Kubernetes'
services: machine-learning, team-data-science-process
documentationcenter: ''
author: jainr
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jainr
ms.openlocfilehash: 6de1832dde1764b2655d4c34643d6a026e198f64
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052232"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>DevOps för artificiell intelligens (AI)-program: skapa pipeline för kontinuerlig integrering på Azure med Docker och Kubernetes
Det finns ofta två strömmar av arbete, Dataexperter att skapa machine learning-modeller och App-utvecklare att bygga programmet och göra den tillgänglig att slutanvändare kan använda för ett AI-program. I den här artikeln visar vi hur du implementerar en kontinuerlig integrering (CI) / kontinuerlig leverans (CD) pipeline för ett AI-program. AI-program är en kombination av programkoden inbäddad med en tränats machine learning (ML)-modell. Vi hämtar en pretrained modell från en privat Azure blob storage-konto, det kan vara ett AWS S3-konto i den här artikeln. Vi använder en enkel python flask-webbapp för artikeln.

> [!NOTE]
> Detta är en CI/CD kan utföras på flera olika sätt. Det finns alternativ till verktygen och andra förutsättningar som anges nedan. När vi utvecklar ytterligare innehåll, publicerar vi de.
>
>

## <a name="github-repository-with-document-and-code"></a>GitHub-databaser med dokument och kod
Du kan ladda ned källkoden från [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). En [detaljerad självstudien](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) är också tillgänglig.

## <a name="pre-requisites"></a>Förutsättningar
Följande är förutsättningar för att kunna följa CI/CD-pipeline som beskrivs nedan:
* [Visual Studio Team Services-konto](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure Container Service (AKS)-kluster som kör Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Azure Container-registret (ACR)-konto](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Installera Kubectl för att köra kommandon mot Kubernetes-kluster.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Vi behöver detta för att hämta konfigurationen från ACS-kluster. 
* Förgrena lagringsplatsen till ditt GitHub-konto.

## <a name="description-of-the-cicd-pipeline"></a>Beskrivning av CI/CD-pipeline
Pipeline-sparkar ut för varje ny incheckning, kör test-sviten om test-pass tar den senaste versionen, paketerar den i en Docker-behållare. Behållaren distribueras sedan använda Azure container service (ACS) och avbildningar lagras säkert i Azure container registry (ACR). ACS med Kubernetes för att hantera behållarkluster men du kan välja Docker Swarm eller Mesos.

Programmet hämtar på ett säkert sätt den senaste modellen från ett Azure Storage-konto och paket som som en del av programmet. Det distribuerade programmet har kod och ML-modell paketerad som enskild behållare. Detta frikopplas de appen utvecklare och dataexperter, att se till att deras produktionsapp alltid körs den senaste koden med senaste ML-modell.

Pipeline-arkitekturen anges nedan. 

![Arkitektur](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Stegen i CI/CD-pipeline
1. Utvecklare arbeta i IDE med sina programkoden.
2. De kan koppla koden till källkontroll för valfri (VSTS har bra stöd för olika käll-kontroller)
3. Data Science fungerar separat, om hur du utvecklar sin modell.
4. När glada, de publicerar modellen till en modell, använder vi i det här fallet blob storage-kontot. Detta kan enkelt ersättas med Azure ML Workbench modellhanteringstjänsten via sina REST-API: er.
5. En version har startats i VSTS utifrån genomförandet i GitHub.
6. VSTS skapa pipeline hämtar den senaste modellen från Blob-behållare och skapar en behållare.
7. VSTS skickar avbildningen till lagringsplatsen för privat avbildning i Azure Container Registry
8. Releasepipeline har startats på ett visst schema (varje natt).
9. Senaste avbildning från ACR hämtas och distribueras över Kubernetes-kluster i ACS.
10. Användare-begäran för appen går igenom DNS-server.
11. DNS-servern skickar sin begäran till belastningsutjämnaren och skickar svaret tillbaka till användaren.

## <a name="next-steps"></a>Nästa steg
* Referera till den [självstudien](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) att följa informationen och implementera en egen CI/CD-pipeline för ditt program.

## <a name="references"></a>Referenser
* [Team Data Science Process (TDSP)](https://aka.ms/tdsp)
* [Azure Machine Learning (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/vso/)
* [Azure Kubernetes-tjänster (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
