---
title: 'Självstudier: Distribuera din ASP.NET Core-app till Azure Service Fabric med Azure DevOps-projekt'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Med DevOps Projects kan du distribuera din ASP.NET Core-app till Azure Service Fabric med några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 8ba217cb9ce849e57b15d3e6cc73529c78bf340e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60554953"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-projects"></a>Självstudier: Distribuera din ASP.NET Core-app till Azure Service Fabric med Azure DevOps-projekt

Azure DevOps Projects ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett exempelprogram för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure. 

DevOps Projects gör även följande:
* Skapar automatiskt Azure-resurser, till exempel Azure Service Fabric.
* Skapar och konfigurerar en versionspipeline i Azure DevOps som konfigurerar en CI/CD-pipeline.
* Skapar en Azure Application Insights-resurs för övervakning.

I den här kursen ska du:

> [!div class="checklist"]
> * Använda DevOps Projects för att skapa en ASP.NET Core-app och distribuera den till Service Fabric
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera automatiskt till Azure
> * Rensa resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Använda DevOps Projects för att skapa en ASP.NET Core-app och distribuera den till Service Fabric

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser, till exempel ett Service Fabric-kluster, i den Azure-prenumeration som du väljer.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **Skapa en resurs** i fönstret till vänster.

1. I sökrutan skriver du **DevOps Projects** och väljer sedan **Skapa**.

    ![DevOps Projects-instrumentpanelen](_img/azure-devops-project-github/fullbrowser.png)

1. Välj **.NET** och sedan **Nästa**.

1. Under **Välj ett programramverk** väljer du **ASP.NET Core** och sedan **Nästa**.

1. Välj **Service Fabric Cluster** och sedan **Nästa**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation. 

1. Ange ett namn för ditt Azure DevOps-projekt. 

1. Välj din Azure-prenumeration.

1. Om du vill visa ytterligare Azure-konfigurationsinställningar och identifiera nodstorleken för den virtuella datorn och operativsystemet för Service Fabric-klustret väljer du **Ändra**.  
    I den här fönsterrutan visas olika alternativ här för konfiguration av typ och plats för Azure-tjänster.
 
1. Stäng Azure-konfigurationsområdet och välj sedan **Klar**.  
    Efter några minuter är processen klar. En ASP.NET Core-exempelapp konfigureras i en Git-lagringsplats i din Azure DevOps-organisation, ett Service Fabric-kluster skapas, en CI/CD-pipeline körs och appen distribueras till Azure. 

    När allt detta är klart visas DevOps Projects-instrumentpanelen i Azure-portalen. Du kan även gå till DevOps Projects-instrumentpanelen direkt från **Alla resurser** i Azure-portalen. 

    Den här instrumentpanelen ger insyn i din Azure DevOps-kodlagringsplats, din CI/CD-pipeline och ditt Service Fabric-kluster. Du kan konfigurera ytterligare alternativ för din CI/CD-pipeline i Azure Repos. Till höger väljer du **Bläddra** för att visa den körande appen.

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps Projects konfigurerar automatiskt en CI/CD-pipeline i Azure Pipelines. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till instrumentpanelen för DevOps-projektet.

1. Välj **Bygg-pipelines** längst upp på DevOps Projects-instrumentpanelen.  
    En webbläsarflik visar bygg-pipelinen för det nya projektet.

1. Peka på fältet **Status** och välj ellipsen (...).  
    En meny med flera alternativ visas, till exempel alternativ för att köa en ny version, pausa en version och redigera bygg-pipelinen.

1. Välj **Redigera**.

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen.  
    Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen. 

1. Under ditt bygg-pipelinenamn väljer du **Historik**.  
    I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**.  
    DevOps Projects skapar automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.  
    Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

## <a name="examine-the-cd-pipeline"></a>Granska CD-pipelinen

DevOps-projekt skapar och konfigurerar de nödvändiga stegen för att automatiskt distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**.  
    DevOps Projects skapar en versionspipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) intill din versionspipeline och välj sedan **Redigera**.  
    Versionspipelinen innehåller en *pipeline* som definierar släpprocessen.

1. Under **Artefakter** väljer du **Släpp**.  
    Den bygg-pipeline som du undersökte tidigare skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  
    Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) intill en version och välj sedan **Öppna**.  
    Du kan utforska flera menyer, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**.  
    Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Loggar**.  
    Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar i Git och distribuera dem automatiskt till Azure 

 > [!NOTE]
 > Följande procedur testar CI/CD-pipeline med en enkel textändring.

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats. Varje ändring av Git-lagringsplatsen påbörjar en version, och en version distribuerar dina ändringar till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att checka in ändringarna till lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. I Azure DevOps-menyn väljer du **Kod** > **Filer** och går sedan till din lagringsplats.

1. Gå till katalogen *Views\Home* och välj ellipsen (...) intill filen *Index.cshtml*. Välj sedan **Redigera**.

1. Gör en ändring i filen, till exempel att lägga till lite text i en av div-taggarna. 

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna.  
    Efter en stund startar en version, och därefter körs en version för att distribuera ändringarna. Du kan övervaka versionstillståndet på DevOps Projects-instrumentpanelen eller i webbläsaren med Azure DevOps realtidsloggning.

1. När versionen har slutförts uppdaterar du appen för att verifiera dina ändringar.

## <a name="clean-up-resources"></a>Rensa resurser

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter. När de inte längre behövs kan du ta bort Azure Service Fabric-klustret och relaterade resurser som du skapade i den här självstudien. Det gör du med funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

> [!IMPORTANT]
> Följande procedur tar permanent bort resurser. Funktionen *Ta bort* förstör alla data som skapats av Azure DevOps-projektet i både Azure och Azure DevOps, och du kan inte återskapa dem. Använd den här proceduren först när du har läst anvisningarna noga.

1. Gå till DevOps Projects-instrumentpanelen i Azure-portalen.
1. Välj **Ta bort** i det övre högra hörnet. 
1. Vid uppmaningen väljer du **Ja** för att *permanent ta bort* resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra Azure CI/CD-pipeline för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda DevOps Projects för att skapa en ASP.NET Core-app och distribuera den till Service Fabric
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

Se följande om du vill veta mer om Service Fabric och mikrotjänster:

> [!div class="nextstepaction"]
> [Använda mikrotjänster för att bygga program](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
