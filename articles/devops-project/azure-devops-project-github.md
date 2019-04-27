---
title: 'Självstudier: Skapa en CI/CD-pipeline för din befintliga kod med hjälp av Azure DevOps-projekt'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. DevOps Projects hjälper dig att använda egen kod och din GitHub-lagringsplats för att lansera en app på en Azure-tjänst med några enkla få steg.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 88ee15a3b5cc53542d9e098dee485b8a526bb9a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555358"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Självstudier: Skapa en CI/CD-pipeline för din befintliga kod med hjälp av Azure DevOps-projekt

Azure DevOps Projects ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett exempelprogram för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.

Du kommer att:

> [!div class="checklist"]
> * Använda DevOps Projects för att skapa en CI/CD-pipeline
> * Konfigurera åtkomst till din GitHub-lagringsplats och välja ett ramverk
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure
> * Granska Azure Pipelines-CI/CD-pipelinen
> * Konfigurera övervakning med Azure Application Insights
> * Rensa resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Åtkomst till en GitHub eller en extern Git-lagringsplats som innehåller .NET, Java, PHP, Node, Python eller statisk webbkod.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Azure DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. Azure DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **Nytt** i den vänstra fönsterrutan.

1. I sökrutan skriver du **DevOps Projects** och väljer sedan **Skapa**.

    ![DevOps Projects-instrumentpanelen](_img/azure-devops-project-github/fullbrowser.png)

1. Välj **Ha med din egen kod** och sedan **Nästa**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Konfigurera åtkomst till din GitHub-lagringsplats och välja ett ramverk

1. Välj antingen **GitHub** eller en extern Git-lagringsplats, och välj sedan din lagringsplats och den gren som innehåller programmet.

1. Välj ditt webbramverk och sedan **Nästa**.

    ![.NET-ramverk](_img/azure-devops-project-github/webframework.png)

    Det programramverk som du valde tidigare avgör vilken typ av distributionsmål som finns tillgängliga för Azure-tjänsten här. 
    
1. Välj måltjänsten och sedan **Nästa**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration 

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation.

    a. Ange ett namn för ditt projekt i Azure DevOps. 
    
    b. Välj din Azure-prenumeration och plats, ange ett namn för programmet och välj sedan **Klar**.

    Efter några minuter visas DevOps Projects-instrumentpanelen i Azure-portalen. Ett exempelprogram konfigureras i en lagringsplats i din Azure DevOps-organisation, en version körs och programmet distribueras till Azure. Den här instrumentpanelen ger insyn i din GitHub-kodlagringsplats, din CI/CD-pipeline och i ditt program i Azure. 
    
1. Visa ditt körande program genom att välja **Bläddra**.

    ![Vy av DevOps Projects-instrumentpanel](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects konfigurerar automatiskt en CI-version och versionsutlösare. Koden finns kvar i din GitHub-lagringsplats eller annan extern lagringsplats. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure 

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats. Varje ändring i GitHub-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure.

1. Gör en ändring i ditt program och checka sedan in ändringen till GitHub-lagringsplatsen.  
    Efter en liten stund startar en version i Azure Pipelines. Du kan övervaka versionsstatusen på DevOps Projects-instrumentpanelen eller i webbläsaren med din Azure DevOps-organisation.

1. När versionen har slutförts uppdaterar du programmet för att verifiera dina ändringar.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Granska Azure Pipelines-CI/CD-pipelinen

Azure DevOps Projects konfigurerar automatiskt en CI/CD-pipeline i Azure Pipelines. Utforska och anpassa pipelinen efter behov. Utför följande för att bekanta dig med bygg- och versionspipelines:

1. Välj **Bygg-pipelines** längst upp på DevOps Projects-instrumentpanelen.  
    En webbläsarflik visar bygg-pipelinen för det nya projektet.

1. Peka på fältet **Status** och välj ellipsen (...).  
    En meny med flera alternativ visas, till exempel alternativ för att köa en ny version, pausa en version och redigera bygg-pipelinen.

1. Välj **Redigera**.

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen.  
    Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande och välj alternativet för att **spara och placera i kö**. Välj sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.  
    Du kan se en spårningslogg över de senaste ändringarna för versionen. Azure DevOps spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**.  
    Azure DevOps Projects skapar automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.  
        Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**.  
    Azure DevOps Projects skapar en versionspipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) intill din versionspipeline och välj sedan **Redigera**.  
    Versionspipelinen innehåller en *pipeline* som definierar släpprocessen. 
    
1. Under **Artefakter** väljer du **Släpp**.  
    Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. Intill ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  
    Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till vänster väljer du **Uppgifter**.  
    Uppgifter är de aktiviteter som distributionsprocessen kör. I det här exemplet skapades en uppgift för att distribuera till Azure App-tjänsten.

1. Till höger väljer du **Visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) intill en version och välj sedan **Öppna**.  
    Det finns flera menyer att utforska, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**.  
    Den här vyn visar kodincheckningar som är associerade med den här distributionen. 

1. Välj **Loggar**.  
    Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurera övervakning med Azure Application Insights

Med Azure Application Insights kan du enkelt övervaka ett programs prestanda och användning. Azure DevOps Projects konfigurerar automatiskt en Application Insights-resurs för ditt program. Du kan konfigurera ytterligare aviseringar och övervakningsfunktioner efter behov.

1. Gå till DevOps Projects-instrumentpanelen i Azure-portalen. 

1. Nere till höger väljer du **Application Insights**-länken för din app.  
    Fönsterrutan **Application Insights** öppnas. Den här vyn innehåller övervakningsinformation om användning, prestanda och tillgänglighet för din app.

    ![Fönsterrutan Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Välj **Tidsintervall** och sedan **Senaste timmen**. Välj **Uppdatera** för att filtrera resultaten.  
    Nu kan du se all aktivitet från de senaste 60 minuterna. Om du vill avsluta tidsintervallet väljer du **x**.

1. Välj **Aviseringar** och sedan **Lägg till metrisk varning**. 

1. Ange ett namn för aviseringen.

1. I listrutan **Source Alter on** (Ändring av källa: På) väljer du din **App Service-resurs.** <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. I listrutan **Mått** undersöker du de olika måtten för aviseringar.  
    Standardaviseringen är för en **serversvarstid som är större än 1 sekund**. Du kan enkelt konfigurera en mängd olika aviseringar för att förbättra övervakningsfunktionerna i din app.

1. Välj kryssrutan **Meddela via e-postägare, deltagare och läsare**.  
    Alternativt kan du utföra ytterligare åtgärder när en avisering visas genom att köra Azure-logikapp.

1. Skapa aviseringen genom att välja **OK**.  
    Efter en liten stund visas aviseringen som aktiv på instrumentpanelen.
    
1. Avsluta området **Aviseringar** och gå tillbaka till fönsterrutan **Application Insights**.

1. Välj **Tillgänglighet** och sedan **Lägg till test**. 

1. Ange ett testnamn och välj sedan **Skapa**.  
    Ett enkelt pingtest skapas för att kontrollera tillgängligheten för ditt program. Testresultaten blir tillgängliga efter några minuter och Application Insights-instrumentpanelen visar en tillgänglighetsstatus.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort Azure App-databasen och relaterade resurser som du skapade i den här självstudien. Det gör du med funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade CI/CD-processen i den här självstudien skapades en bygg- och versionspipeline automatiskt i Azure DevOps Projects. Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du har lärt dig att:

> [!div class="checklist"]
> * Använda DevOps Projects för att skapa en CI/CD-pipeline
> * Konfigurera åtkomst till din GitHub-lagringsplats och välja ett ramverk
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure
> * Granska Azure Pipelines-CI/CD-pipelinen
> * Konfigurera övervakning med Azure Application Insights
> * Rensa resurser

Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Definiera din CD-pipeline med flera steg](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
