---
title: 'Självstudiekurs: Distribuera ASP.NET appar till Azure-funktioner med Azure DevOps-projekt'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Med DevOps-projekt kan du distribuera din ASP.NET-appen till Azure Functions i några snabba steg.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971562"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Distribuera kontinuerligt till Azure-funktioner med DevOps-projekt

Azure DevOps Projects ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett exempelprogram för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.

DevOps-projekt gör även följande:

* Skapar Azure-resurser automatiskt, till exempel Azure-funktioner

* Skapar och konfigurerar en versionspipeline i Azure DevOps för CI/CD

I den här kursen ska du:

> [!div class="checklist"]
>* Använda DevOps-projekt för att distribuera en ASP.NET app till Azure-funktionen
>* Konfigurera Azure DevOps och en Azure-prenumeration
>* Undersök Azure-funktionen
>* Granska CI-pipelinen
>* Granska CD-pipelinen
>* Genomföra ändringar i Git och distribuera dem automatiskt till Azure
>* Rensa resurser

För närvarande stöds körningar för funktioner är **.NET** och **Node.js**. Vi använder. NET-körning för den här självstudien som ska distribueras till Azure-funktioner. 

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Du kan få en gratis via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Använda DevOps-projekt för att distribuera en ASP.NET app till Azure Functions

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps-projekt skapar också Azure-resurser, till exempel en IoTHub, i azure-prenumerationen som du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com)

1. Välj **Skapa en resurs** i fönstret till vänster.

1. Skriv **DevOps-projekt**i sökrutan och klicka sedan på **Lägg till**.

   ![DevOps-projekt](_img/azure-devops-project-functions/devops-project.png)

1. Välj **.NET** och sedan **Nästa**. Under **Välj ett programramverk**väljer du **ASP.NET** och klickar på **Nästa**.

1. Välj **Funktionsapp** och välj sedan **Nästa**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurera Azure DevOps- och Azure-prenumeration

1. Ange ett namn för ditt Azure DevOps-projekt.

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation.

1. Välj din Azure-prenumeration.

1. Om du vill visa ytterligare Azure-konfigurationsinställningar och identifiera prisnivå och plats klickar du på Ytterligare inställningar. I det här fönstret visas olika alternativ för att konfigurera prisnivån och platsen för Azure-tjänster.

1. Stäng Azure-konfigurationsområdet och välj sedan Klar.

1. Efter några minuter är processen klar. Ett exempel ASP.NET app har konfigurerats i en Git-repo i din Azure DevOps-organisation, en funktionsapp och Application Insights skapas, en CI/CD-pipeline körs och din app distribueras till Azure.

   När allt detta är klart visas Azure DevOps Projects-instrumentpanelen i Azure-portalen. Du kan även gå till DevOps Projects-instrumentpanelen direkt från **Alla resurser** i Azure-portalen.

   Den här instrumentpanelen ger insyn i din Azure DevOps-kodlagringsplats, din CI/CD-pipeline och din Azure-funktion. Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps-pipeline. Till höger väljer du **Funktionsapp** att visa.

## <a name="examine-the-function-app"></a>Undersök funktionsappen

DevOps-projekt konfigurerar automatiskt funktionsappen, som du kan utforska och anpassa. Så här lär du känna funktionsappen:

1. Gå till DevOps Projects-instrumentpanelen.

    ![Instrumentpanelen DevOps-projekt](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Till höger väljer du funktionsappen. En ruta öppnas för funktionsappen. Från den här vyn kan du utföra olika åtgärder, till exempel åtgärder övervakning, söka loggar.

    ![Funktionsapp](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps Projects konfigurerar automatiskt en Azure CI/CD-pipeline i Azure DevOps-organisationen. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till DevOps Projects-instrumentpanelen.

1. Klicka på hyperlänken under **Build**. En webbläsarflik visar bygg-pipelinen för det nya projektet.

    ![Utveckla](_img/azure-devops-project-functions/build.png)

1. Välj **Redigera**. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Build utför olika uppgifter, till exempel hämta källkod från Git-repo, skapa programmet, köra enhetstester och publicera utdata som används för distributioner.

1. Välj **Utlösare**. DevOps Projects skapar automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra namnet på byggpipelinen till något mer beskrivande och välj sedan Spara i den rullgardinsmenyn **Spara &.Change** the name of your build pipeline to something more descriptive, and then select **Save from** the Save & queue dropdown.

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps hålla reda på alla ändringar som gjorts i byggpipelinen, och det låter dig jämföra versioner.

## <a name="examine-the-cd-release-pipeline"></a>Granska CD-versionspipelinen

DevOps-projekt skapar och konfigurerar automatiskt de steg som är nödvändiga för att distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Navigera till **pipelines | Släpper**.

1. Klicka på **Redigera**.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten.

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Klicka på utgåvan, som kommer att visa rörledningen. Klicka på valfri miljö för att kontrollera versionen **Sammanfattning, Commits**, associerade **arbetsobjekt**.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Visa loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

> [!NOTE]
> Följande procedur testar CI/CD-pipeline med en enkel textändring.

Du är nu redo att samarbeta med ett team i din app med hjälp av en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din Azure-funktion. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att checka in ändringarna till lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. Välj **Repos | Filer**och gå sedan till din repo.

1. Databasen innehåller redan kod som kallas **SampleFunctionApp** baserat på det programspråk som du valde i skapandeprocessen. Öppna filen **Application/SampleFunctionApp/Function1.cs.**

1. Välj **Redigera**och gör sedan en ändring av **radnummer 31** . Till exempel kan du uppdatera den till **Hello there! Välkommen till Azure-funktioner med DevOps-projekt**

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna.

1. Öppna filen **Application/SampleFunctionApp.Test/Function1TestRunner.cs.** 

1. Välj **Redigera**och gör sedan en ändring av **radnummer 21**. Till exempel kan du uppdatera den till **Hello there! Välkommen till Azure-funktioner med Azure DevOps-projekt**.

     Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka versionstillståndet med instrumentpanelen för DevOps Projects eller i webbläsaren med din Azure DevOps-organisation.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de relaterade resurser som du skapade när du inte behöver dem längre. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda DevOps-projekt för att distribuera en ASP.NET Core-app till Azure-funktionen
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Undersök Azure-funktionen
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

