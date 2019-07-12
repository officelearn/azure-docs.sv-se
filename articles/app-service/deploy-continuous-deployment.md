---
title: Kontinuerlig distribution – Azure App Service | Microsoft Docs
description: Läs om hur du aktiverar kontinuerlig distribution till Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin;dariagrigoriu
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3a207f5513c24f2c33571df929f7490ec7f2eba5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836845"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuerlig distribution till Azure App Service

[Azure App Service](overview.md) möjliggör kontinuerlig distribution från GitHub, BitBucket, och [Azure lagringsplatser](https://azure.microsoft.com/services/devops/repos/) databaser genom att dra i de senaste uppdateringarna. Den här artikeln visar hur du använder Azure-portalen för att kontinuerligt distribuera appen via Kudu build-tjänsten eller [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Läs mer på Källkontrolltjänster [skapa en repo (GitHub)], [skapa en repo (BitBucket)], eller [skapa en ny Git-lagringsplats (Azure-databaser)].

Att manuellt konfigurera kontinuerlig distribution från en molnlagringsplats som portalen inte direkt stöd för, till exempel [GitLab](https://gitlab.com/), se [ställer in kontinuerlig distribution med manuella steg](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Auktorisera Azure App Service 

Kontrollera att din Azure DevOps-organisation är länkad till din Azure-prenumeration om du vill använda Azure-databaser. Mer information finns i [skapar ett konto för Azure DevOps-tjänsterna så att den kan distribuera till en webbapp](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Auktorisera Azure App Service för att ansluta till din lagringsplats för Bitbucket eller GitHub. Du behöver bara godkänna med en tjänst för käll-kontroll en gång. 

1. Välj **Apptjänster** i den [Azure-portalen](https://portal.azure.com) vänstra navigeringsrutan och välj sedan den webbapp som du vill distribuera. 
   
1. På sidan, väljer **Deployment Center** på den vänstra menyn.
   
1. På den **Deployment Center** väljer **GitHub** eller **Bitbucket**, och välj sedan **auktorisera**. 
   
   ![Välj kontrolltjänst för källa och sedan auktorisera.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Logga in på tjänsten om det behövs och följ anvisningarna för auktorisering. 

## <a name="enable-continuous-deployment"></a>Aktivera kontinuerlig distribution 

När du godkänner en tjänst för kontroll av källa måste du konfigurera din app för kontinuerlig distribution genom inbyggda [App Service Kudu-versionsserver](#option-1-use-the-app-service-build-service), eller via [Azure Pipelines](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Alternativ 1: Använda tjänsten build App Service

Du kan använda den inbyggda App Service Kudu-versionsserver att distribuera kontinuerligt från GitHub, Bitbucket eller Azure-databaser. 

1. Välj **Apptjänster** i den [Azure-portalen](https://portal.azure.com) vänstra navigeringsrutan och välj sedan den webbapp som du vill distribuera. 
   
1. På sidan, väljer **Deployment Center** på den vänstra menyn.
   
1. Välj din auktoriserade källkontrollsleverantör på den **Deployment Center** och välj **Fortsätt**. För GitHub eller Bitbucket, kan du också välja **ändra konto** att ändra kontot som är auktoriserade. 
   
   > [!NOTE]
   > Kontrollera att organisationen Azure DevOps-tjänsterna är länkad till din Azure-prenumeration om du vill använda Azure-databaser. Mer information finns i [skapar ett konto för Azure DevOps-tjänsterna så att den kan distribuera till en webbapp](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
   
1. För GitHub- eller Azure-databaser på den **Build-provider** väljer **Apptjänst byggtjänst**, och välj sedan **Fortsätt**. Bitbucket använder alltid byggtjänsten App Service.
   
   ![Välj App Service-byggtjänsten och sedan fortsätta.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. På den **konfigurera** sidan:
   
   - För GitHub, nedrullningsbar listruta och väljer den **organisation**, **databasen**, och **gren** du vill distribuera kontinuerligt.
     
     > [!NOTE]
     > Om du inte ser alla databaser, kan du behöva auktorisera Azure App Service i GitHub. Bläddra till din GitHub-lagringsplats och gå till **inställningar** > **program** > **behörighet OAuth appar**. Välj **Azure App Service**, och välj sedan **bevilja**.
     
   - För Bitbucket, väljer du Bitbucket **Team**, **databasen**, och **gren** du vill distribuera kontinuerligt.
     
   - Azure-lagringsplatser, Välj den **Azure DevOps-organisation**, **projekt**, **databasen**, och **gren** du vill distribuera kontinuerligt.
     
     > [!NOTE]
     > Om din Azure DevOps-organisation inte visas, kontrollera att den är länkad till din Azure-prenumeration. Mer information finns i [skapar ett konto för Azure DevOps-tjänsterna så att den kan distribuera till en webbapp](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)...
     
1. Välj **Fortsätt**.
   
   ![Fyll i informationen om lagringsplats och välj sedan Fortsätt.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. När du har konfigurerat build-provider kan du granska inställningarna på den **sammanfattning** och välj sedan **Slutför**.
   
   Nya allokeringarna i valda lagringsplatsen och grenen är nu distribuera kontinuerligt i din App Service-app. Du kan spåra incheckningar och distributioner på den **Deployment Center** sidan.
   
   ![Spåra incheckningar och distributioner i Deployment Center](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Alternativ 2: Använd Azure Pipelines 

Om ditt konto har behörigheterna som krävs, kan du ställa in Azure Pipelines att distribuera kontinuerligt från GitHub eller Azure-lagringsplatser lagringsplatser. Mer information om hur du distribuerar via Azure-Pipelines finns i [distribuera en webbapp till Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

För Azure App Service för att skapa kontinuerlig leverans Azure Pipelines i din Azure DevOps-organisation: 

- Azure-kontot måste ha behörighet att skriva till Azure Active Directory och skapa en tjänst. 
  
- Azure-kontot måste ha den **ägare** roll i din Azure-prenumeration.

- Du måste vara en administratör i Azure DevOps-projektet som du vill använda.

Så här konfigurerar du Azure Pipelines (förhandsversion):

1. Välj **Apptjänster** i den [Azure-portalen](https://portal.azure.com) vänstra navigeringsrutan och välj sedan den webbapp som du vill distribuera. 
   
1. På sidan, väljer **Deployment Center** på den vänstra menyn.
   
1. På den **Build-provider** väljer **Azure Pipelines (förhandsversion)** , och välj sedan **Fortsätt**. 
   
1. På den **konfigurera** sidan den **kod** avsnittet:
   
   - För GitHub, nedrullningsbar listruta och väljer den **organisation**, **databasen**, och **gren** du vill distribuera kontinuerligt.
     
     > [!NOTE]
     > Om du inte ser alla databaser, kan du behöva auktorisera Azure App Service i GitHub. Bläddra till din GitHub-lagringsplats och gå till **inställningar** > **program** > **behörighet OAuth appar**. Välj **Azure App Service**, och välj sedan **bevilja**.
     
   - Azure-lagringsplatser, Välj den **Azure DevOps-organisation**, **projekt**, **databasen**, och **gren** du vill distribuera kontinuerligt eller konfigurera en ny Azure DevOps-organisation.
     
     > [!NOTE]
     > Om din befintliga Azure DevOps-organisation inte visas kan du behöva länka den till din Azure-prenumeration. Mer information finns i [definiera din version CD-pipeline](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Välj **Fortsätt**.
   
1. För Azure-databaser i den **skapa** anger språkramverk som Azure Pipelines ska använda för att köra build-uppgifter och välj sedan **Fortsätt**.
   
1. På den **Test** väljer du om du vill aktivera belastningstester och välj sedan **Fortsätt**.
   
1. Beroende på App Service-planen [prisnivån](https://azure.microsoft.com/pricing/details/app-service/plans/), du kan se en **distribuera till mellanlagring** sidan. Välj om du vill [aktivera distributionsplatser](deploy-staging-slots.md), och välj sedan **Fortsätt**.
   
   > [!NOTE]
   > Azure Pipelines låter inte kontinuerlig leverans till produktionsplatsen. Den här begränsningen förhindrar oavsiktlig distributioner till produktion. Konfigurera kontinuerlig leverans till en mellanlagringsplats, kontrollera ändringarna där och sedan växla platserna när du är klar.
   
1. När du har konfigurerat build-provider kan du granska inställningarna på den **sammanfattning** och välj sedan **Slutför**.
   
   Nya allokeringarna i valda lagringsplatsen och grenen är nu distribuera kontinuerligt i din App Service-app. Du kan spåra incheckningar och distributioner på den **Deployment Center** sidan.
   
   ![Spåra incheckningar och distributioner i Deployment Center](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Inaktivera kontinuerlig distribution

Om du vill inaktivera kontinuerlig distribution, Välj **Disconnect** överst i din app **Deployment Center** sidan.

![Inaktivera kontinuerlig distribution](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Ytterligare resurser

* [Undersöka vanliga problem med kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Använda Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Git-dokumentation](https://git-scm.com/documentation)
* [Kudu-projektet](https://github.com/projectkudu/kudu/wiki)

[Skapa en repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Skapa en repo (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Skapa en ny Git-lagringsplats (Azure-databaser)]: /azure/devops/repos/git/creatingrepo
