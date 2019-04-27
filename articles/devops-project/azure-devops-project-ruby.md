---
title: 'Snabbstart: Skapa en CI/CD-pipeline för Ruby on Rails med hjälp av Azure DevOps Projects'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Du kan lansera en Ruby-webbapp på en Azure-tjänst med några enkla steg.
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
ms.openlocfilehash: 4cf3feeb92f04b4e97cbdc83c539c206790a78c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60558539"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-projects"></a>Skapa en CI/CD-pipeline för Ruby on Rails med hjälp av Azure DevOps Projects

Konfigurera kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din Ruby on Rails-app med hjälp av Azure DevOps Projects. DevOps Projects förenklar den initiala konfigurationen av en Azure DevOps-bygg- och versionspipeline.

Om du inte har en Azure-prenumeration kan du skaffa en kostnadsfritt via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Azure DevOps Projects skapar en CI/CD-pipeline i Azure Repos. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **Skapa en resurs** i fönstret till vänster.

1. I sökrutan skriver du **DevOps Projects** och väljer sedan **Skapa**.

    ![DevOps Projects-instrumentpanelen](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Välj en exempelapp och en Azure-tjänst

1. Välj **Ruby**-exempelappen.

1. Välj **Ruby on Rails**-programramverket. När du är klar väljer du **Nästa**.

1. **Web App on Linux** är distributionsmålet som är standard.  
    Du kan alternativt välja **Web App for Containers**. Det programramverk som du valde tidigare avgör vilken typ av distributionsmål som finns tillgängliga för Azure-tjänsten här. 
    
1. Välj önskad måltjänst och sedan **Nästa**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration 

1. Skapa en ny kostnadsfri Azure DevOps-organisation eller använd en befintlig organisation. 

1. Ange ett namn för ditt Azure DevOps-projekt. 

1. Välj din Azure-prenumeration och plats, ange ett namn för appen och välj sedan **Klar**.  
    Efter några minuter visas DevOps Projects-instrumentpanelen i Azure-portalen. En exempelapp konfigureras i en lagringsplats i din Azure DevOps-organisation, en version körs och appen distribueras till Azure. 
    
    Instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och appen i Azure. Till höger väljer du **Bläddra** för att visa den körande appen.

    ![Instrumentpanelsvy](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Checka in kodändringarna och kör CI/CD

Azure DevOps Projects skapar en Git-lagringsplats i Azure Pipelines eller GitHub. Visa lagringsplatsen och gör kodändringar i appen genom att utföra följande:

1. Till vänster på DevOps Projects-instrumentpanelen väljer du länken för din huvudgren.  
    Länken öppnar en vy till den nyligen skapade Git-lagringsplatsen.

1. Om du vill visa URL för klonen av lagringsplatsen väljer du **Klona** längst upp till höger.  
    Du kan klona Git-lagringsplatsen i din favorit-IDE. I kommande steg kan du använda webbläsaren för att göra och checka in ändringar i koden direkt till huvudgrenen.

1. Längst till vänster, går du till filen *app/views/pages/home.html.erb* och väljer sedan **Redigera**.

1. Gör en ändring i filen. Du kan till exempel ändra text i en av div-taggarna.

1. Välj **Checka in** och spara sedan ändringarna.

1. I webbläsaren går du till DevOps Projects-instrumentpanelen.  
    En version bör pågå. De ändringar du utfört skapas och distribueras automatiskt via en CI/CD-pipeline.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Granska Azure Pipelines-CI/CD-pipelinen

Azure DevOps Projects konfigurerar automatiskt en fullständig Azure CI/CD-pipeline i Azure DevOps-organisationen. Utforska och anpassa pipelinen efter behov. Bekanta dig med Azure DevOps-bygg- och versionspipelines genom att göra följande:

1. Gå till DevOps Projects-instrumentpanelen.

1. Överst på sidan väljer du **Bygg-pipelines**.  
    En webbläsarflik visar bygg-pipelinen för det nya projektet.

1. Peka på fältet **Status** och välj ellipsen (...).  
    En meny med flera alternativ visas, till exempel alternativ för att köa en ny version, pausa en version och redigera bygg-pipelinen.

1. Välj **Redigera**.

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen.  
    Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande och välj alternativet för att **spara och placera i kö**. Välj sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.  
    I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**.  
    DevOps Projects skapar automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.  
    Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**.  
    DevOps Projects skapar en versionspipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) intill din versionspipeline och välj sedan **Redigera**.  
    Versionspipelinen innehåller en *pipeline* som definierar släpprocessen.

1. Under **Artefakter** väljer du **Släpp**.  
    Den bygg-pipeline som du undersökte tidigare skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  
    Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till vänster väljer du **Uppgifter**.  
    Uppgifter är de aktiviteter som distributionsprocessen utför. I det här exemplet skapades en uppgift för att distribuera till Azure App-tjänsten.

1. Till höger väljer du **Visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) intill en version och välj sedan **Öppna**.  
    Du kan utforska flera menyer, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**.  
    Den här vyn visar kodincheckningar som är associerade med den här distributionen. 

1. Välj **Loggar**.  
    Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort Azure App Service-instansen och relaterade resurser som du skapade i den här snabbstarten. Det gör du med funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Se följande om du vill lära dig mer om att ändra dessa bygg- och versionspipelines för att uppfylla behoven i ditt team:

> [!div class="nextstepaction"]
> [Definiera din CD-pipeline med flera steg](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
