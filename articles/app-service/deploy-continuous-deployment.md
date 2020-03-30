---
title: Konfigurera kontinuerlig distribution
description: Lär dig hur du aktiverar CI/CD till Azure App Service från GitHub, BitBucket, Azure Repos eller andra repos. Välj den byggpipeline som passar dina behov.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: d58eb333c930d2ffac4eb57340ea776338325181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266044"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuerlig distribution till Azure App Service

[Azure App Service](overview.md) möjliggör kontinuerlig distribution från GitHub-, BitBucket- och [Azure-databaser](https://azure.microsoft.com/services/devops/repos/) genom att dra in de senaste uppdateringarna. Den här artikeln visar hur du använder Azure-portalen för att kontinuerligt distribuera din app via Kudu-byggtjänsten eller [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Mer information om källkontrolltjänsterna finns i [Skapa en repo (GitHub),] [Skapa en repo (BitBucket)]eller [Skapa en ny Git-repo (Azure Repos).]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Auktorisera Azure App-tjänst 

Om du vill använda Azure Repos kontrollerar du att din Azure DevOps-organisation är länkad till din Azure-prenumeration. Mer information finns i [Konfigurera ett Azure DevOps Services-konto så att det kan distribueras till en webbapp](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

För Bitbucket eller GitHub godkänner du Azure App Service att ansluta till din databas. Du behöver bara auktorisera med en källkontrolltjänst en gång. 

1. Sök efter och välj **App Services**i [Azure-portalen](https://portal.azure.com). 

   ![Sök efter apptjänster.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Välj den webbapp som du vill distribuera.

   ![Välj din app.](media/app-service-continuous-deployment/select-your-app.png)
   
1. På appsidan väljer du **Distributionscenter** på den vänstra menyn.
   
1. På sidan **Distributionscenter** väljer du **GitHub** eller **Bitbucket**och väljer sedan **Auktorisera**. 
   
   ![Välj källkontrolltjänst och välj sedan Auktorisera.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Logga in på tjänsten om det behövs och följ auktoriseringsanspråken. 

## <a name="enable-continuous-deployment"></a>Aktivera kontinuerlig distribution 

När du har auktoriserat en källkontrolltjänst konfigurerar du appen för kontinuerlig distribution via den inbyggda [Build-servern](#option-1-use-the-app-service-build-service)för Kudu App Service eller via [Azure Pipelines](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Alternativ 1: Använd apptjänstens byggtjänst

Du kan använda den inbyggda Build-servern för Kudu App Service för att kontinuerligt distribuera från GitHub, Bitbucket eller Azure Repos. 

1. Sök efter och välj **App Services**i [Azure-portalen](https://portal.azure.com)och välj sedan den webbapp som du vill distribuera. 
   
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
   
   Nya åtaganden i den valda databasen och grenen distribueras nu kontinuerligt till apptjänstappen. Du kan spåra åtaganden och distributioner på **sidan Distributionscenter.**
   
   ![Spåra åtaganden och distributioner i Distributionscenter](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Alternativ 2: Använd Azure Pipelines 

Om ditt konto har de behörigheter som krävs kan du konfigurera Azure Pipelines för kontinuerlig distribution från GitHub- eller Azure-databaser. Mer information om hur du distribuerar via Azure Pipelines finns i [Distribuera en webbapp till Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

För Azure App Service för att skapa kontinuerlig leverans Av Azure Pipelines i din Azure DevOps-organisation: 

- Ditt Azure-konto måste ha behörighet att skriva till Azure Active Directory och skapa en tjänst. 
  
- Ditt Azure-konto måste ha **ägarrollen** i din Azure-prenumeration.

- Du måste vara administratör i Azure DevOps-projektet som du vill använda.

Så här konfigurerar du Azure Pipelines (förhandsversion):

1. Sök efter och välj **App Services**i [Azure-portalen](https://portal.azure.com)och välj sedan den webbapp som du vill distribuera. 
   
1. På appsidan väljer du **Distributionscenter** på den vänstra menyn.
   
1. På sidan **Byggprovider** väljer du **Azure Pipelines (förhandsversion)** och väljer sedan **Fortsätt**. 
   
1. På sidan **Konfigurera** i avsnittet **Kod:**
   
   - För GitHub kan du listruta och välja den **organisation,** **databas**och **gren som** du vill distribuera kontinuerligt.
     
     > [!NOTE]
     > Om du inte ser några databaser kan du behöva godkänna Azure App Service i GitHub. Bläddra till din GitHub-databas och gå till **Inställningar** > **Program** > **AuktoriseradE OAuth Apps**. Välj **Azure App Service**och välj sedan **Bevilja**. För organisationsdatabaser måste du vara ägare till organisationen för att kunna bevilja behörigheterna.
     
   - För Azure Repos väljer du **Azure DevOps Organization,** **Project,** **Repository**och **Branch** som du vill distribuera kontinuerligt eller konfigurerar en ny Azure DevOps-organisation.
     
     > [!NOTE]
     > Om din befintliga Azure DevOps-organisation inte finns med i listan kan du behöva länka den till din Azure-prenumeration. Mer information finns i [Definiera cd-versionspipelinen](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Välj **Fortsätt**.
   
1. För Azure Repos anger du i avsnittet **Bygg** det språkramverk som Azure Pipelines ska använda för att köra bygguppgifter och väljer sedan **Fortsätt**.
   
1. På sidan **Test** väljer du om du vill aktivera belastningstester och väljer sedan **Fortsätt**.
   
1. Beroende på [prisnivån](https://azure.microsoft.com/pricing/details/app-service/plans/)för appserviceplanen kan du se sidan **Distribuera till mellanlagring.** Välj om [distributionsplatser](deploy-staging-slots.md)ska aktiveras och välj sedan **Fortsätt**.
   
   > [!NOTE]
   > Azure Pipelines tillåter inte kontinuerlig leverans till produktionsplatsen. Den här begränsningen förhindrar oavsiktliga distributioner till produktion. Ställ in kontinuerlig leverans till en mellanlagringsplats, verifiera ändringarna där och byt sedan ut kortplatserna när du är redo.
   
1. När du har konfigurerat byggprovidern granskar du inställningarna på **sidan Sammanfattning** och väljer sedan **Slutför**.
   
   Nya åtaganden i den valda databasen och grenen distribueras nu kontinuerligt till apptjänstappen. Du kan spåra åtaganden och distributioner på **sidan Distributionscenter.**
   
   ![Spåra åtaganden och distributioner i Distributionscenter](media/app-service-continuous-deployment/github-finished.png)

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
