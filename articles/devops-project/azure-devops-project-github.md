---
title: 'Självstudie: Skapa en CI/CD-pipeline för din befintliga kod med hjälp av Azure DevOps Projects'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. DevOps Projects hjälper dig att använda egen kod och din GitHub-lagringsplats för att lansera en app på en Azure-tjänst med några enkla få steg.
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
ms.openlocfilehash: 15ac201a078864717d8e0079801507cf5fc0fe3b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481118"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Självstudie: Skapa en CI/CD-pipeline för din befintliga kod med hjälp av Azure DevOps Projects

Azure DevOps Projects ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett exempelprogram för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.

Du kommer att:

> [!div class="checklist"]
> * Använda DevOps Projects för att skapa en CI/CD-pipeline
> * Konfigurera åtkomst till din GitHub-lagringsplats och välja ett ramverk
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure
> * Granska Azure Pipelines-CI/CD-pipelinen
> * Rensa resurser

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Åtkomst till en GitHub eller en extern Git-lagringsplats som innehåller .NET, Java, PHP, Node, Python eller statisk webbkod.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Azure DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. Azure DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Från Azure Portal-menyn väljer du **skapa en resurs**.

   ![Azure Portal-menyn – skapa en resurs](_img/azure-devops-project-github/createaresource.png)

3. Välj **DevOps > DevOps-projekt**.

   ![DevOps Projects-instrumentpanelen](_img/azure-devops-project-github/azuredashboard.png)

3. Välj **ta med din egen kod**och välj sedan **Nästa**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Konfigurera åtkomst till din GitHub-lagringsplats och välja ett ramverk

1. Välj antingen **GitHub** eller en extern **git** Code-lagringsplats. I den här självstudien väljer du **GitHub**. Du kan behöva autentisera med GitHub första gången så att Azure kan komma åt din GitHub-lagringsplats.

2. Slutför genom att välja **databasen** och **grenen**, Välj **Nästa**.

3. Om du använder Docker-behållares ändring **är app Dockerized** till **Ja**, för den här självstudien lämnar du **inte** markerat, väljer du **Nästa**. Mer information om hur du använder Docker containers hovring över **i** -ikonen.

   ![.NET-ramverk](_img/azure-devops-project-github/appframework.png)

4. Välj **program körning** och **ramverk**i list rutorna och välj sedan **Nästa**. Program ramverket som du väljer avgör vilken typ av distributions mål för Azure-tjänsten som är tillgänglig.

5. Välj den **Azure-tjänst** som du vill distribuera programmet till och välj sedan **Nästa**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Ange ett **projekt namn**.

2. Skapa en ny kostnads fri **Azure DevOps-organisation** eller Välj en befintlig organisation i list rutan.

3. Välj din **Azure-prenumeration**, ange ett namn för din **webbapp** eller gör standardvärdet. Välj en **plats**och välj sedan **Slutför**. Efter några minuter visas översikten över DevOps-projekt distribution i Azure Portal.

4. Välj **gå till resurs** om du vill visa DevOps-instrumentpanelen för projektet. I det övre högra hörnet fäster du **projektet** på instrument panelen för snabb åtkomst. Azure DevOps Projects konfigurerar automatiskt en CI-version och versionsutlösare. Koden finns kvar i din GitHub-lagringsplats eller annan extern lagringsplats. En exempel app har kon figurer ATS i en lagrings platsen i din **Azure DevOps-organisation**. En version körs och din app distribueras till Azure.

   ![Vy av DevOps Projects-instrumentpanel](_img/azure-devops-project-github/projectsdashboard.png)

5. Instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och appen i Azure. Välj **Bläddra** till höger under Azure-resurser för att Visa appen som körs.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats. Varje ändring i GitHub-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure.

1. Från DevOps-projektets instrument panel väljer du **databaser**. Din GitHub-lagringsplats öppnas i en ny flik i webbläsaren. gör en ändring i programmet och klicka sedan på **genomför ändringar**.

2. Efter en liten stund startar en version i Azure Pipelines. Du kan övervaka build-statusen i DevOps Projects instrument panelen eller övervaka den i din Azure DevOps-organisation genom att välja fliken **Bygg pipelines** på instrument panelen för projektet.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Granska Azure Pipelines-CI/CD-pipelinen

Azure DevOps Projects konfigurerar automatiskt en CI/CD-pipeline i Azure Pipelines. Utforska och anpassa pipelinen efter behov. Utför följande för att bekanta dig med bygg- och versionspipelines:

1. Välj **Bygg pipelines**i DevOps Projects instrument panelen.

2. När sidan för **Azure-pipelinen** öppnas visas en historik över de senaste build-versionerna och status för varje version.

   ![Pipeline-versioner för Azure DevOps](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. I det övre högra hörnet på sidan **versioner** ser du alternativ för att **Redigera** den aktuella versionen, **kön** för att köa en ny version och tre punkter ( **&#8942;** ) för att öppna en meny med fler alternativ, Välj **Redigera**.

4. Versionen utför olika uppgifter, till exempel hämtning av källor från lagrings platsen, återställning av beroenden och publicering av utdata för distributioner. Till höger under **namn**ändrar du namnet på bygg pipelinen till något mer beskrivande. Välj **spara & kö**, **Spara**, lämna en kommentar och välj sedan **Spara** igen.

   ![Sidan Azure DevOps-versioner](_img/azure-devops-project-github/buildpage.png)

5. Om du vill se en gransknings historik för de senaste ändringarna för versionen väljer du fliken **Historik** . Azure-DevOps spårar alla ändringar som gjorts i build-pipeline och gör att du kan jämföra versioner.

6. Välj fliken **utlösare** . Azure DevOps Project skapar automatiskt en CI-utlösare med vissa standardinställningar. Utlösare som **aktivera kontinuerlig integrering** kan ställas in för att köra en version varje gång en kod ändring har genomförts, eller schemaläggning av versioner för att köras vid specifika tidpunkter.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort Azure App-databasen och relaterade resurser som du skapade i den här självstudien. Det gör du med funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade CI/CD-processen i den här självstudien skapades en bygg- och versionspipeline automatiskt i Azure DevOps Projects. Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du har lärt dig att:

> [!div class="checklist"]
>  * Använda DevOps Projects för att skapa en CI/CD-pipeline
> * Konfigurera åtkomst till din GitHub-lagringsplats och välja ett ramverk
> * Konfigurera Azure DevOps och en Azure-prenumeration
> * Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure
> * Granska Azure Pipelines-CI/CD-pipelinen
> * Rensa resurser

Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Definiera din CD-pipeline med flera steg](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Mer information om program övervakning finns i:
  
 > [!div class="nextstepaction"]
 > [Vad är Azure-övervakaren?](https://docs.microsoft.com/azure/azure-monitor/overview)
