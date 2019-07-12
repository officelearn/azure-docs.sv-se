---
title: 'Självstudier: Distribuera Node.js-appar som drivs av Azure Cosmos DB med Azure DevOps-projekt'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Med DevOps-projekt kan distribuera du din Node.js-app som drivs av Azure Cosmos DB till Windows Web App med några få enkla steg.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4310807423600b96078ee48a04a5ad6dab68cd7e
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813068"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Distribuera Node.js-appar som drivs av Azure Cosmos DB med DevOps-projekt

Azure DevOps-projekt erbjuder en smidig miljö där du kan ta med befintlig kod och Git-lagringsplats eller välja ett exempelprogram för att skapa en kontinuerlig integrering (CI) och en pipeline för kontinuerlig leverans (CD) till Azure.

DevOps-projekt gör även följande:

* Skapa Azure-resurser automatiskt som Azure Cosmos DB, Application Insights, App Service och App Service.

* Skapar och konfigurerar en releasepipeline i Azure DevOps för CI/CD

I den här kursen ska du:

> [!div class="checklist"]
> * Använda DevOps-projekt för att distribuera en Node.js-app som drivs av Azure Cosmos DB
> * Konfigurera Azure DevOps och en Azure-prenumeration
> * Granska Azure Cosmos DB
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Ändringarna i Git och automatiskt distribuera dem till Azure
> * Rensa resurserna

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få en kostnadsfri till [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Använda DevOps-projekt för att distribuera Node.js-app

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps-projekt skapar även Azure-resurser, till exempel Azure Cosmos DB, Application Insights, App Service och App Service-planen i valfri Azure-prenumeration.

1. Logga in på [Azure-portalen](https://portal.azure.com)

1. I den vänstra rutan väljer du avsnittet **skapa en resurs**.

1. I sökrutan skriver **DevOps-projekt**, och klicka sedan på **Lägg till**.

   ![DevOps-projekt](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Välj **Node.js** som runtime och välj sedan **nästa**. Under **Välj ett programramverk**väljer **Express.js**.

1. Aktivera avsnittet **lägga till en databas** för **Cosmos DB** och klicka på **nästa**.

    ![Lägg till databas](_img/azure-devops-project-cosmos-db/add-database.png)

    Cosmos DB stöder olika programramverk som **Express.js**, **exemplet Node.js-app**, och **Sail.js**. I den här självstudien kan du överväga att **Express.js**.

1. Välj en Azure-tjänst att distribuera programmet. Du har olika tjänster som Windows Web App, Kubernetes Service och Web App for Containers. Den här självstudien ska vi använda **Windows Web App**. Klicka på **nästa**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurera Azure DevOps och Azure-prenumeration

1. Ange ett namn för ditt Azure DevOps-projekt.

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation.

1. Välj din Azure-prenumeration.

1. Visa fler Azure konfigurationsinställningar och för att identifiera prisnivå nivå och plats, klicka på ytterligare inställningar. Det här fönstret visar olika alternativ för att konfigurera prisnivå nivå och platsen för Azure-tjänster.

1. Stäng Azure-konfigurationsområdet och välj sedan **Klar**.

1. Efter några minuter är processen klar. En Node.js-exempelapp ställs in i en Git-lagringsplats i din organisation med Azure DevOps, skapas en Azure Cosmos DB, App Service, App Service-plan och Application Insights, en CI/CD-pipeline körs och din app distribueras till Azure.

   När allt detta är klart visas Azure DevOps Projects-instrumentpanelen i Azure-portalen. Du kan även gå till DevOps Projects-instrumentpanelen direkt från **Alla resurser** i Azure-portalen.

   Den här instrumentpanelen ger insyn i din Azure DevOps kodcentrallager, CI/CD-pipeline och i Azure Cosmos DB. Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps-pipeline. Till höger, Välj **Azure Cosmos DB** att visa.

## <a name="examine-the-azure-cosmos-db"></a>Granska Azure Cosmos DB

DevOps-projekt konfigurerar automatiskt Cosmos DB, som du kan utforska och anpassa. För att bekanta dig med Cosmos DB, gör du följande:

1. Gå till DevOps Projects-instrumentpanelen.

    ![DevOps-projekt-instrumentpanelen](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Till höger, Välj Cosmos DB. En ruta öppnas för Cosmos DB. Du kan utföra olika åtgärder som att åtgärder för övervakning och söka i loggar från den här vyn.

    ![Funktionsapp](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps Projects konfigurerar automatiskt en Azure CI/CD-pipeline i Azure DevOps-organisationen. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till DevOps Projects-instrumentpanelen.

1. Klicka på länken under **skapa**. En webbläsarflik visar bygg-pipelinen för det nya projektet.

    ![Utveckla](_img/azure-devops-project-cosmos-db/build.png)

1. Välj **Redigera**. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter, t.ex. Hämta källkoden från Git-lagringsplats som skapar program, köra enhetstester och publicera utdata som används för distributioner.

1. Välj **Utlösare**. DevOps Projects skapar automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra namnet på din build-pipeline till något mer beskrivande och väljer sedan **spara** från den **spara och köa** listrutan.

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

## <a name="examine-the-cd-release-pipeline"></a>Granska CD-versionspipelinen

DevOps-projekt skapar och konfigurerar de nödvändiga stegen för att automatiskt distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Navigera till den **Pipelines | Versioner**.

1. Klicka på **Redigera**.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten.

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här releasepipeline har aktiverat CD-utlösare, som kör en distribution varje gång en ny byggesartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.

1. Välj avsnitt till höger, **visa versioner** att visa en historik över versioner.

1. Klicka på påverkas, vilket visar pipelinen. Klicka på valfri miljö för att kontrollera versionen **sammanfattning, incheckningar**, associerade **arbetsobjekt**.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **visa loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

> [!NOTE]
> Följande procedur testar CI/CD-pipeline med en enkel textändring.

Nu är du redo att samarbeta med ett team i din app med hjälp av en CI/CD-process som automatiskt distribuerar ditt senaste arbete till Azure App Service. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att genomföra ändringarna för lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. I Azure DevOps-menyn väljer **lagringsplatser | Filer**, och gå sedan till din lagringsplats.

1. Databasen innehåller redan kod baserat på programmets språk du valde under skapandeprocessen. Öppna den **Application/views/index.pug** fil.

1. Välj **redigera**, och sedan ändrar till **radnummer 15** . Du kan till exempel uppdatera den till **min första distributionen till Azure App Service med Azure Cosmos DB**

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna.

     Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka versionstillståndet med instrumentpanelen för DevOps Projects eller i webbläsaren med din Azure DevOps-organisation.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de relaterade resurserna som du skapade när du inte längre behövs. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda DevOps-projekt för att distribuera en Node.js-app som drivs av Azure Cosmos DB
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska Azure Cosmos DB
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser
