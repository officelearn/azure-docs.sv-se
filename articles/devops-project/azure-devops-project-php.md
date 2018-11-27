---
title: 'Snabbstart: Skapa en CI/CD-pipeline för PHP med Azure DevOps Projects'
description: DevOps Projects gör det enkelt att komma igång med Azure. Det hjälper dig att starta en app på en Azure-tjänst med några enkla få steg.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 24a957260e3e3f161ce9e2e61b746f9b052e784d
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264377"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-projects"></a>Skapa en CI/CD-pipeline för PHP med Azure DevOps Projects

Azure DevOps Projects ger ett förenklat sätt att skapa Azure-resurser och konfigurerar en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din PHP-app i Azure Pipelines.  

Om du inte har en Azure-prenumeration kan du skaffa en kostnadsfritt via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

 DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny kostnadsfri Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).

1. I den vänstra fönsterrutan väljer du ikonen **Skapa en resurs** och söker sedan efter **DevOps Projects**.  

3. Välj **Skapa**.

    ![Starta konfiguration av kontinuerlig leverans](_img/azure-devops-project-php/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogram och en Azure-tjänst

1. Välj PHP-exempelprogrammet.  
        PHP-exemplet innehåller ett val av flera programramverk. Det exempelramverk som är standard är Laravel. 
        
2. Låt standardinställningen vara och välj **Nästa**.  

1. Web App for Containers är det distributionsmål som är standard.  
    Det programramverk som du valde tidigare avgör vilken typ av distributionsmål som finns tillgängliga för Azure-tjänsten här.  Låt standardinställningen för tjänsten vara och välj **Nästa**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration 

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation. 

    a. Välj ett namn för ditt projekt i Azure DevOps. 
    
    b. Välj din Azure-prenumeration och plats, ange ett namn för programmet och välj sedan **Klar**.   
        Efter några minuter visas DevOps Projects-instrumentpanelen i Azure-portalen. Ett exempelprogram konfigureras i en lagringsplats i Azure DevOps-organisationen, en version körs och programmet distribueras till Azure. Den här instrumentpanelen ger insyn i din kodlagringsplats, din CI/CD-pipeline och ditt program i Azure.  
        
2. Visa ditt körande program genom att välja **Bläddra**.

    ![Instrumentpanelsvy](_img/azure-devops-project-php/dashboardnopreview.png) 
    
 DevOps Projects konfigurerade automatiskt en CI-version och versionsutlösare.  Nu är du redo att samarbeta med ett team på en PHP-app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

 DevOps Projects skapar en Git-lagringsplats i Azure Repos eller GitHub. Visa lagringsplatsen och gör kodändringar i programmet genom att utföra följande steg:

1. Till vänster om DevOps Projects-instrumentpanelen väljer du länken för din huvudgren.   
    Den här länken öppnar en vy till den nyligen skapade Git-lagringsplatsen.

1. Om du vill visa webbadressen för den klonade lagringsplatsen väljer du **Klona** längst upp till höger i webbläsaren.   
    Du kan klona Git-lagringsplatsen till din favorit-IDE. I kommande steg använder du webbläsaren för att göra och checka in ändringar i koden direkt till huvudgrenen.

1. Till vänster går du till filen **resources/views/welcome.blade.php**.

1. Välj **Redigera** och gör sedan en ändring i en del av texten.  Du kan till exempel ändra en del av texten för en av div-taggarna.

1. Välj **Checka in** och spara sedan ändringarna.

1. I webbläsaren går du till DevOps Projects-instrumentpanelen.  
Du bör nu se att en version håller på att skapas. De ändringar som du just utfört skapas och distribueras automatiskt via en CI/CD-pipeline.

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

 DevOps Projects konfigurerar automatiskt en fullständig CI/CD-pipeline i Azure Pipelines. Utforska och anpassa pipelinen efter behov. Utför följande för att bekanta dig med bygg- och versionspipelines:

1. Välj **Bygg-pipelines** längst upp på DevOps Projects-instrumentpanelen.  
    Länken öppnar en webbläsarflik och bygg-pipelinen för det nya projektet.

1. Peka på fältet **Status** och välj **ellipsen** (...).  
    En meny med flera alternativ visas, till exempel alternativ för att köa en ny version, pausa en version och redigera bygg-pipelinen.

1. Välj **Redigera**.

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen.  
    Versionen kör olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata som används för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande och välj **Spara och köa** och sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.   
    I fönsterrutan **Historik** visas en spårningslogg över de senaste ändringarna för versionen. Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**.  
      DevOps Projects skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.   
    Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**.  
     DevOps Projects skapar en versionspipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) intill din versionspipeline och välj sedan **Redigera**.  
    Versionspipelinen innehåller en pipeline som definierar versionsprocessen. 

12. Under **Artefakter** väljer du **Släpp**.  
    Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. Intill ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.   
    Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig.  Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till vänster väljer du **Uppgifter**.  
        Uppgifter är de aktiviteter som distributionsprocessen utför.  I det här exemplet skapades en uppgift för att distribuera till Azure App-tjänsten.

1. Till höger väljer du **Visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) intill en av versionerna och välj **Öppna**.  
        Det finns flera menyer att utforska från den här vyn, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**.  
        Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. 

1. Välj **Loggar**.  
        Loggarna innehåller användbar information om distributionsprocessen. De kan visas både under och efter distributionerna.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser när de inte längre behövs. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade CI/CD-processen skapades bygg- och versionspipelines automatiskt. Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Mer information om CI/CD-pipelinen finns i den här självstudien:

> [!div class="nextstepaction"]
> [Anpassa CD-process](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
