---
title: 'Självstudier: Distribuera ASP.NET-appar till Azure Functions med Azure DevOps Projects'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Med DevOps Projects kan du distribuera ASP.NET-appen till Azure Functions några enkla steg.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971562"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Distribuera kontinuerligt till Azure Functions med DevOps Projects

Azure DevOps Projects ger ett förenklat sätt att ta med befintlig kod och Git-lagringsplatser i Azure, eller välja ett exempelprogram för att skapa en pipeline för kontinuerlig integration (CI) och kontinuerlig leverans (CD) till Azure.

DevOps-projekt gör även följande:

* Skapar automatiskt Azure-resurser, till exempel Azure Functions

* Skapar och konfigurerar en versions pipeline i Azure DevOps för CI/CD

I den här kursen ska du:

> [!div class="checklist"]
>* Använda DevOps Projects för att distribuera en ASP.NET-app till Azure Function
>* Konfigurera Azure DevOps och en Azure-prenumeration
>* Granska Azure Function
>* Granska CI-pipelinen
>* Granska CD-pipelinen
>* Genomföra ändringar i Git och distribuera dem automatiskt till Azure
>* Rensa resurser

För närvarande är de körningar som stöds för functions **.net** och **Node. js**. Vi använder. NET Runtime för den här självstudien för att distribuera till Azure Functions. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få ett kostnads fritt via [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Använd DevOps Projects för att distribuera en ASP.NET-app till Azure Functions

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar också Azure-resurser, till exempel en IoTHub, i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com)

1. Välj **Skapa en resurs** i fönstret till vänster.

1. I rutan Sök skriver du **DevOps Projects**och klickar sedan på **Lägg till**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Välj **.NET** och sedan **Nästa**. Under **Välj ett program ramverk**väljer du **ASP.net** och klickar på **Nästa**.

1. Välj **Funktionsapp** och välj sedan **Nästa**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurera Azure-DevOps och Azure-prenumerationen

1. Ange ett namn för ditt Azure DevOps-projekt.

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation.

1. Välj din Azure-prenumeration.

1. Om du vill visa ytterligare konfigurations inställningar för Azure och identifiera pris nivån och platsen klickar du på ytterligare inställningar. I det här fönstret visas olika alternativ för att konfigurera pris nivån och platsen för Azure-tjänster.

1. Avsluta Azures konfigurations fält och välj sedan slutfört.

1. Efter några minuter är processen klar. Ett exempel på en ASP.NET-app har kon figurer ATS i en git-lagrings platsen i din Azure DevOps-organisation, en Funktionsapp och Application Insights skapas, en CI/CD-pipeline körs och din app distribueras till Azure.

   När allt detta är klart visas Azure DevOps Projects-instrumentpanelen i Azure-portalen. Du kan även gå till DevOps Projects-instrumentpanelen direkt från **Alla resurser** i Azure-portalen.

   Den här instrument panelen ger insyn i din Azure DevOps Code-lagringsplats, din CI/CD-pipeline och din Azure-funktion. Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps-pipeline. Välj **Funktionsapp** som ska visas till höger.

## <a name="examine-the-function-app"></a>Granska Funktionsapp

DevOps Projects konfigurerar automatiskt Function-appen, som du kan utforska och anpassa. Gör så här för att bekanta dig med Function-appen:

1. Gå till DevOps Projects-instrumentpanelen.

    ![DevOps Projects instrument panel](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Välj Function-appen till höger. Ett fönster öppnas för Function-appen. I den här vyn kan du utföra olika åtgärder, till exempel åtgärder för övervakning, sökning av loggar.

    ![Funktionsapp](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps Projects konfigurerar automatiskt en Azure CI/CD-pipeline i Azure DevOps-organisationen. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till DevOps Projects-instrumentpanelen.

1. Klicka på hyperlänken under **bygge**. En webbläsarflik visar bygg-pipelinen för det nya projektet.

    ![Utveckla](_img/azure-devops-project-functions/build.png)

1. Välj **Redigera**. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter, till exempel hämtning av källkod från git-lagrings platsen, skapande av program, körning av enhets test och publicering av utdata som används för distributioner.

1. Välj **Utlösare**. DevOps Projects skapar automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra namnet på din build-pipeline till något mer beskrivande och välj sedan **Spara** i list rutan **Spara & kö** .

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps håller koll på alla ändringar som gjorts i bygg pipelinen och gör det möjligt att jämföra versioner.

## <a name="examine-the-cd-release-pipeline"></a>Granska CD-versionspipelinen

DevOps-projekt skapar och konfigurerar de nödvändiga stegen för att automatiskt distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Navigera till **pipelinen | Versioner**.

1. Klicka på **Redigera**.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten.

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Klicka på versionen för att Visa pipelinen. Klicka på valfri miljö för att kontrol lera **sammanfattningen av utgåvor, incheckningar**, associerade **arbets objekt**.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Visa loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

> [!NOTE]
> Följande procedur testar CI/CD-pipeline med en enkel textändring.

Du är nu redo att samar beta med ett team i din app genom att använda en CI/CD-process som automatiskt distribuerar det senaste arbetet till din Azure-funktion. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att genomföra ändringarna för lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. På menyn Azure-DevOps väljer du **databaser | Filer**och gå sedan till din lagrings platsen.

1. Databasen innehåller redan kod som heter **SampleFunctionApp** baserat på det program språk som du valde i skapande processen. Öppna filen **Application/SampleFunctionApp/Function1. cs** .

1. Välj **Redigera**och gör sedan en ändring av **rad numret 31** . Du kan till exempel uppdatera den till **Hello där! Välkommen till Azure Functions med DevOps Projects @ no__t-0

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna.

1. Öppna filen **Application/SampleFunctionApp. test/Function1TestRunner. cs** . 

1. Välj **Redigera**och gör sedan en ändring av **rad nummer 21**. Du kan till exempel uppdatera den till **Hello där! Välkommen till Azure Functions med Azure DevOps Projects @ no__t-0.

     Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka versionstillståndet med instrumentpanelen för DevOps Projects eller i webbläsaren med din Azure DevOps-organisation.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de relaterade resurserna som du skapade när du inte längre behöver dem. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd DevOps Projects för att distribuera en ASP.NET Core app till Azure Function
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska Azure Function
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

