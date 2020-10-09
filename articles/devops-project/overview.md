---
title: Översikt över Azure DevOps starter | Microsoft Docs
description: Lär dig hur Azure DevOps starter hjälper dig att starta, distribuera och hantera din app från en enda vy i Azure Portal.
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 3e6f28da37980717e15b05ab94c582aa5c77e9e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856870"
---
# <a name="overview-of-azure-devops-starter"></a>Översikt över Azure DevOps starter

 Med Azure DevOps Starter är det enkelt att komma igång med Azure. Det hjälper dig att distribuera din favoritapp på den Azure-tjänst du föredrar med några snabba steg i Azure-portalen. 

 DevOps starter ställer in allt du behöver för att utveckla, distribuera och övervaka ditt program. Du kan använda instrument panelen för DevOps starter för att övervaka kod skrivningar,-versioner och-distributioner, allt från en enda vy i Azure Portal.

## <a name="advantages-of-using-devops-starter"></a>Fördelar med att använda DevOps starter

  DevOps starter automatiserar installationen av en hel kontinuerlig integrering (CI) och kontinuerlig leverans (CD) pipeline till Azure.  Du kan börja med befintlig kod eller använda en av de exempelprogram som tillhandahålls. Sedan kan du snabbt distribuera programmet till olika Azure-tjänster såsom virtuella datorer, App Service, Azure Kubernetes Service (AKS), Azure SQL Database och Azure Service Fabric.  

  DevOps startstarter utför allt arbete för den inledande konfigurationen av en DevOps-pipeline, inklusive allt från att ställa in den första git-lagringsplatsen, konfigurera CI/CD-pipeline, skapa en Application Insights-resurs för övervakning och tillhandahålla en enda vy av hela lösningen med att skapa en DevOps Projectss instrument panel i Azure Portal.

Du kan använda DevOps starter för att:

* Snabbt distribuera ditt program till Azure
* Automatisera konfigurationen av en CI/CD-pipeline
* Visa och förstå hur du konfigurerar en CI/CD-pipeline på rätt sätt
* Ytterligare anpassa versionspipelinen baserat på dina specifika scenarier

## <a name="how-to-use-devops-starter"></a>Hur använder jag DevOps starter?

  DevOps Starter är tillgängligt från Azure Portal. Du skapar en DevOps start resurs precis som du skapar andra Azure-resurser från portalen. DevOps Projects innehåller en stegvis guide för de olika konfigurationsalternativen.  

Du kan välja flera konfigurationsalternativ som en del av den initiala installationen. Dessa alternativ är:

* Använda den tillhandahållna exempelappen eller din egen kod
* Välja ett appspråk
* Välja ett appramverk utifrån språket
* Välja en Azure-tjänst (distributionsmål)
* Skapa en ny Azure DevOps-organisation eller använda en befintlig organisation 
* Välja en Azure-prenumeration
* Välja en plats för Azure-tjänsterna
* Välja bland olika prisnivåer för Azure-tjänster

När du har använt DevOps Starter kan du också ta bort alla resurser från en enda plats från DevOps starter-instrumentpanelen på Azure Portal.

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps starter och Azure DevOps-integrering

DevOps starter drivs av Azure DevOps. DevOps starter automatiserar allt arbete som behövs i Azure pipelines för att skapa en CI/CD-pipeline. Det skapar en Git-lagringsplats i en ny eller befintlig Azure DevOps-organisation och checkar sedan in ett exempelprogram eller din befintliga kod till en ny Git-lagringsplats.  

Automatiseringen upprättar även en CI-utlösare för versionen så att varje ny kodincheckning initierar en version. DevOps starter skapar en CD-utlösare och distribuerar varje ny lyckad version till den Azure-tjänst du väljer.  

Versionspipelines kan anpassas för ytterligare scenarier. Dessutom kan du klona versions- och byggpipelines för användning i andra projekt.

När du har skapat dina DevOps-startstarter kan du:

* Anpassa din versionspipeline
* Använda pull-förfrågningar för att hantera ditt kodflöde och hålla en hög kvalitet
* Testa och bygga varje incheckning innan du slår samman koden för att höja kvalitetsribban
* Spåra dina kvarvarande uppgifter och problem tillsammans med programmet

## <a name="getting-started-with-devops-starter"></a>Komma igång med DevOps starter

* [Kom igång med DevOps starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>DevOps starter-videor

* [Skapa CI/CD med Azure DevOps starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)