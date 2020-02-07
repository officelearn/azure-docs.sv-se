---
title: 'Snabb start: skapa en CI/CD-pipeline för Java-Azure DevOps Projects'
description: DevOps Projects gör det enkelt att komma igång med Azure. Det hjälper dig att starta en app på en Azure-tjänst med några enkla få steg.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
monikerRange: vsts
ms.openlocfilehash: 1a276770887bee39972ba8630fb13f52bcbe802d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049951"
---
# <a name="quickstart-set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-projects"></a>Snabb start: Konfigurera en CI/CD-pipeline för en Java-app med Azure DevOps Projects

I den här snabb starten använder du den förenklade Azure DevOps Projects upplevelsen för att ställa in en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din Java-app i Azure-pipeline. Du kan använda Azure DevOps Projects för att ställa in allt du behöver för att utveckla, distribuera och övervaka din app. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Ett [Azure DevOps](https://azure.microsoft.com/services/devops/) -konto och en organisation.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj **skapa en resurs**i den vänstra rutan. 

   ![Skapa en Azure-resurs i Azure Portal](_img/azure-devops-project-java/continuous-delivery-configuration-full-browser.png)

1. Sök efter och välj **DevOps Projects**och välj sedan **skapa**.

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogram och en Azure-tjänst

1. Välj Java-exempelprogrammet.  
Java-exemplet innehåller ett val av flera programramverk.

1. Det exempelramverk som är standard är Spring. Låt standardinställningen vara och välj **Nästa**.  Web App for Containers är det distributionsmål som är standard. Det programramverk som du valde tidigare avgör vilken typ av distributionsmål som finns tillgängliga för Azure-tjänsten här. 

2. Låt standardinställningen för tjänsten vara och välj **Nästa**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration 

1. Skapa en ny Azure DevOps-organisation eller använd en befintlig organisation. 
   
   1. Välj ett namn för projektet. 
   
   1. Välj din Azure-prenumeration och plats, välj ett namn för programmet och välj sedan **Klar**.  
   Efter några minuter visas DevOps Projects-instrumentpanelen i Azure-portalen. Ett exempelprogram konfigureras i en lagringsplats i din Azure DevOps-organisation, en version körs och programmet distribueras till Azure. Den här instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och ditt program i Azure.
   
2. Visa ditt körande program genom att välja **Bläddra**.
   
   ![Visa instrument panelen för programmet i Azure Portal](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

DevOps Projects konfigurerar automatiskt en CI-version och släpper utlösaren.  Nu är du redo att samarbeta med ett team på en Java-app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps Projects skapar en Git-lagringsplats i Azure Repos eller GitHub. Visa lagringsplatsen och gör kodändringar i programmet genom att utföra följande:

1. Till vänster om DevOps Projects-instrumentpanelen väljer du länken för din huvudgren.  
Den här länken öppnar en vy till den nyligen skapade Git-lagringsplatsen.

1. Om du vill visa webbadressen för den klonade lagringsplatsen väljer du **Klona** längst upp till höger i webbläsaren.   
    Du kan klona Git-lagringsplatsen till din favorit-IDE. I kommande steg kan du använda webbläsaren för att göra och checka in ändringar i koden direkt till huvudgrenen.

1. På vänster sida i webbläsaren går du till filen **src/main/webapp/index.html**.

1. Välj **Redigera** och gör sedan en ändring i en del av texten.
    Du kan till exempel ändra en del av texten för en av div-taggarna.

1. Välj **Checka in** och spara sedan ändringarna.

1. I webbläsaren går du till DevOps Projects-instrumentpanelen.   
Du bör nu se att en version håller på att skapas. De ändringar du just utfört skapas och distribueras automatiskt via en CI/CD-pipeline.

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

 I föregående steg konfigurerade DevOps Projects automatiskt en fullständig CI/CD-pipeline. Utforska och anpassa pipelinen efter behov. Utför följande steg för att bekanta dig med bygg- och versionspipelines.

1. Välj **Bygg-pipelines** längst upp på DevOps Projects-instrumentpanelen.  
Länken öppnar en webbläsarflik och bygg-pipelinen för det nya projektet.

1. Peka på fältet **Status** och välj ellipsen (...).  
    Den här åtgärden öppnar en meny där du kan starta flera aktiviteter, till exempel lägga till en ny version i en kö, pausa en version och redigera bygg-pipelinen.

1. Välj **Redigera**.

1. I det här fönstret kan du granska de olika uppgifterna för bygg-pipelinen.  
Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata som används för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande och välj alternativet för att **spara och placera i kö**. Välj sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.   
I fönsterrutan **Historik** ser du en spårningslogg över de senaste ändringarna för versionen.  Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **utlösare**.   
 DevOps Projects skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **kvarhållning**.   
Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **Build and Release** (Build-versioner och versioner) och sedan **Versioner**.  
 DevOps Projects skapar en versionspipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) till vänster bredvid din releasepipeline och välj sedan **Redigera**.  
Versionspipelinen innehåller en pipeline som definierar versionsprocessen.  
    
12. Under **Artefakter** väljer du **Släpp**.  
Den bygg-pipeline du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. Intill ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  
Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till vänster väljer du **Uppgifter**.   
Uppgifter är de aktiviteter som distributionsprocessen utför. I det här exemplet skapades en uppgift för att distribuera till Azure App-tjänsten.

1. Till höger väljer du alternativet för att **visa versioner**.  
Den här vyn visar en historik över versioner.

1. Välj ellipsen (...) bredvid en av versionerna och välj **Öppna**.  
Det finns flera menyer att utforska, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**.   
Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. 

1. Välj **Loggar**.  
Loggarna innehåller användbar information om distributionsprocessen. De kan visas både under och efter distributionerna.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser när de inte längre behövs. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade CI/CD-processen skapades bygg- och versionspipelines automatiskt. Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Anpassa CD-process](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
