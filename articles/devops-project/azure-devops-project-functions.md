---
title: 'Självstudier: Distribuera ASP.NET-appar till Azure Functions med Azure DevOps-projekt'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Med DevOps-projekt kan du distribuera din ASP.NET-app till Azure Functions i några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6894f9bc6c803e2692afb54d7459adf6b0e6dbd6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828094"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Kontinuerlig distribution till Azure Functions med DevOps-projekt

Azure DevOps Projects ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett exempelprogram för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.

DevOps-projekt gör även följande:

* Skapar automatiskt Azure-resurser, till exempel Azure Functions

* Skapar och konfigurerar en releasepipeline i Azure DevOps för CI/CD

I den här kursen ska du:

> [!div class="checklist"]
>* Använda DevOps-projekt för att distribuera en ASP.NET-app till Azure-funktion
>* Konfigurera Azure DevOps och en Azure-prenumeration
>* Granska Azure-funktion
>* Granska CI-pipelinen
>* Granska CD-pipelinen
>* Genomföra ändringar i Git och distribuera dem automatiskt till Azure
>* Rensa resurser

Körningar för funktioner som stöds finns för närvarande **.NET** och **Node.js**. Vi använder. NET runtime för den här kursen och distribuera till Azure Functions. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få en kostnadsfri till [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Använda DevOps-projekt för att distribuera en ASP.NET-app till Azure Functions

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps-projekt skapar även Azure-resurser, till exempel en IoTHub i valfri Azure-prenumeration.

1. Logga in på [Azure-portalen](https://portal.azure.com)

1. Välj **Skapa en resurs** i fönstret till vänster.

1. I sökrutan skriver **DevOps-projekt**, och klicka sedan på **Lägg till**.

   ![DevOps-projekt](_img/azure-devops-project-functions/devops-project.png)

1. Välj **.NET** och sedan **Nästa**. Under **Välj ett programramverk**väljer **ASP.NET** och klicka på **nästa**.

1. Välj **Funktionsapp** och välj sedan **nästa**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurera Azure DevOps och Azure-prenumeration

1. Ange ett namn för ditt Azure DevOps-projekt.

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation.

1. Välj din Azure-prenumeration.

1. Visa fler Azure konfigurationsinställningar och för att identifiera prisnivå nivå och plats, klicka på ytterligare inställningar. Det här fönstret visar olika alternativ för att konfigurera prisnivå nivå och platsen för Azure-tjänster.

1. Avsluta Azure konfigurationsområde och välj klart.

1. Efter några minuter är processen klar. En exempelapp för ASP.NET har ställts in i en Git-lagringsplats i organisationen Azure DevOps, en Funktionsapp och Application Insights har skapats, en CI/CD-pipeline körs och din app distribueras till Azure.

   När allt detta är klart visas Azure DevOps Projects-instrumentpanelen i Azure-portalen. Du kan även gå till DevOps Projects-instrumentpanelen direkt från **Alla resurser** i Azure-portalen.

   Den här instrumentpanelen ger insyn i din Azure DevOps-kodcentrallager, CI/CD-pipeline och din Azure-funktion. Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps-pipeline. Till höger, Välj **Funktionsapp** att visa.

## <a name="examine-the-function-app"></a>Granska Funktionsappen

DevOps-projekt konfigurerar automatiskt funktionsapp, som du kan utforska och anpassa. För att få veta funktionsappen kan du göra följande:

1. Gå till DevOps Projects-instrumentpanelen.

    ![DevOps-projekt-instrumentpanelen](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Till höger, Välj funktionsappen. En ruta öppnas för funktionsappen. Du kan utföra olika åtgärder, till exempel operations övervakning, söka i loggar från den här vyn.

    ![Funktionsapp](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps Projects konfigurerar automatiskt en Azure CI/CD-pipeline i Azure DevOps-organisationen. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till DevOps Projects-instrumentpanelen.

1. Klicka på länken under **skapa**. En webbläsarflik visar bygg-pipelinen för det nya projektet.

    ![Utveckla](_img/azure-devops-project-functions/build.png)

1. Välj **Redigera**. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter, t.ex. Hämta källkoden från Git-lagringsplats som skapar program, köra enhetstester och publicera utdata som används för distributioner.

1. Välj **Utlösare**. DevOps Projects skapar automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra namnet på din build-pipeline till något mer beskrivande och väljer sedan **spara** från den **spara och köa** listrutan.

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps Håll koll på ändringar som görs till build-pipelinen och du kan jämföra versioner.

## <a name="examine-the-cd-release-pipeline"></a>Granska CD-versionspipelinen

DevOps-projekt skapar och konfigurerar de nödvändiga stegen för att automatiskt distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Navigera till den **Pipelines | Versioner**.

1. Klicka på **Redigera**.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten.

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Klicka på påverkas, vilket visar pipelinen. Klicka på valfri miljö för att kontrollera versionen **sammanfattning, incheckningar**, associerade **arbetsobjekt**.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **visa loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

> [!NOTE]
> Följande procedur testar CI/CD-pipeline med en enkel textändring.

Nu är du redo att samarbeta med ett team i din app med hjälp av en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din Azure-funktion. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att genomföra ändringarna för lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. I Azure DevOps-menyn väljer **lagringsplatser | Filer**, och gå sedan till din lagringsplats.

1. Databasen innehåller redan kod som anropas **SampleFunctionApp** baserat på programmets språk du valde under skapandeprocessen. Öppna den **Application/SampleFunctionApp/Function1.cs** fil.

1. Välj **redigera**, och sedan ändrar till **radnummer 31** . Du kan till exempel uppdatera den till **Hej! Välkommen till Azure Functions med DevOps-projekt**

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna.

1. Öppna den **Application/SampleFunctionApp.Test/Function1TestRunner.cs** fil. 

1. Välj **redigera**, och sedan ändrar till **radnummer 21**. Du kan till exempel uppdatera den till **Hej! Välkommen till Azure Functions med Azure DevOps-projekt**.

     Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka versionstillståndet med instrumentpanelen för DevOps Projects eller i webbläsaren med din Azure DevOps-organisation.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de relaterade resurserna som du skapade när du inte längre behövs. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda DevOps-projekt för att distribuera en ASP.NET Core-app till Azure-funktion
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska Azure-funktion
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

