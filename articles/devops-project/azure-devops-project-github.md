---
title: 'Självstudie: Skapa en CI/CD-pipeline för din befintliga kod med hjälp av Azure DevOps Projects'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. I några snabba steg hjälper DevOps Projects dig att använda din egen kod och GitHub-repo för att starta en app på en Azure-tjänst.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: a0496999a9a5dc3e9bfd57df0ec035e6db77d620
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73615133"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Självstudie: Skapa en CI/CD-pipeline för din befintliga kod med hjälp av Azure DevOps Projects

Azure DevOps Projects presenterar en förenklad process för att skapa en kontinuerlig integration (CI) och cd-pipeline (continuous delivery) till Azure. Du kan ta med din befintliga kod och Git repo, eller så kan du välja ett exempelprogram.

Du kommer att:

> [!div class="checklist"]
> * Använda DevOps Projects för att skapa en CI/CD-pipeline
> * Konfigurera åtkomst till din GitHub-lagringsplats och välja ett ramverk
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure
> * Granska Azure Pipelines-CI/CD-pipelinen
> * Rensa resurser

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Åtkomst till en GitHub- eller extern Git-repa som innehåller .NET, Java, PHP, Node.js, Python eller statisk webbkod.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Azure DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. Azure DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. På Portal-menyn i Azure väljer du **Skapa en resurs**.

   ![Azure portal-menyn - Skapa en resurs](_img/azure-devops-project-github/createaresource.png)

3. Välj **DevOps** > **DevOps Project**.

   ![DevOps Projects-instrumentpanelen](_img/azure-devops-project-github/azuredashboard.png)

1. Välj **Ha med din egen kod** och sedan **Nästa**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Konfigurera åtkomst till GitHub-repon och välj ett ramverk

1. Välj antingen **GitHub** eller en extern **Git-koddatabas.** För den här självstudien väljer du **GitHub**. Du kan behöva autentisera med GitHub första gången för att azure ska kunna komma åt din GitHub-databas.

1. Välj en **databas** och en **gren**och välj sedan **Nästa**.

1. Om du använder Docker-behållare ändrar **du Är app Dockerized** till **YES**. För den här självstudien lämnar du **NEJ** markerad och väljer sedan **Nästa**. Om du vill ha mer information om hur du använder Docker-behållare håller du muspekaren över **i-ikonen.**

   ![Val av programramverk på rullgardinsmenyn](_img/azure-devops-project-github/appframework.png)

1. På de nedrullningsbara menyerna väljer du en **programkörning** och ett **programramverk**och väljer sedan **Nästa**. Programramverket dikterar vilken typ av Azure-tjänstdistributionsmål som är tillgängligt.

1. Välj en **Azure-tjänst** för att distribuera programmet och välj sedan **Nästa**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Ange ett namn på **Projektnamn**.

1. Skapa en ny kostnadsfri organisation i **Azure DevOps Organization** eller välj en befintlig organisation på den nedrullningsbara menyn.

1. Välj din prenumeration i **Azure-prenumeration**och ange antingen ett namn i **webbapp** eller använd standardvärdet. Välj en **plats**och välj sedan **Klar**. Efter några minuter visas distributionsöversikten för DevOps-projekt i Azure-portalen.

1. Välj **Gå till resurs** om du vill visa instrumentpanelen DevOps Projects. I det övre högra hörnet fäster du **project på** instrumentpanelen för snabb åtkomst. Azure DevOps Projects konfigurerar automatiskt en CI-version och versionsutlösare. Koden finns kvar i din GitHub-repo eller en annan extern repo och en exempelapp har konfigurerats i en repo i **Azure DevOps Organization**. Azure DevOps Projects kör bygget och distribuerar appen till Azure.

   ![Instrumentpanelsvyn Azure DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. Instrumentpanelen visar din kodåterupplag, din CI/CD-pipeline och din app i Azure. Till höger, under Azure-resurser, väljer **Du Bläddra** för att visa din app som körs.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure

Nu är du redo att samarbeta i din app med ett team. CI/CD-processen distribuerar automatiskt ditt senaste arbete till din webbplats. Varje ändring av GitHub-repo startar en version i Azure DevOps och en CD-pipeline kör en distribution till Azure.

1. Välj **Databaser på**instrumentpanelen DevOps Projects . Din GitHub-databas öppnas på en ny webbläsarflik. Gör en ändring i ditt program och välj sedan **Genomför ändringar**.

1. Efter en liten stund startar en version i Azure Pipelines. Du kan övervaka byggstatusen på instrumentpanelen DevOps Projects. Du kan också övervaka den i din Azure DevOps-organisation genom att välja fliken **Bygg pipelines** på instrumentpanelen DevOps Projects.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Granska Azure Pipelines-CI/CD-pipelinen

Azure DevOps Projects konfigurerar automatiskt en CI/CD-pipeline i Azure Pipelines. Utforska och anpassa pipelinen efter behov. Utför följande för att bekanta dig med bygg- och versionspipelines:

1. Välj **Bygg pipelines**på instrumentpanelen DevOps Projects .

1. När sidan **Azure Pipelines** har öppnats visas en historik över de senaste versionerna och statusen för varje version.

   ![Sidan Azure Pipelines bygger](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. I det övre högra hörnet på sidan **Byggen** kan du välja **Redigera** om du vill ändra den aktuella versionen, **Kö** för att lägga till en ny version eller den lodräta ellipsknappen (**&#8942;**) för att öppna en meny med fler alternativ. Välj **Redigera**.

1. Build utför olika uppgifter, till exempel hämta källor från osipo, återställa beroenden och publicera utdata för distributioner. Till höger, under **Namn**, ändra byggpipellinenamnet till något mer beskrivande. Välj **Spara & kö**och välj sedan **Spara**. Ange en kommentar och välj sedan **Spara** igen.

   ![Sidan Azure DevOps bygger](_img/azure-devops-project-github/buildpage.png)

1. Om du vill se en granskningsspårning över de senaste ändringarna för bygget väljer du fliken **Historik.**  Azure DevOps spårar alla ändringar som gjorts i byggpipelinen och låter dig jämföra versioner.

1. Välj fliken **Utlösare.** Azure DevOps-projekt skapar automatiskt en CI-utlösare med vissa standardinställningar. Du kan ange utlösare som **Aktivera kontinuerlig integrering** för att köra en version varje gång du genomför en kodändring. Du kan också ställa in utlösare för att schemalägga byggen att köras vid specifika tidpunkter.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver Azure App Service och relaterade resurser som du skapade i den här självstudien kan du ta bort dem. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade CI/CD-processen i den här självstudien skapade du automatiskt en pipeline för bygg- och utgivning i Azure DevOps-projekt. Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov.

Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Definiera din CD-pipeline med flera steg](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Mer information om programövervakning finns i:
  
 > [!div class="nextstepaction"]
 > [Vad är Azure-övervakare?](https://docs.microsoft.com/azure/azure-monitor/overview)
