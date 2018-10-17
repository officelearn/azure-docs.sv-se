---
title: Distribuera din ASP.NET Core-app till Azure Service Fabric med Azure DevOps-projekt | Självstudier för Azure DevOps Services
description: DevOps-projekt gör det enkelt att komma igång med Azure. Azure DevOps-projektet gör det enkelt att distribuera din ASP.NET Core-app med Azure Service Fabric i några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6a0539b2213b99e09021a4f90d914172eac62560
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300407"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>Självstudier: Distribuera din ASP.NET Core-app till Azure Service Fabric med Azure DevOps-projekt | VSTS självstudier

Azure DevOps-projektet ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett av exempelprogrammen för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.  DevOps-projektet skapar automatiskt Azure-resurser som Azure Service Fabric, skapar och konfigurerar en versionspipeline i Azure DevOps som konfigurerar en CI/CD-pipeline och skapar sedan en Azure Application Insights-resurs för övervakning.

Du kommer att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET Core-app och Service Fabric
> * Konfigurera Azure DevOps Services och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera automatiskt till Azure
> * Rensa resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>Skapa ett Azure DevOps-projekt för en ASP.NET Core-app och Service Fabric

Azure DevOps-projektet skapar en CI/CD-pipeline.  Du kan skapa en **ny Azure DevOps Services-organisation** eller använda en **befintlig organisation**.  Azure DevOps-projektet skapar även **Azure-resurser** som ett Service Fabric-kluster i den **Azure-prenumeration** som du väljer.

1. Logga in i [Microsoft Azure-portalen](https://portal.azure.com).

1. Välj ikonen **Skapa en resurs** i det vänstra navigeringsfältet och sök efter **DevOps-projekt**.  Välj **Skapa**.

    ![Startar kontinuerlig leverans](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. Välj **.NET** och välj sedan **Nästa**.

1. För **Välj ett programramverk** väljer du **ASP.NET Core**. Välj sedan **Nästa**.

1. Välj **Service Fabric-kluster** och sedan **Nästa**.  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Konfigurera Azure DevOps Services och en Azure-prenumeration

1. Skapa en **ny** Azure DevOps Services-organisation eller använd en **befintlig** organisation.  Välj ett **namn** för ditt Azure DevOps-projekt.  

1. Välj din **Azure-prenumeration**.

1. Välj länken **Ändra** för att se ytterligare Azure-konfigurationsinställningar och identifiera **nodstorleken för den virtuella datorn** och **operativsystemet** för **Service Fabric-klustret**.  Det finns flera olika alternativ här för konfigurering av typ och placering av Azure-tjänster.
 
1. Avsluta Azure-konfigurationsområdet och välj **Klar**.

1. Det tar flera minuter för processen att slutföras.  Ett exempelprogram för ASP.NET Core konfigureras i en lagringsplats i din Azure DevOps Services-organisation, ett Service Fabric-kluster skapas, en CI/CD-pipeline körs och programmet distribueras till Azure.  

    När det är klart läses Azure DevOps **projektinstrumentpanel** in i Azure-portalen.  Du kan också navigera till **instrumentpanelen för Azure DevOps-projektet** direkt från **Alla resurser** i **Azure-portalen**.  

    Den här instrumentpanelen ger insyn i **kodlagringsplatsen** för Azure DevOps Services, **Azure DevOps Services CI/CD-pipeline** och i **Service Fabric-klustret**.  Du kan konfigurera fler alternativ i Azure DevOps Services.  På höger sida av instrumentpanelen väljer du **Bläddra** för att visa det program som körs.

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Granska CI-pipelinen för Azure DevOps Services

Azure DevOps-projektet konfigurerar automatiskt en fullständig Azure DevOps Services CI/CD-pipeline i Azure DevOps Services-organisationen.  Du kan utforska och anpassa pipelinen.  Följ stegen nedan för att bekanta dig med bygg-pipelinen för Azure DevOps Services.

1. Navigera till **instrumentpanelen för Azure DevOps-projektet**.

1. Välj **Skapa pipelines** **längst upp** på **instrumentpanelen för Azure DevOps-projektet**.  Den här länken öppnar en flik i webbläsaren och öppnar bygg-pipelinen för Azure DevOps Services för det nya projektet.

1. Flytta markören till höger om bygg-pipelinen bredvid fältet **Status**. Välj den **ellips** som visas.  Den här åtgärden öppnar en meny där du kan starta flera aktiviteter, till exempel att **lägga till en ny version i en kö**, **pausa en version** och **redigera bygg-pipelinen**.

1. Välj **Redigera**.

1. Från den här vyn **granskar du de olika uppgifterna** för bygg-pipelinen.  Versionen kör olika uppgifter som att hämta källor från Git-lagringsplatsen för Azure DevOps Services, återställa beroenden och publicera utdata för distributioner.

1. Välj **bygg-pipelinens namn** längst upp i bygg-pipelinen. 

1. Under ditt bygg-pipelinenamn väljer du **Historik**.  Du kan se en spårningslogg över de senaste ändringarna för versionen.  Azure DevOps Services spårar alla ändringar som görs av bygg-pipelinen vilket gör att du kan jämföra versioner.

1. Välj **Utlösare**.  Azure DevOps-projektet skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.  Baserat på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Granska CD-pipelinen för Azure DevOps Services

Azure DevOps-projektet skapar och konfigurerar de nödvändiga stegen för att automatiskt distribuera från din Azure DevOps Services-organisation till din Azure-prenumeration.  De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps Services till din Azure-prenumeration.  Automationen skapar också en Azure DevOps Services versionspipeline och den tillhandahåller CD:n till Azure.  Följ stegen nedan för att ta reda på mer om Azure DevOps Services versionspipeline.

1. Välj **Build and Release** (Build-versioner och versioner) och sedan **Versioner**.  Azure DevOps-projektet skapade en Azure DevOps Services-versionspipeline för att hantera distributioner till Azure.

1. På vänster sida i webbläsaren väljer du **ellipsen** bredvid din versionspipeline och sedan väljer du **Redigera**.

1. Versionspipelinen innehåller en **pipeline** som definierar lanseringsprocessen.  Under **Artefakter** väljer du **Släpp**.  Den bygg-pipeline du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **ikonen** **Utlösare av kontinuerlig distribution** (som ser ut som en blixt).  Den här versionspipelinen har en aktiverad CD-utlösare.  Utlösaren startar en distribution varje gång en ny versionsartefakt är tillgänglig.  Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till höger i webbläsaren väljer du **Visa versioner**.  Den här vyn visar en historik över versioner.

1. Välj **ellipsen** bredvid en versionerna och välj **Öppna**.  Det finns flera menyer att utforska från den här vyn, till exempel en **versionssammanfattning**, **tillhörande arbetsobjekt** och **tester**.

1. Välj **Incheckningar**.  Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. Du kan jämföra versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Loggar**.  Loggarna innehåller användbar information om distributionsprocessen.  De kan visas både under och efter distributionerna.

## <a name="commit-changes-to-git-and-automatically-deploy-to-azure"></a>Genomföra ändringar i Git och distribuera automatiskt till Azure 

 > [!NOTE]
 > Stegen nedan testar CI/CD-pipeline med en enkel textändring för din webbapp.

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.  Varje ändring av Git-lagringsplatsen påbörjar en version, och en version distribuerar dina ändringar till Azure.  Följ stegen nedan eller använd andra metoder för att göra ändringar till databasen.  Du kan till exempel **klona** Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. Välj **Kod** och sedan **Filer** från menyn Azure DevOps Services och navigera till din lagringsplats.
1. Navigera till katalogen **Views\Home** och välj **ellipsen** bredvid filen **Index.cshtml**. Välj sedan **redigera** .

1. Gör en ändring i filen, till exempel i texten i en av **div-taggarna**.  Välj **Genomför** i det övre högra hörnet.  Välj **Genomför** igen för att push-överföra ändringen. 

1. Efter en stund **startar en version** och därefter körs en version för att distribuera ändringarna.  Du kan övervaka **build status** (versionstillståndet) med instrumentpanelen för DevOps-projektet eller i webbläsaren med Azure DevOps Services realtidsloggning.

1. När lanseringen har slutförts **uppdaterar du ditt program** i webbläsaren för att kontrollera dina ändringar.

## <a name="clean-up-resources"></a>Rensa resurser

 > [!NOTE]
 > Stegen nedan tar permanent bort resurser.  Använd endast den här funktionen efter att ha läst anvisningarna noga.

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter.  När du inte längre behöver klustret kan du ta bort Azure Service Fabric-klustret och relaterade resurser som skapats i den här självstudien med hjälp av funktionen **Ta bort** på instrumentpanelen för Azure DevOps-projekt.  **Var försiktig** eftersom funktionen Ta bort förstör alla data som skapats av Azure DevOps-projektet i både Azure och Azure DevOps Services. Du kommer inte att kunna återskapa dem när de har tagits bort.

1. Gå till **Azure DevOps-projekt** från **Azure-portalen**.
2. I **övre högra hörnet** av instrumentpanelen väljer du **Ta bort**.  När du har läst dialogrutan väljer du **Ja** för att **permanent ta bort** resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra Azure CI/CD-pipeline för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra projekt.  Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET Core-app och Service Fabric
> * Konfigurera Azure DevOps Services och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera automatiskt till Azure
> * Rensa resurser

Om du vill veta nedan mer om Service Fabric och mikrotjänster:

> [!div class="nextstepaction"]
> [Använda mikrotjänster för att bygga program](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
