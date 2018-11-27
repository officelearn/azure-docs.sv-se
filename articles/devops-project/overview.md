---
title: Översikt över Azure DevOps Projects | Microsoft Docs
description: Förstå värdet av Azure DevOps Projects
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: 9efb3c9bae790efed3d09274b1a77d7b8161d479
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165251"
---
# <a name="overview-of-azure-devops-projects"></a>Översikt över Azure DevOps Projects

 Azure DevOps Projects gör det enkelt att komma igång med Azure. Det hjälper dig att distribuera din favoritapp på den Azure-tjänst du föredrar med några snabba steg i Azure-portalen. 

 DevOps Projects konfigurerar allt du behöver för att utveckla, distribuera och övervaka ditt program. Du kan använda DevOps Projects-instrumentpanelen för att övervaka allt från kodincheckningar till versioner och distributioner från en och samma vy i Azure-portalen.

## <a name="why-should-i-use-devops-projects"></a>Varför bör jag använda DevOps Projects?

  DevOps Projects automatiserar installationen av en hel pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) till Azure.  Du kan börja med befintlig kod eller använda en av de exempelprogram som tillhandahålls. Sedan kan du snabbt distribuera programmet till olika Azure-tjänster såsom virtuella datorer, App Service, Azure Kubernetes Service (AKS), Azure SQL Database och Azure Service Fabric.  

  DevOps Projects gör allt arbete för den inledande konfigurationen av en DevOps-pipeline, inklusive allt från att installera den första Git-lagringsplatsen, konfigurera CI/CD-pipelinen och skapa en Application Insights-resurs för övervakning till att tillhandahålla en enda vy av hela lösningen genom att skapa en DevOps Projects-instrumentpanel i Azure-portalen.

Du kan använda DevOps Projects för att:

* Snabbt distribuera ditt program till Azure
* Automatisera konfigurationen av en CI/CD-pipeline
* Visa och förstå hur du konfigurerar en CI/CD-pipeline på rätt sätt
* Ytterligare anpassa versionspipelinen baserat på dina specifika scenarier

## <a name="how-do-i-use-devops-projects"></a>Hur använder jag DevOps Projects?

  DevOps Projects är tillgängligt i Azure-portalen. Du kan skapa en DevOps Projects-resurs precis som vilken annan Azure-resurs som helst i portalen. DevOps Projects innehåller en stegvis guide för de olika konfigurationsalternativen.  

Du kan välja flera konfigurationsalternativ som en del av den initiala installationen. Dessa alternativ är:

* Använda den tillhandahållna exempelappen eller din egen kod
* Välja ett appspråk
* Välja ett appramverk utifrån språket
* Välja en Azure-tjänst (distributionsmål)
* Skapa en ny Azure DevOps-organisation eller använda en befintlig organisation 
* Välja en Azure-prenumeration
* Välja en plats för Azure-tjänsterna
* Välja bland olika prisnivåer för Azure-tjänster

När du har använt DevOps Projects kan du även ta bort alla resurser från den gemensamma platsen på DevOps Projects-instrumentpanelen i Azure-portalen.

## <a name="devops-projects-and-azure-devops-integration"></a>Integrering av DevOps Projects och Azure DevOps

DevOps Projects drivs av Azure DevOps. DevOps Projects automatiserar allt arbete som behövs i Azure Pipelines för att konfigurera en CI/CD-pipeline. Det skapar en Git-lagringsplats i en ny eller befintlig Azure DevOps-organisation och checkar sedan in ett exempelprogram eller din befintliga kod till en ny Git-lagringsplats.  

Automatiseringen upprättar även en CI-utlösare för versionen så att varje ny kodincheckning initierar en version. DevOps Projects skapar även en CD-utlösare och distribuerar varje ny lyckad version till den Azure-tjänst du väljer.  

Versionspipelines kan anpassas för ytterligare scenarier. Dessutom kan du klona versions- och byggpipelines för användning i andra projekt.

När du har skapat ditt DevOps-projekt kan du:

* Anpassa din versionspipeline
* Använda pull-förfrågningar för att hantera ditt kodflöde och hålla en hög kvalitet
* Testa och bygga varje incheckning innan du slår samman koden för att höja kvalitetsribban
* Spåra dina kvarvarande uppgifter och problem tillsammans med programmet

## <a name="how-do-i-start-using-devops-projects"></a>Hur börjar jag använda DevOps Projects?

* [Kom igång med DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>DevOps Projects-videor

* [Skapa CI/CD med Azure DevOps Projects](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
