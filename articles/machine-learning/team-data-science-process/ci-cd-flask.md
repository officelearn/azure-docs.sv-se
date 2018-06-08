---
title: 'DevOps för program som styrs av datorn (AI): skapa kontinuerlig integration pipeline i Azure med hjälp av Docker, Kubernetes och Python Flask'
description: 'DevOps för program som styrs av datorn (AI): skapa kontinuerlig integration pipeline i Azure med hjälp av Docker och Kubernetes'
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
ms.openlocfilehash: 233da393bb9e030d885ce588f4841dc1c707c1cb
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836274"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>DevOps för program som styrs av datorn (AI): skapa kontinuerlig integration pipeline i Azure med hjälp av Docker och Kubernetes
Det finns ofta två dataströmmar arbetet, Datavetare skapa machine learning-modeller och App-utvecklare skapa programmet och sedan exponera det att slutanvändare kan använda för ett AI-program. I den här artikeln visar vi hur du implementerar en kontinuerlig Integration (KO) / kontinuerlig leverans (CD) i pipeline för ett AI-program. AI program är en kombination av programkoden inbäddade med en pretrained machine learning (ML) modell. För den här artikeln vi hämtar en pretrained modell från ett privat Azure blob storage-konto, kan det bero på ett AWS S3-konto. Vi använder en enkel python flask för artikeln.

> [!NOTE]
> Detta är en CI/CD kan utföras på flera olika sätt. Det finns alternativ till verktygsuppsättning och andra krav som anges nedan. När vi utvecklar ytterligare innehåll, publicerar vi de.
>
>

## <a name="github-repository-with-document-and-code"></a>GitHub-lagret med dokumentet och kod
Du kan ladda ned källkoden från [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). En [detaljerad vägledning](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) är också tillgänglig.

## <a name="pre-requisites"></a>Förutsättningar
Följande är kraven för följande CI/CD-pipelinen beskrivs nedan:
* [Visual Studio Team Services-konto](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure Container Service (AKS)-kluster som kör Kubernetes](https://docs.microsoft.com/en-us/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Azure Container registret (ACR)-konto](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal)
* [Installera Kubectl för att köra kommandon mot Kubernetes klustret.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Vi behöver detta för att hämta konfigurationen från ACS-kluster. 
* Duplicera databasen till GitHub-konto.

## <a name="description-of-the-cicd-pipeline"></a>Beskrivning för CI/CD-pipeline
Pipeline-sparkar ut för varje ny genomförande Kör test-sviten om tester som tar den senaste versionen paket den i en dockerbehållare. Behållaren distribueras sedan med hjälp av Azure container service (ACS) och avbildningar lagras säkert i Azure-behållaren registret (ACR). ACS körs Kubernetes för att hantera behållare kluster, men du kan välja Docker Swarm eller Mesos.

Programmet hämtar på ett säkert sätt senaste modellen från ett Azure Storage-konto och paket som som en del av programmet. Det distribuerade programmet har Appkod och ML-modell paketeras som enskild behållare. Detta frikopplar i appen utvecklare och data forskare, se till att deras produktionsprogrammet alltid körs senaste koden med senaste ML-modell.

Pipeline-arkitektur anges nedan. 

![Arkitektur](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Pipeline-stegen för CI/CD: N
1. Utvecklare fungerar på IDE valfri i programkoden.
2. De kan koppla koden till källkontroll valfri (VSTS har bra stöd för olika käll-kontroller)
3. Forskare data fungerar separat, för att utveckla sina modellen.
4. När glada, de publicerar modellen till en databas i modellen använder i det här fallet du ett blob storage-konto. Detta kan enkelt kan ersättas med Azure ML arbetsstationen modellen management-tjänsten via sina REST-API: er.
5. En version har inletts i VSTS baserat på genomförande i GitHub.
6. VSTS skapa pipeline hämtar senaste modellen från Blob-behållaren och skapar en behållare.
7. VSTS skickar bilden till privata avbildningslagringsplatsen i registret för Azure-behållare
8. På ett visst schema (rensning) inletts version pipeline.
9. Senaste avbildningen från ACR hämtas och distribueras över Kubernetes kluster på ACS.
10. Användare-begäran för appen går igenom DNS-server.
11. DNS-servern skickar begäran till belastningsutjämnaren och skickar svaret tillbaka till användaren.

## <a name="next-steps"></a>Nästa steg
* Referera till den [kursen]((https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)) följa information och implementera din egen CI/CD-pipeline för ditt program.

## <a name="references"></a>Referenser
* [Team datavetenskap Process (TDSP)](https://aka.ms/tdsp)
* [Azure Machine Learning (AML)](https://docs.microsoft.com/en-us/azure/machine-learning/service/)
* [Visual Studio Team Services VSTS)](https://www.visualstudio.com/vso/)
* [Azure Kubernetes tjänster (AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes)