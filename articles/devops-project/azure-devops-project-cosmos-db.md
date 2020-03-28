---
title: 'Självstudiekurs: Distribuera Node.js-appar som drivs av Azure Cosmos DB med Azure DevOps-projekt'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Med DevOps-projekt kan du distribuera din Node.js-app som drivs av Azure Cosmos DB till Windows Web App i några snabba steg.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 229b4b9f53ea3866dce1169645f6d6da20827271
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73888893"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Distribuera Node.js-appar som drivs av Azure Cosmos DB med DevOps-projekt

Azure DevOps Projects erbjuder en strömlinjeformad upplevelse där du kan skapa en kontinuerlig integration (CI) och CD-pipeline (Continuous Deployment) till Azure. Du gör detta genom att använda din befintliga kod och Git-databas (repo) eller genom att välja ett exempelprogram.

DevOps-projekt gör även följande:

* Skapar Azure-resurser automatiskt, till exempel Azure Cosmos DB, Azure Application Insights, Azure App Service och App Service-abonnemang

* Skapar och konfigurerar en CI/CD-versionspipeline i Azure DevOps

I den här kursen ska du:

> [!div class="checklist"]
> * Använda DevOps-projekt för att distribuera en Node.js-app som drivs av Azure Cosmos DB
> * Konfigurera Azure DevOps och en Azure-prenumeration
> * Undersök Azure Cosmos DB
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomför ändringarna i Git och distribuera dem automatiskt till Azure
> * Rensa resurserna

## <a name="prerequisites"></a>Krav

Du behöver en Azure-prenumeration som du kan få via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) utan kostnad.

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Använda DevOps-projekt för att distribuera nod.js-appen

DevOps Projects skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps-projekt skapar också Azure-resurser, till exempel Azure Cosmos DB, Application Insights, App Service och App Service-abonnemang, i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Skapa en resurs** i fönstret till vänster.

1. Skriv **DevOps-projekt**i sökrutan och välj sedan **Lägg till**.

   ![Fönstret DevOps-projekt](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Välj **Nod.js** som körning och välj sedan **Nästa**. Under **Välj ett programramverk**väljer du **Express.js**.

1. Aktivera avsnittet **Lägg till en databas** för **Cosmos DB**och välj sedan **Nästa**.

    ![Lägga till en databas](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps-projekt stöder olika programramverk, till exempel **Express.js,** **Exempelnod.js-appen**och **Sail.js**. I den här guiden använder vi **Express.js**.

1. Välj en Azure-tjänst för att distribuera programmet och välj sedan **Nästa**. Alternativen inkluderar Windows Web App, Azure Kubernetes Service och Azure Web App for Containers. I den här självstudien använder vi **Windows Web App**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurera Azure DevOps- och Azure-prenumeration

1. Ange ett namn för ditt Azure DevOps-projekt.

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation.

1. Välj din Azure-prenumeration.

1. Om du vill visa ytterligare Azure-konfigurationsinställningar eller identifiera prisnivå och plats väljer du **Ytterligare inställningar**. I den här rutan visas olika alternativ för att konfigurera prisnivån och platsen för Azure-tjänster.

1. Stäng Azure-konfigurationsområdet och välj sedan **Klar**.

1. Processen avslutas efter några minuter. En exempelnod.js-app har konfigurerats i en Git-repo i din Azure DevOps-organisation. Sedan skapas Azure Cosmos DB, App Service, App Service Plan och Application Insights-resurser samt en CI/CD-pipeline. Din app distribueras sedan till Azure.

   När alla dessa processer är klara visas instrumentpanelen Azure DevOps Project i Azure-portalen. Du kan även gå till DevOps Projects-instrumentpanelen direkt från **Alla resurser** i Azure-portalen.

   Den här instrumentpanelen ger insyn i din Azure DevOps-kodlagringsplats, din CI/CD-pipeline och din Azure Cosmos DB-databas. Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps-pipeline. Till höger på instrumentpanelen väljer du **Azure Cosmos DB** för att visa dessa alternativ.

## <a name="examine-azure-cosmos-db"></a>Undersök Azure Cosmos DB

DevOps-projekt konfigurerar automatiskt Azure Cosmos DB, som du kan utforska och anpassa. Gör följande om du vill bekanta dig med Azure Cosmos DB:

1. Gå till DevOps Projects-instrumentpanelen.

    ![Instrumentpanelen DevOps-projekt](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Till höger väljer du Azure Cosmos DB. En ruta öppnas för Azure Cosmos DB. Från den här vyn kan du utföra olika åtgärder, till exempel övervakningsåtgärder och sökloggar.

    ![Portalen Azure Cosmos DB](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps Projects konfigurerar automatiskt en Azure CI/CD-pipeline i Azure DevOps-organisationen. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till DevOps Projects-instrumentpanelen.

1. Markera hyperlänken under **Skapa**. En webbläsarflik visar bygg-pipelinen för det nya projektet.

    ![Bygg fönsterruta](_img/azure-devops-project-cosmos-db/build.png)

1. Välj **Redigera**. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Build utför olika uppgifter, till exempel hämta källkod från Git-repo, skapa programmet, köra enhetstester och publicera utdata som används för distributioner.

1. Välj **Utlösare**. DevOps Projects skapar automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version. Du kan välja att inkludera eller utesluta grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra namnet på byggpipelinen till något mer beskrivande och välj sedan Spara i den rullgardinsmenyn **Spara &.Change** the name of your build pipeline to something more descriptive, and then select **Save from** the Save & queue dropdown.

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

## <a name="examine-the-cd-release-pipeline"></a>Granska CD-versionspipelinen

DevOps-projekt skapar och konfigurerar automatiskt de steg som är nödvändiga för att distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Gå till **Pipelines** och välj **Releases**.

1. Välj **Redigera**.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten.

1. Till höger om **ikonen Släpp** väljer du Kontinuerlig **distributionsutlösare**. Den här versionspipelinen har aktiverat utlösare för kontinuerlig distribution, som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan inaktivera utlösaren så att distributionerna körs manuellt.

1. Till höger väljer du avsnittet **Visa versioner** för att visa en historik över utgåvor.

1. Välj utgivningen som ska visa pipelinen. Markera valfri miljö om du vill kontrollera versionssammanfattningen, åtagandena eller tillhörande arbetsobjekt.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Visa loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Genomföra kodändringar och köra CI/CD-pipelinen

> [!NOTE]
> Följande procedur testar CI/CD-pipeline med en enkel textändring.

Du är nu redo att samarbeta med ett team i din app med hjälp av en CI/CD-process som distribuerar ditt senaste arbete till din App Service. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att checka in ändringarna till lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. På Azure DevOps-menyn väljer du **Repos** och sedan **Filer**. Gå då till din repa.

1. Repo innehåller redan kod baserat på det programspråk som du valde i skapandeprocessen. Öppna filen **Program/views/index.pug.**

1. Välj **Redigera**och gör sedan en ändring av **radnummer 15**. Du kan till exempel ändra den till "Min första distribution till Azure App Service som drivs av Azure Cosmos DB.",

1. I det övre högra hörnet väljer du **Commit**och väljer sedan **Commit** igen för att driva ändringen.

     Efter några sekunder startar en version i Azure DevOps och en version körs för att distribuera ändringarna. Övervaka versionstillståndet med instrumentpanelen för DevOps Projects eller i webbläsaren med din Azure DevOps-organisation.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de relaterade resurser som du har skapat när du inte behöver dem längre. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda DevOps-projekt för att distribuera en Node.js-app som drivs av Azure Cosmos DB
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Undersök Azure Cosmos DB
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

Mer information och nästa steg [finns i Definiera cd-pipelinen (Multi-stage continuous deployment)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) för mer information och nästa steg.


