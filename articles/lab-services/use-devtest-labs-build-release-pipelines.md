---
title: Använda DevTest Labs i Azure pipelines build and release pipelines | Microsoft Docs
description: 'Lär dig hur du använder Azure DevTest Labs i pipeline: build och release i Azure.'
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2019
ms.author: spelluru
ms.openlocfilehash: 032f598fed765b281d4a6a124f8855abc201ee94
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774555"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Använda DevTest Labs i Azure pipelines build and release-pipelines
Den här artikeln innehåller information om hur DevTest Labs kan användas i pipeline: build och release i Azure. 

## <a name="overall-flow"></a>Övergripande flöde
Det grundläggande flödet är att ha en **pipeline** för bygge som utför följande uppgifter:

1. Bygg program koden.
1. Skapa bas miljön i DevTest Labs.
1. Uppdatera miljön med anpassad information.
1. Distribuera programmet till DevTest Labs-miljön
1. Testa koden. 

När bygget har slutförts använder lanserings **pipeline** Bygg artefakterna för att distribuera mellanlagringen eller produktionen. 

En av de nödvändiga anläggningarna är att all information som behövs för att återskapa det testade eko systemet är tillgänglig i versions artefakterna, inklusive konfigurationen av Azure-resurserna. Eftersom Azure-resurser betalar en kostnad när de används vill företag antingen styra eller spåra användningen av dessa resurser. I vissa situationer kan Azure Resource Manager mallar som används för att skapa och konfigurera resurser hanteras av en annan avdelning, till exempel. Och de här mallarna kan lagras i en annan lagrings plats. Det leder till en intressant situation där en version skapas och testas, och både koden och konfigurationen måste lagras i bygg artefakterna för att återskapa systemet i produktion på rätt sätt. 

Med DevTest Labs under build/test-fasen kan du lägga till Azure Resource Manager mallar och stödfiler till versions källorna så att den exakta konfigurationen som används för att testa med distribueras till produktion under den versions fas som används för att testa med. Med rätt konfiguration sparas Resource Manager-mallarna i versions artefakterna för aktiviteten **skapa Azure DevTest Labss miljö** med rätt konfiguration. I det här exemplet ska du använda koden från [självstudien: Bygg en .net Core-och SQL Database-webbapp i](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)Azure App Service för att distribuera och testa webbappen i Azure.

![Övergripande flöde](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Ställa in Azure-resurser
Det finns ett par objekt som måste skapas i förväg:

- Två databaser. Den första som har koden från självstudien och en Resource Manager-mall med två ytterligare virtuella datorer. Den andra kommer att innehålla bas Azure Resource Managers mal len (befintlig konfiguration).
- En resurs grupp för distribution av produktions koden och konfigurationen.
- Ett labb måste konfigureras med en [anslutning till konfigurations lagrings platsen](devtest-lab-create-environment-from-arm.md) för build-pipeline. Resource Manager-mallen måste kontrol leras i konfigurations lagrings platsen som azuredeploy. JSON med metadata. JSON för att DevTest-labb ska kunna identifiera och distribuera mallen.

Pipelinen build skapar en DevTest Labs-miljö och distribuerar koden för testning.

## <a name="set-up-a-build-pipeline"></a>Konfigurera en pipeline för bygge
I Azure-pipeline skapar du en versions pipeline med hjälp av koden från [självstudien: Bygg en .NET Core-och SQL Database-webbapp i](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)Azure App Service. Använd **ASP.net Core** mall, som fyller i den nödvändiga uppgiften för att bygga, testa och publicera koden.

![Välj mallen ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Du måste lägga till tre ytterligare aktiviteter för att skapa miljön i DevTest Labs och distribuera till miljön.

![Pipeline med tre uppgifter](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Skapa miljö aktivitet
I aktiviteten skapa miljö (**Azure DevTest Labs Skapa miljö** ) använder du List rutorna för att välja följande värden:

- Azure-prenumeration
- Labbets namn
- namn på databasen
- namnet på mallen (som visar mappen där miljön lagras). 

Vi rekommenderar att du använder list rutor på sidan i stället för att ange informationen manuellt. Om du anger informationen manuellt anger du fullständigt kvalificerade ID för Azure-resurs. Uppgiften visar de egna namnen i stället för resurs-ID. 

Miljö namnet är det namn som visas i DevTest Labs. Det bör vara ett unikt namn för varje version. Exempel: **TestEnv $ (build. BuildId)** . 

Du kan ange antingen parameter filen eller parametrar för att skicka information till Resource Manager-mallen. 

Välj kommandot **skapa utdata baserat på utdata-alternativet för miljö mal len** och ange ett referens namn. I det här exemplet anger du **BaseEnv** som referens namn. Du kommer att använda den här BaseEnv när du konfigurerar nästa uppgift. 

![Skapa Azure DevTest Labss miljö aktivitet](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Uppdatera miljö aktivitet
Den andra aktiviteten (**Azure DevTest Labs fylla** i en miljö uppgift) är att uppdatera den befintliga DevTest Labs-miljön. Aktiviteten skapa miljö utdata **BaseEnv. environmentResourceId** som används för att konfigurera miljö namnet för den här aktiviteten. Resource Manager-mallen för det här exemplet har två parametrar- **adminUserName** och **adminPassword**. 

![Fylla i Azure DevTest Labss miljö aktivitet](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>App Service distribuera uppgift
Den tredje aktiviteten är aktiviteten **Azure App service distribution** . Appens typ är inställd på **webbapp** och App Service namnet anges till **$ (webbplats)** .

![App Service distribuera uppgift](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Konfigurera versions pipeline
Du skapar en versions pipeline med två uppgifter: **Azure-distribution: Skapa eller uppdatera resurs gruppen** och **Distribuera Azure App Service**. 

För den första aktiviteten anger du namn och plats för resurs gruppen. Sökvägen är en länkad artefakt. Om Resource Manager-mallen innehåller länkade mallar måste en anpassad distribution av resurs grupper implementeras. Mallen finns i den publicerade Drop-artefakten. Åsidosätt mallparametrar för Resource Manager-mallen. Du kan lämna kvar de återstående inställningarna med standardvärdena. 

För den andra aktiviteten **distribuerar Azure App Service**anger du Azure-prenumerationen, väljer **webbapp** för **typ av app**och **$ (webbplats)** för **App Service namn**. Du kan lämna kvar de återstående inställningarna med standardvärdena. 

## <a name="test-run"></a>Kör test
Nu när båda pipelinen har kon figurer ATS kan du skapa en version i kö manuellt och se hur det fungerar. Nästa steg är att ange lämplig utlösare för build och ansluta till versions pipelinen.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Integrera Azure DevTest Labs i dina Azure-pipeliner kontinuerlig integrering och leverans pipeline](devtest-lab-integrate-ci-cd-vsts.md)
- [Integrera miljöer i Azures pipelines CI/CD-pipelines](integrate-environments-devops-pipeline.md)
