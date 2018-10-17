---
title: Distribuera din ASP.NET Core-app till Azure Kubernetes Service (AKS) med Azure DevOps-projekt | Azure DevOps-självstudier
description: Azure DevOps-projektet gör det enkelt att komma igång med Azure. Azure DevOps-projektet gör det enkelt att distribuera din ASP.NET Core-app med Azure Kubernetes Service (AKS) i några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 55ea101b3a03fdb7fc375c4594cab36d4cd79978
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299125"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-kubernetes-service-aks-with-the-azure-devops-project"></a>Självstudie: Distribuera din ASP.NET Core-app till Azure Kubernetes Service (AKS) med Azure DevOps-projekt

Azure DevOps-projektet ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett av exempelprogrammen för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.  DevOps-projektet skapar automatiskt Azure-resurser som AKS, skapar och konfigurerar en versionspipeline i Azure DevOps Services som omfattar en versionspipeline för CI/CD och skapar sedan en Azure Application Insights-resurs för övervakning.

Du kommer att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET Core-app och AKS
> * Konfigurera Azure DevOps Services och en Azure-prenumeration 
> * Granska AKS-kluster
> * Granska CI-pipelinen för Azure DevOps Services
> * Granska CD-pipelinen för Azure DevOps Services
> * Genomföra ändringar i Git och distribuera automatiskt till Azure
> * Rensa resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-aks"></a>Skapa ett Azure DevOps-projekt för en ASP.NET Core-app och AKS

Azure DevOps-projektet skapar en CI/CD-pipeline i Azure.  Du kan skapa en **ny Azure DevOps Services-organisation** eller använda en **befintlig organisation**.  Azure DevOps-projektet skapar även **Azure-resurser** som ett AKS-kluster i den **Azure-prenumeration** som du väljer.

1. Logga in i [Microsoft Azure-portalen](https://portal.azure.com).

1. Välj ikonen **Skapa en resurs** i det vänstra navigeringsfältet och sök efter **DevOps-projekt**.  Välj **Skapa**.

    ![Startar kontinuerlig leverans](_img/azure-devops-project-aks/fullbrowser.png)

1. Välj **.NET** och välj sedan **Nästa**.

1. För **Välj ett programramverk** väljer du **ASP.NET Core**. Välj sedan **Nästa**.

1. Välj **Kubernetes-tjänst** och sedan **Nästa**.  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Konfigurera Azure DevOps Services och en Azure-prenumeration

1. Skapa en **ny** Azure DevOps-organisation eller använd en **befintlig** organisation.  Välj ett **namn** för ditt projekt.  

1. Välj din **Azure-prenumeration**.

1. Välj länken **Ändra** för att se ytterligare Azure-konfigurationsinställningar och identifiera **antalet noder** för **Kubernetes-klustret**.  Det finns flera olika alternativ här för konfigurering av typ och placering av Azure-tjänster.
 
1. Avsluta Azure-konfigurationsområdet och välj **Klar**.

1. Det tar flera minuter för processen att slutföras.  Ett exempelprogram för ASP.NET Core konfigureras i en Git-lagringsplats för Azure Repos i din Azure DevOps Services-organisation, ett AKS-kluster skapas, en CI/CD-pipeline körs och programmet distribueras till Azure.  

    När det är klart läses Azure DevOps **projektinstrumentpanel** in i Azure-portalen.  Du kan också navigera till **instrumentpanelen för Azure DevOps-projektet** direkt från **Alla resurser** i **Azure-portalen**.  

    Den här instrumentpanelen ger insyn i **kodlagringsplatsen** för Azure Repos, **Azure DevOps Services CI/CD-pipeline** och i **AKS-klustret**.  Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps Services-pipeline.  På höger sida av instrumentpanelen väljer du **Bläddra** för att visa det program som körs.

## <a name="examine-the-aks-cluster"></a>Granska AKS-kluster

Azure DevOps-projektet konfigurerar automatiskt ett AKS-kluster.  Du kan utforska och anpassa klustret.  Följ stegen nedan för att bekanta dig med AKS.

1. Navigera till **instrumentpanelen för Azure DevOps-projektet**.

1. På höger sida av instrumentpanelen för DevOps-projektet väljer du **Kubernetes-tjänsten**.

1. Ett blad öppnas för AKS-klustret.  Från den här vyn kan du utföra olika åtgärder som att **övervaka hälsotillstånd för behållare**, **söka i loggar** och öppna **Kubernetes-instrumentpanelen**.

1. På höger sida av skärmen väljer du **visa Kubernetes-instrumentpanelen**.  Du kan också följa stegen för att öppna Kubernetes-instrumentpanelen.

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Granska CI-pipelinen för Azure DevOps Services

Azure DevOps-projektet konfigurerar automatiskt en Azure CI/CD-pipeline i Azure DevOps-organisationen.  Du kan utforska och anpassa pipelinen.  Följ stegen nedan för att bekanta dig med CI/CD-pipelinen för Azure DevOps Services.

1. Navigera till **instrumentpanelen för Azure DevOps-projektet**.

1. Välj **Skapa pipelines** **längst upp** på **instrumentpanelen för Azure DevOps-projektet**.  Den här länken öppnar en flik i webbläsaren och öppnar bygg-pipelinen för Azure DevOps Services för det nya projektet.

1. Flytta markören till höger om bygg-pipelinen bredvid fältet **Status**. Välj den **ellips** som visas.  Den här åtgärden öppnar en meny där du kan utföra flera aktiviteter, till exempel att **lägga till en ny version i en kö**, **pausa en version** och **redigera bygg-pipelinen**.

1. Välj **Redigera**.

1. Från den här vyn **granskar du de olika uppgifterna** för din.  Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen för Azure DevOps Services Repos, återställa beroenden och publicera utdata för distributioner.

1. Välj **bygg-pipelinens namn** längst upp i bygg-pipelinen.

1. Ändra **namnet** på din bygg-pipeline till något mer beskrivande.  Välj **Save & queue** (Spara och köa) och välj sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.  Du kan se en spårningslogg över de senaste ändringarna för versionen.  Azure DevOps Services spårar alla ändringar som görs av bygg-pipelinen vilket gör att du kan jämföra versioner.

1. Välj **Utlösare**.  Azure DevOps-projektet skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.  Baserat på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

## <a name="examine-the-azure-devops-services-cd-release-pipeline"></a>Granska CD-versionspipelinen för Azure DevOps Services

Azure DevOps-projektet skapar och konfigurerar de nödvändiga stegen för att automatiskt distribuera från din Azure DevOps Services-organisation till din Azure-prenumeration.  De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps Services med din Azure-prenumeration.  Automationen skapar också en Azure DevOps Services versionspipeline och versionspipelinen tillhandahåller CD:n till Azure.  Följ stegen nedan för att ta reda på mer om Azure DevOps Services versionspipeline.

1. Välj **Build and Release** (Build-versioner och versioner) och sedan **Versioner**.  Azure DevOps-projektet skapade en Azure DevOps Services-versionspipeline för att hantera distributioner till Azure.

1. På vänster sida i webbläsaren väljer du **ellipsen** bredvid din versionspipeline och sedan väljer du **Redigera**.

1. Versionspipelinen innehåller en **pipeline** som definierar lanseringsprocessen.  Under **Artefakter** väljer du **Släpp**.  Den bygg-pipeline du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **ikonen** **Utlösare av kontinuerlig distribution** (som ser ut som en blixt).  Den här versionspipelinen har en aktiverad CD-utlösare.  Utlösaren skapar en distribution varje gång en ny versionsartefakt är tillgänglig.  Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till höger i webbläsaren väljer du **Visa versioner**.  Den här vyn visar en historik över versioner.

1. Välj **ellipsen** bredvid en versionerna och välj **Öppna**.  Det finns flera menyer att utforska från den här vyn, till exempel en **versionssammanfattning**, **tillhörande arbetsobjekt** och **tester**.

1. Välj **Incheckningar**.  Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. Du kan jämföra versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Loggar**.  Loggarna innehåller användbar information om distributionsprocessen.  De kan visas både under och efter distributionerna.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Genomför ändringar av Azure DevOps Services och distribuera automatiskt till Azure 

 > [!NOTE]
 > Stegen nedan testar CI/CD-pipeline med en enkel textändring för din webbapp.

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.  Varje ändring i Git-lagringsplatsen för Azure DevOps Services startar en version i Azure DevOps Services och en CD-pipeline distribuerar dina ändringar till Azure.  Följ stegen nedan eller använd andra metoder för att göra ändringar till databasen.  Du kan till exempel **klona** Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. Välj **Kod** och sedan **Filer** från menyn Azure DevOps Services och navigera till din lagringsplats.
1. Navigera till katalogen **Views\Home** och välj **ellipsen** bredvid filen **Index.cshtml**. Välj sedan **redigera** .

1. Gör en ändring i filen, till exempel i texten i en av **div-taggarna**.  Välj **Genomför** i det övre högra hörnet.  Välj **Genomför** igen för att push-överföra ändringen. 

1. Efter en stund **startar en version i Azure DevOps Services** och därefter körs en version för att distribuera ändringarna.  Du kan övervaka **build status** (versionstillståndet) med instrumentpanelen för DevOps-projektet eller i webbläsaren med din Azure DevOps Services-organisation.

1. När lanseringen har slutförts **uppdaterar du ditt program** i webbläsaren för att kontrollera dina ändringar.

## <a name="clean-up-resources"></a>Rensa resurser

 > [!NOTE]
 > Stegen nedan tar permanent bort resurser.  Använd endast den här funktionen efter att ha läst anvisningarna noga.

Om du testar kan du rensa bland resurserna för att undvika att skapa fler fakturor.  När du inte längre behöver klustret kan du ta bort Azure Kubernetes-klustret och relaterade resurser som skapats i den här självstudien med hjälp av funktionen **Ta bort** på instrumentpanelen för Azure DevOps-projekt.  **Var försiktig** eftersom funktionen Ta bort förstör alla data som skapats av Azure DevOps-projektet i både Azure och Azure DevOps Services. Du kommer inte att kunna återskapa dem när de har tagits bort.

1. Gå till **Azure DevOps-projekt** från **Azure-portalen**.
2. I **övre högra hörnet** av instrumentpanelen väljer du **Ta bort**.  När du har läst dialogrutan väljer du **Ja** för att **permanent ta bort** resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines.  Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure DevOps-projekt för en ASP.NET Core-app och AKS
> * Konfigurera Azure DevOps Services och en Azure-prenumeration 
> * Granska AKS-kluster
> * Granska CI-pipelinen för Azure DevOps Services
> * Granska CD-pipelinen för Azure DevOps Services
> * Genomföra ändringar i Git och distribuera automatiskt till Azure
> * Rensa resurser

Mer information om att använda Kubernetes-instrumentpanelen finns nedan:

> [!div class="nextstepaction"]
> [Använda Kubernetes-instrumentpanelen](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
