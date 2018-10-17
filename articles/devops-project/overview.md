---
title: Översikt över Azure DevOps Project | Microsoft Docs
description: Förstå värdet för Azure DevOps Project
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
ms.openlocfilehash: 39dffad597b8382dea4df6fa1b0726d9582d67d1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44293634"
---
# <a name="overview-of-azure-devops-project"></a>Översikt över Azure DevOps Project

Azure DevOps Project gör det enkelt att komma igång med Azure. DevOps-projektresursen hjälper dig att starta din typ av favoritapp på den Azure-tjänst du föredrar med några snabba steg i Azure-portalen. DevOps Project konfigurerar allt du behöver för att utveckla, distribuera och övervaka ditt program.
Med DevOps Project-instrumentpanelen kan du övervaka allt från kodincheckningar till versioner och distributioner från en och samma vy i Azure Portal.

## <a name="why-should-i-use-the-azure-devops-project"></a>Varför ska jag använda Azure DevOps Project?

Azure DevOps Project automatiserar installationen av en hel pipeline för kontinuerlig Integration (CI) och kontinuerlig leverans (CD) till Azure.  Du kan börja med en befintlig kod eller använda något av de angivna exempelprogrammen och sedan snabbt distribuera programmet till olika Azure-tjänster som Virtual Machines, App Service, Azure Container Service, Azure SQL Database eller Azure Service Fabric.  

Azure DevOps-projektet gör allt arbete för den inledande konfigurationen av en DevOps-pipeline, inklusive allt från att installera den första Git-lagringsplatsen, konfigurera CI/CD-pipelinen och skapa en Application Insights-resurs för övervakning till att tillhandahålla en enda vy av hela lösningen genom att skapa en Azure DevOps Project-instrumentpanel i Azure-portalen.

Du kan användas Azure DevOps Project till att:

* Snabbt distribuera ditt program till Azure
* Automatisera konfigurationen av en Azure CI/CD-pipeline
* Använd DevOps-projektet som en mall för att visa och förstå hur man korrekt konfigurerar CI/CD för Azure med Azure DevOps
* Kom igång med CI/CD-pipelinen till Azure och anpassa sedan versionspipelinen utifrån dina specifika scenarier

## <a name="how-do-i-use-the-azure-devops-project"></a>Hur använder jag Azure DevOps Project?

Azure DevOps Project är tillgängligt i Azure Portal.  Du kan skapa en Azure DevOps-projektresurs precis som vilken annan Azure-resurs som helst i portalen.  DevOps-projektet innehåller en steg-för-steg-liknande guide för de olika konfigurationsalternativen.  

Du kan välja flera konfigurationsalternativ som en del av den initiala installationen.  Dessa alternativ är:

* Använd den tillhandahållna exempelappen eller använd din egen kod
* Välj ett appspråk
* Välj ett appramverk utifrån det valda språket
* Välj en Azure-tjänst (distributionsmål)
* Azure DevOps-organisation (ny eller befintlig)
* Välj en Azure-prenumeration
* Välj en plats för Azure-tjänsterna
* Välj bland olika prisnivåer för Azure-tjänster

När du har använt Azure DevOps Project kan du även ta bort alla resurserna från den gemensamma platsen på Azure DevOps Project-instrumentpanelen i Azure Portal.

## <a name="azure-devops-project-and-azure-devops-integration"></a>Integrering av Azure DevOps-projekt och Azure DevOps

DevOps Project drivs av Azure DevOps.  DevOps-projektet automatiserar allt arbete som behövs i Azure DevOps för att konfigurera CI/CD för Azure.  En Git-lagringsplats har skapats i en ny eller befintlig Azure DevOps-organisation.  DevOps-projektet checkar in ett exempelprogram eller din befintliga kod på en ny Git-lagringsplats.  Automatiseringen upprättar även en CI-utlösare för versionen så att varje ny kodincheckning initierar en version.  DevOps-projektet skapar även en CD-utlösare och distribuerar varje ny lyckad version till den Azure-tjänst du valt.  Versionspipelines kan anpassas för ytterligare scenarier.  Du kan också klona versionspipelines för användning i andra projekt.

När du har skapat ditt DevOps-projekt kan du:

* Anpassa din versionspipeline
* Använda pull-förfrågningar för att hantera ditt kodflöde och hålla en hög kvalitet
* Testa och bygga varje incheckning innan du slår samman koden för att höja kvalitetsribban
* Spåra dina projektlogg och problem tillsammans med ditt program

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Hur börjar jag använda Azure DevOps Project?

* [Kom igång med Azure DevOps Project](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Azure DevOps Project-videor

* [Skapa CI/CD med Azure DevOps-projekt](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
