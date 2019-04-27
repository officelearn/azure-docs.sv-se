---
title: 'Självstudier: Distribuera ASP.NET Core-appar till Azure Kubernetes Service med Azure DevOps-projekt'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Med DevOps Projects kan du distribuera din ASP.NET Core-app med Azure Kubernetes Service (AKS) i några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 2aa103b36f60a84aaafc47f03a6cf6d5b6b66160
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60554955"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Självstudier: Distribuera ASP.NET Core-appar till Azure Kubernetes Service med Azure DevOps-projekt

Azure DevOps Projects ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett exempelprogram för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure. 

DevOps-projekt gör även följande:
* Skapar automatiskt Azure-resurser, till exempel Azure Kubernetes Service (AKS).
* Skapar och konfigurerar en versionspipeline i Azure DevOps som konfigurerar en versionspipeline för CI/CD.
* Skapar en Azure Application Insights-resurs för övervakning.
* Gör så att [Azure Monitor för containrar](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) kan övervaka prestanda för containerarbetsbelastningar på AKS-klustret

I den här kursen ska du:

> [!div class="checklist"]
> * Använda DevOps Projects för att distribuera en ASP.NET Core-app till AKS
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska AKS-kluster
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>Använda DevOps Projects för att distribuera en ASP.NET Core-app till AKS

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurse, som ett AKS-kluster, i den Azure-prenumeration som du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Skapa en resurs** i fönstret till vänster.

1. I sökrutan skriver du **DevOps Projects** och väljer sedan **Skapa**.

    ![DevOps Projects-instrumentpanelen](_img/azure-devops-project-github/fullbrowser.png)

1. Välj **.NET** och sedan **Nästa**.

1. Under **Välj ett programramverk** väljer du **ASP.NET Core**.

1. Välj **Kubernetes Service**, och välj sedan **Nästa**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation. 

1. Ange ett namn för ditt Azure DevOps-projekt. 

1. Välj din Azure-prenumeration.

1. Välj **Ändra** för att se ytterligare Azure-konfigurationsinställningar och identifiera antalet noder för AKS-klustret.  
    I den här fönsterrutan visas olika alternativ här för konfiguration av typ och plats för Azure-tjänster.
 
1. Stäng Azure-konfigurationsområdet och välj sedan **Klar**.  
    Efter några minuter är processen klar. En ASP.NET Core-exempelapp konfigureras i en Git-lagringsplats i din Azure DevOps-organisation, ett AKS-kluster skapas, en CI/CD-pipeline körs och appen distribueras till Azure. 

    När allt detta är klart visas Azure DevOps Projects-instrumentpanelen i Azure-portalen. Du kan även gå till DevOps Projects-instrumentpanelen direkt från **Alla resurser** i Azure-portalen. 

    Den här instrumentpanelen ger insyn i din Azure DevOps-kodlagringsplats, din CI/CD-pipeline och ditt AKS-kluster. Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps-pipeline. Till höger väljer du **Bläddra** för att visa den körande appen.

## <a name="examine-the-aks-cluster"></a>Granska AKS-kluster

DevOps-projekt konfigurerar automatiskt ett AKS-kluster som du kan utforska och anpassa. För att bekanta dig med AKS-klustret gör du följande:

1. Gå till DevOps Projects-instrumentpanelen.

1. Välj AKS-tjänsten till höger.  
    Ett fönster öppnas för AKS-klustret. Från den här vyn kan du utföra olika åtgärder som att övervaka hälsotillstånd för containrar, söka i loggar och öppna Kubernetes-instrumentpanelen.

1. Till höger väljer du **Visa Kubernetes-instrumentpanelen**.  
    Du kan också följa stegen för att öppna Kubernetes-instrumentpanelen.

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps Projects konfigurerar automatiskt en Azure CI/CD-pipeline i Azure DevOps-organisationen. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till DevOps Projects-instrumentpanelen.

1. Välj **Skapa pipelines** längst upp på DevOps Projects-instrumentpanelen.  
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

## <a name="examine-the-cd-release-pipeline"></a>Granska CD-versionspipelinen

DevOps-projekt skapar och konfigurerar de nödvändiga stegen för att automatiskt distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**.  
    DevOps Projects skapar en versionspipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) intill din versionspipeline och välj sedan **Redigera**.  
    Versionspipelinen innehåller en *pipeline* som definierar släpprocessen.

1. Under **Artefakter** väljer du **Släpp**.  
    Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  
    Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) intill en version och välj sedan **Öppna**.  
    Du kan utforska flera menyer, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**.  
    Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Loggar**.  
    Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure 

 > [!NOTE]
 > Följande procedur testar CI/CD-pipeline med en enkel textändring.

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att genomföra ändringarna för lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. I Azure DevOps-menyn väljer du **Kod** > **Filer** och går sedan till din lagringsplats.

1. Gå till katalogen *Views\Home* och välj ellipsen (...) intill filen *Index.cshtml*. Välj sedan **Redigera**.

1. Gör en ändring i filen, till exempel att lägga till lite text i en av div-taggarna. 

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna.  
    Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka versionstillståndet med instrumentpanelen för DevOps Projects eller i webbläsaren med din Azure DevOps-organisation.

1. När versionen har slutförts uppdaterar du appen för att verifiera dina ändringar.

## <a name="clean-up-resources"></a>Rensa resurser

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter. När de inte längre behövs kan du ta bort AKS-klustret och relaterade resurser som du skapade i den här självstudien. Det gör du med funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

> [!IMPORTANT]
> Följande procedur tar permanent bort resurser. Funktionen *Ta bort* förstör alla data som skapats av Azure DevOps-projektet i både Azure och Azure DevOps, och du kan inte återskapa dem. Använd den här proceduren först när du har läst anvisningarna noga.

1. Gå till DevOps Projects-instrumentpanelen i Azure-portalen.
2. Välj **Ta bort** i det övre högra hörnet. 
3. Vid uppmaningen väljer du **Ja** för att *permanent ta bort* resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda DevOps Projects för att distribuera en ASP.NET Core-app till AKS
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska AKS-kluster
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

Mer information om att använda Kubernetes-instrumentpanelen finns:

> [!div class="nextstepaction"]
> [Använda Kubernetes-instrumentpanelen](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
