---
title: Skapa en CI/CD-pipeline för Python med Azure DevOps-projekt | Snabbstart
description: DevOps-projekt gör det enkelt att komma igång med Azure. Det hjälper dig att starta en app på en Azure-tjänst med några enkla få steg.
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
ms.openlocfilehash: 46f65772ed4cb70b80674ae39629f52694be49e1
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405657"
---
# <a name="create-a-cicd-pipeline-for-python-with-the-azure-devops-project"></a>Skapa en CI/CD-pipeline för Python med Azure DevOps-projekt

Azure DevOps-projektet ger ett förenklat sätt att skapa Azure-resurser och konfigurerar en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din Python-app i Azure DevOps Services.  

Om du inte har en Azure-prenumeration kan du skaffa en kostnadsfritt via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Azure DevOps-projektet skapar en CI/CD-pipeline i Azure.  Du kan skapa en **ny kostnadsfri Azure DevOps Services-organisation** eller använda en **befintlig organisation**.  DevOps-projektet skapar även **Azure-resurser** i den **Azure-prenumeration** som du väljer.

1. Logga in i [Microsoft Azure-portalen](https://portal.azure.com).

1. Välj ikonen **Skapa en resurs** i det vänstra navigeringsfältet och sök efter **DevOps-projekt**.  Välj **Skapa**.

    ![Börja konfiguration av kontinuerlig leverans](_img/azure-devops-project-python/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogrammet och en Azure-tjänst

1. Välj **Python**-exempelprogrammet.  Python-exemplet innehåller ett val av flera programramverk.

1. Exempelramverket som är standard är **Django**. Låt standardinställningen vara och välj **Nästa**.  

1. **Web App for Containers** är distributionsmålet som är standard.  Programramverket som du valde i föregående steg avgör vilka typer av distributionsmål som finns tillgängliga för Azure-tjänsten här.  Låt standardinställningen för tjänsten vara och välj **Nästa**.
 
## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Konfigurera Azure DevOps Services och en Azure-prenumeration 

1. Skapa en **ny** Azure DevOps Services-organisation eller använd en **befintlig** organisation.  Välj ett **namn** för ditt Azure DevOps-projekt.  Välj din **Azure-prenumeration**, **plats** och välj ett **namn** för ditt program.  När du är klar väljer du **Klar**.

1. **Projektinstrumentpanelen** läses in i Azure-portalen på några minuter.  Ett exempelprogram konfigureras i en lagringsplats i DevOps Services-organisationen, en version körs och programmet distribueras till Azure.  Den här instrumentpanelen ger insyn i **kodlagringsplatsen**, **Azure CI/CD-pipelinen** och ditt **program i Azure**.  På höger sida av instrumentpanelen väljer du **Bläddra** för att visa dt program som körs.

    ![Instrumentpanelsvy](_img/azure-devops-project-python/dashboardnopreview.png) 
    
Azure DevOps-projektet konfigurerar automatiskt en CI-version och släpper utlösaren.  Nu är du redo att samarbeta med ett team på en Python-app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

Azure DevOps-projektet skapade en Git-lagringsplats i din Azure DevOps Services-organisation eller på ditt GitHub-konto.  Följ stegen nedan för att visa lagringsplatsen och gör ändringar i koden i programmet.

1. På vänster sida av instrumentpanelen för DevOps-projektet väljer du länken för din **huvudgren**.  Den här länken öppnar en vy till den nyligen skapade Git-lagringsplatsen.

1. Om du vill visa webbadressen för den klonade lagringsplatsen väljer du **Klona** längst upp till höger i webbläsaren. Du kan klona Git-lagringsplatsen till din favorit-IDE.  I kommande steg kan du använda webbläsaren för att göra och genomföra ändringar i koden direkt till huvudgrenen.

1. På vänster sida i webbläsaren navigerar du till filen **app/templates/app/index.html**.

1. Välj **Redigera** och göra en ändring i en del av texten.  Du kan till exempel ändra en del av texten för en av div-taggarna.

1. Välj **Checka in** och spara sedan ändringarna.

1. I webbläsaren navigerar du till **instrumentpanelen för Azure DevOps-projektet**.  Du bör nu se att en version håller på att skapas.  De ändringar du just utfört skapas och distribueras automatiskt via en Azure-CI/CD-pipeline.

## <a name="examine-the-azure-cicd-pipeline"></a>Granska Azure-CI/CD-pipelinen

Azure DevOps-projektet konfigurerade automatiskt en fullständig Azure CI/CD-pipeline i Azure DevOps Services-organisationen.  Utforska och anpassa pipelinen efter behov.  Följ stegen nedan för att bekanta dig med bygg- och versionspipelinerna för Azure DevOps Services.

1. Välj **Skapa pipelines** **längst upp** på Azure DevOps-projektets instrumentpanel.  Den här länken öppnar en flik i webbläsaren och öppnar bygg-pipelinen för Azure DevOps Services för det nya projektet.

1. Flytta markören till höger om bygg-pipelinen bredvid fältet **Status**. Välj den **ellips** som visas.  Den här åtgärden öppnar en meny där du kan starta flera aktiviteter, till exempel lägga till en ny version i en kö, pausa en version och redigera bygg-pipelinen.

1. Välj **Redigera**.

1. Från den här vyn **granskar du de olika uppgifterna** för bygg-pipelinen.  Versionen kör olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Välj **bygg-pipelinens namn** längst upp i bygg-pipelinen.

1. Ändra **namnet** på din bygg-pipeline till något mer beskrivande.  Välj **Save & queue** (Spara och köa) och välj sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.  Du kan se en spårningslogg över de senaste ändringarna för versionen.  Azure DevOps Services spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**.  Azure DevOps-projektet skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.  Baserat på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **Build and Release** (Build-versioner och versioner) och sedan **Versioner**.  Azure DevOps-projektet skapade en Azure DevOps Services-versionspipeline för att hantera distributioner till Azure.

1. På vänster sida i webbläsaren väljer du **ellipsen** bredvid din versionspipeline och sedan väljer du **Redigera**.

1. Versionspipelinen innehåller en **pipeline** som definierar släpprocessen.  Under **Artefakter** väljer du **Släpp**.  Den bygg-pipeline du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig.  Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. På vänster sida i webbläsaren väljer du **Uppgifter**.  Uppgifter är de aktiviteter som distributionsprocessen utför.  I det här exemplet skapades en uppgift för att distribuera till **Azure App-tjänsten**.

1. Till höger i webbläsaren väljer du **Visa versioner**.  Den här vyn visar en historik över versioner.

1. Välj **ellipsen** bredvid en versionerna och välj **Öppna**.  Det finns flera menyer att utforska från den här vyn, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**.  Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. 

1. Välj **Loggar**.  Loggarna innehåller användbar information om distributionsprocessen.  De kan visas både under och efter distributionerna.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort Azure App-tjänsten och relaterade resurser som skapats i den här snabbstarten med hjälp av funktionen **Ta bort** på instrumentpanelen för Azure DevOps-projektet.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade CI/CD-processen i den här snabbstarten skapades automatiskt en bygg- och versionspipeline i Azure DevOps-projektet. Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Mer information finns i den här självstudien:

> [!div class="nextstepaction"]
> [Anpassa CD-process](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
