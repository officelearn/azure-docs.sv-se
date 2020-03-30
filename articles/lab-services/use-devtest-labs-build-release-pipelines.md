---
title: Använda DevTest Labs i Build and Release-pipelines i Azure Pipelines
description: Lär dig hur du använder Azure DevTest Labs i Azure Pipelines bygga och släppa pipelines.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169225"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Använda DevTest Labs i Build and Release-pipelines i Azure Pipelines
Den här artikeln innehåller information om hur DevTest Labs kan användas i Azure Pipelines bygga och släppa pipelines. 

## <a name="overall-flow"></a>Totalt flöde
Det grundläggande flödet är att ha en **byggpipeline** som utför följande uppgifter:

1. Skapa programkoden.
1. Skapa basmiljön i DevTest Labs.
1. Uppdatera miljön med anpassad information.
1. Distribuera programmet till DevTest Labs-miljön
1. Testa koden. 

När build har slutförts kommer **versionspipelinen** att använda byggartefakterna för att distribuera mellanlagring eller produktion. 

En av de nödvändiga lokalerna är att all information som behövs för att återskapa det testade ekosystemet är tillgänglig i build-artefakterna, inklusive konfigurationen av Azure-resurserna. Eftersom Azure-resurser ådrar sig en kostnad när de används, vill företag antingen styra eller spåra användningen av dessa resurser. I vissa situationer kan Azure Resource Manager-mallar som används för att skapa och konfigurera resurserna hanteras av en annan avdelning som IT. Och dessa mallar kan lagras i en annan databas. Det leder till en intressant situation där en version kommer att skapas och testas, och både koden och konfigurationen måste lagras i byggartefakterna för att återskapa systemet i produktion på rätt sätt. 

Med DevTest Labs under bygg-/testfasen kan du lägga till Azure Resource Manager-mallar och stödfiler i byggkällorna så att den exakta konfigurationen som används för att testa med under utgivningsfasen distribueras till produktion. Aktiviteten **Skapa Azure DevTest Labs-miljö** med rätt konfiguration sparar Resource Manager-mallarna i build-artefakterna. I det här exemplet använder du koden från [självstudiekursen: Skapa en .NET Core- och SQL Database-webbapp i Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)för att distribuera och testa webbappen i Azure.

![Totalt flöde](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Ställa in Azure-resurser
Det finns ett par objekt som måste skapas i förväg:

- Två förråd. Den första med koden från självstudien och en Resource Manager-mall med ytterligare två virtuella datorer. Den andra innehåller den grundläggande Azure Resource Manager-mallen (befintlig konfiguration).
- En resursgrupp för distribution av produktionskoden och konfigurationen.
- Ett labb måste konfigureras med en [anslutning till konfigurationsdatabasen](devtest-lab-create-environment-from-arm.md) för byggpipelinen. Resource Manager-mallen måste checkas in i konfigurationsdatabasen som azuredeploy.json med metadata.json för att DevTest Labs ska kunna känna igen och distribuera mallen.

Build-pipelinen skapar en DevTest Labs-miljö och distribuerar koden för testning.

## <a name="set-up-a-build-pipeline"></a>Konfigurera en byggpipeline
Skapa en byggpipeline med hjälp av koden från [självstudiekursen: Skapa en webbapp för .NET Core och SQL Database i Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md). Använd **mallen ASP.NET Core,** som fyller i den uppgift som krävs för att skapa, testa och publicera koden.

![Markera mallen ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Du måste lägga till ytterligare tre uppgifter för att skapa miljön i DevTest Labs och distribuera till miljön.

![Pipeline med tre aktiviteter](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Skapa miljöuppgift
I aktiviteten skapa miljö (**Azure DevTest Labs Create Environment** task) använder du listrutorna för att välja följande värden:

- Azure-prenumeration
- namnet på labbet
- databasens namn
- mallens namn (som visar mappen där miljön lagras). 

Vi rekommenderar att du använder listrutor på sidan i stället för att ange informationen manuellt. Om du anger informationen manuellt anger du fullständigt kvalificerade Azure Resource ID:er. Aktiviteten visar de egna namnen i stället för resurs-ID. 

Miljönamnet är det visade namnet som visas i DevTest Labs. Det bör vara ett unikt namn för varje byggnad. Till exempel: **TestEnv$(Build.BuildId)**. 

Du kan ange antingen parameterfil eller parametrar för att skicka information till Resource Manager-mallen. 

Välj **utdatavariablerna Skapa baserat på utdataalternativet för miljömallen** och ange ett referensnamn. I det här exemplet anger du **BaseEnv** för referensnamnet. Du kommer att använda den här BaseEnv när du konfigurerar nästa uppgift. 

![Skapa azure devtest labs miljö uppgift](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Fylla miljöuppgift
Den andra uppgiften (**Azure DevTest Labs Populate Environment** task) är att uppdatera den befintliga DevTest Labs-miljön. Aktiviteten Skapa miljöutdata utdata **BaseEnv.environmentResourceId** som används för att konfigurera miljönamn för den här aktiviteten. Mallen Resurshanteraren för det här exemplet har två parametrar - **adminUserName** och **adminPassword**. 

![Fylla i azure devtest labs-miljöuppgift](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Distribuera uppgift för App-tjänst
Den tredje uppgiften är **Azure App Service Deploy-uppgiften.** Apptypen är inställd på **Web App** och apptjänstnamnet är inställt på **$(WebSite).**

![Distribuera uppgift om apptjänst](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Konfigurera versionspipeline
Du skapar en versionspipeline med två uppgifter: **Azure Deployment: Skapa eller uppdatera resursgrupp** och **distribuera Azure App Service**. 

För den första aktiviteten anger du namn och plats för resursgruppen. Mallplatsen är en länkad artefakt. Om Resurshanteraren-mallen innehåller länkade mallar måste en anpassad resursgruppsdistribution implementeras. Mallen finns i den publicerade släppartefakten. Åsidosätt mallparametrar för resurshanteraren-mallen. Du kan lämna återstående inställningar med standardvärden. 

För den andra uppgiften **Distribuera Azure App Service**anger du Azure-prenumerationen, väljer Web **App** för **apptypen**och **$(WebSite)** för **apptjänstnamnet**. Du kan lämna återstående inställningar med standardvärden. 

## <a name="test-run"></a>Provkörning
Nu när båda pipelines är inställda, manuellt köa upp en bygga och se det fungerar. Nästa steg är att ställa in lämplig utlösare för att bygga och ansluta bygga till release pipeline.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Integrera Azure DevTest Labs i din Azure Pipelines kontinuerlig integrering och leveranspipeline](devtest-lab-integrate-ci-cd-vsts.md)
- [Integrera miljöer i dina CI/CD-pipelines i Azure Pipelines](integrate-environments-devops-pipeline.md)
