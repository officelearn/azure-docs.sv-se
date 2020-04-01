---
title: Konfigurera kontinuerlig distribution
description: Lär dig hur du aktiverar CI/CD till Azure App Service från GitHub, BitBucket, Azure Repos eller andra repos. Välj den byggpipeline som passar dina behov.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437244"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuerlig distribution till Azure App Service

[Azure App Service](overview.md) möjliggör kontinuerlig distribution från GitHub-, BitBucket- och [Azure-databaser](https://azure.microsoft.com/services/devops/repos/) genom att dra in de senaste uppdateringarna. Den här artikeln visar hur du använder Azure-portalen för att kontinuerligt distribuera din app via Kudu-byggtjänsten eller [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Mer information om källkontrolltjänsterna finns i [Skapa en repo (GitHub),] [Skapa en repo (BitBucket)]eller [Skapa en ny Git-repo (Azure Repos).]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Auktorisera Azure App-tjänst 

Om du vill använda Azure Repos kontrollerar du att din Azure DevOps-organisation är länkad till din Azure-prenumeration. Mer information finns i [Konfigurera ett Azure DevOps Services-konto så att det kan distribueras till en webbapp](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

För Bitbucket eller GitHub godkänner du Azure App Service att ansluta till din databas. Du behöver bara auktorisera med en källkontrolltjänst en gång. 

1. Sök efter **App Services** och välj i [Azure-portalen.](https://portal.azure.com)

   ![Sök efter apptjänster.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Välj den apptjänst som du vill distribuera.

   ![Välj din app.](media/app-service-continuous-deployment/select-your-app.png)
   
1. På appsidan väljer du **Distributionscenter** på den vänstra menyn.
   
1. På sidan **Distributionscenter** väljer du **GitHub** eller **Bitbucket**och väljer sedan **Auktorisera**. 
   
   ![Välj källkontrolltjänst och välj sedan Auktorisera.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Logga in på tjänsten om det behövs och följ auktoriseringsanspråken. 

## <a name="enable-continuous-deployment"></a>Aktivera kontinuerlig distribution 

När du har auktoriserat en källkontrolltjänst konfigurerar du appen för kontinuerlig distribution via den inbyggda Build-servern för [Kudu App Service](#option-1-kudu-app-service) eller via [Azure Pipelines](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Alternativ 1: Kudu App Service

Du kan använda den inbyggda Build-servern för Kudu App Service för att kontinuerligt distribuera från GitHub, Bitbucket eller Azure Repos. 

1. Sök efter **App Services**i [Azure-portalen](https://portal.azure.com)och välj sedan den Apptjänst som du vill distribuera. 
   
1. På appsidan väljer du **Distributionscenter** på den vänstra menyn.
   
1. Välj din auktoriserade källkontrollleverantör på sidan **Distributionscenter** och välj **Fortsätt**. För GitHub eller Bitbucket kan du också välja **Ändra konto** för att ändra det behöriga kontot. 
   
   > [!NOTE]
   > Om du vill använda Azure Repos kontrollerar du att din Azure DevOps Services-organisation är länkad till din Azure-prenumeration. Mer information finns i [Konfigurera ett Azure DevOps Services-konto så att det kan distribueras till en webbapp](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. För GitHub eller Azure Repos väljer du **App Service-byggtjänst**på sidan **Byggprovider** och väljer sedan **Fortsätt**. Bitbucket använder alltid App Service-byggtjänsten.
   
   ![Välj App Service build-tjänst och välj sedan Fortsätt.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. På sidan **Konfigurera:**
   
   - För GitHub kan du listruta och välja den **organisation,** **databas**och **gren som** du vill distribuera kontinuerligt.
     
     > [!NOTE]
     > Om du inte ser några databaser kan du behöva godkänna Azure App Service i GitHub. Bläddra till din GitHub-databas och gå till **Inställningar** > **Program** > **AuktoriseradE OAuth Apps**. Välj **Azure App Service**och välj sedan **Bevilja**. För organisationsdatabaser måste du vara ägare till organisationen för att kunna bevilja behörigheterna.
     
   - För Bitbucket väljer du det Bitbucket-team, **en databas**och **gren som** du vill distribuera kontinuerligt. **Team**
     
   - För Azure Repos väljer du **Azure DevOps Organization,** **Project,** **Repository**och **Branch** som du vill distribuera kontinuerligt.
     
     > [!NOTE]
     > Om din Azure DevOps-organisation inte finns med i listan kontrollerar du att den är länkad till din Azure-prenumeration. Mer information finns i [Konfigurera ett Azure DevOps Services-konto så att det kan distribueras till en webbapp](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)..
     
1. Välj **Fortsätt**.
   
   ![Fyll i databasinformation och välj sedan Fortsätt.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. När du har konfigurerat byggprovidern granskar du inställningarna på **sidan Sammanfattning** och väljer sedan **Slutför**.
   
1. Nya åtaganden i den valda databasen och grenen distribueras nu kontinuerligt till apptjänstappen. Du kan spåra åtaganden och distributioner på **sidan Distributionscenter.**
   
   ![Spåra åtaganden och distributioner i Distributionscenter](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Alternativ 2: Azure-pipelines 

Om ditt konto har de behörigheter som krävs kan du konfigurera Azure Pipelines för kontinuerlig distribution från GitHub eller Azure Repos. Mer information om hur du distribuerar via Azure Pipelines finns i [Distribuera en webbapp till Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

#### <a name="prerequisites"></a>Krav

För Azure App Service för att skapa kontinuerlig leverans med Azure Pipelines bör din Azure DevOps-organisation ha följande behörigheter: 

- Ditt Azure-konto måste ha behörighet att skriva till Azure Active Directory och skapa en tjänst. 
  
- Ditt Azure-konto måste ha **ägarrollen** i din Azure-prenumeration.

- Du måste vara administratör i Azure DevOps-projektet som du vill använda.

#### <a name="github--azure-pipelines"></a>GitHub + Azure Pipelines

1. Sök efter **App Services**i [Azure-portalen](https://portal.azure.com)och välj sedan den Apptjänst som du vill distribuera. 
   
1. På appsidan väljer du **Distributionscenter** på den vänstra menyn.

1. Välj **GitHub** som källkontrollprovider på sidan **Distributionscenter** och välj **Fortsätt**. För **GitHub**kan du välja **Ändra konto** för att ändra det behöriga kontot.

    ![källkontroll](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. På sidan **Byggprovider** väljer du **Azure Pipelines (förhandsversion)** och väljer sedan **Fortsätt**.

    ![byggleverantör](media/app-service-continuous-deployment/select-build-provider.png)
   
1. På sidan **Konfigurera** i avsnittet **Kod** väljer du den **organisation,** **databas**och **gren** som du vill distribuera kontinuerligt och väljer **Fortsätt**.
     
     > [!NOTE]
     > Om du inte ser några databaser kan du behöva godkänna Azure App Service i GitHub. Bläddra till din GitHub-databas och gå till **Inställningar** > **Program** > **AuktoriseradE OAuth Apps**. Välj **Azure App Service**och välj sedan **Bevilja**. För organisationsdatabaser måste du vara ägare till organisationen för att kunna bevilja behörigheterna.
       
    I avsnittet **Skapa** anger du Azure DevOps Organization, Project, språkramverket som Azure Pipelines ska använda för att köra byggaktiviteter och väljer sedan **Fortsätt**.

   ![byggleverantör](media/app-service-continuous-deployment/build-configure.png)

1. När du har konfigurerat byggprovidern granskar du inställningarna på **sidan Sammanfattning** och väljer sedan **Slutför**.

   ![byggleverantör](media/app-service-continuous-deployment/summary.png)
   
1. Nya åtaganden i den valda databasen och grenen distribueras nu kontinuerligt till din App-tjänst. Du kan spåra åtaganden och distributioner på **sidan Distributionscenter.**
   
   ![Spåra åtaganden och distributioner i Distributionscenter](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Azure Pipelines

1. Sök efter **App Services**i [Azure-portalen](https://portal.azure.com)och välj sedan den Apptjänst som du vill distribuera. 
   
1. På appsidan väljer du **Distributionscenter** på den vänstra menyn.

1. Välj **Azure Repos** som källkontrollprovider på sidan **Distributionscenter** och välj **Fortsätt**.

    ![källkontroll](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. På sidan **Byggprovider** väljer du **Azure Pipelines (förhandsversion)** och väljer sedan **Fortsätt**.

    ![källkontroll](media/app-service-continuous-deployment/azure-pipelines.png)

1. På sidan **Konfigurera** i avsnittet **Kod** väljer du den **organisation,** **databas**och **gren** som du vill distribuera kontinuerligt och väljer **Fortsätt**.

   > [!NOTE]
   > Om din befintliga Azure DevOps-organisation inte finns med i listan kan du behöva länka den till din Azure-prenumeration. Mer information finns i [Definiera cd-versionspipelinen](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   I avsnittet **Skapa** anger du Azure DevOps Organization, Project, språkramverket som Azure Pipelines ska använda för att köra byggaktiviteter och väljer sedan **Fortsätt**.

   ![byggleverantör](media/app-service-continuous-deployment/build-configure.png)

1. När du har konfigurerat byggprovidern granskar du inställningarna på **sidan Sammanfattning** och väljer sedan **Slutför**.  
     
   ![byggleverantör](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. Nya åtaganden i den valda databasen och grenen distribueras nu kontinuerligt till din App-tjänst. Du kan spåra åtaganden och distributioner på **sidan Distributionscenter.**

## <a name="disable-continuous-deployment"></a>Inaktivera kontinuerlig distribution

Om du vill inaktivera kontinuerlig distribution väljer du **Koppla från** högst upp på appens **sida distributionscenter.**

![Inaktivera kontinuerlig distribution](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Använda repos som inte stöds

För Windows-appar kan du manuellt konfigurera kontinuerlig distribution från en moln-Git- eller Mercurial-databas som portalen inte direkt stöder, till exempel [GitLab](https://gitlab.com/). Du gör det genom att välja rutan Extern på sidan **Distributionscenter.** Mer information finns i [Konfigurera kontinuerlig distribution med hjälp av manuella steg](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Ytterligare resurser

* [Undersöka vanliga problem med kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Använda Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Git-dokumentation](https://git-scm.com/documentation)
* [Kudu-projektet](https://github.com/projectkudu/kudu/wiki)

[Skapa en repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Skapa en repo (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Skapa en ny Git-repa (Azure Repos)]: /azure/devops/repos/git/creatingrepo
