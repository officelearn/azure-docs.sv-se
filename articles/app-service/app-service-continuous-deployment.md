---
title: Kontinuerlig distribution till Azure App Service | Microsoft Docs
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
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: 9dead2c8801340a736b6f67010a85f3dd34fbe85
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820270"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuerlig distribution till Azure App Service
Den här artikeln visar hur du konfigurerar kontinuerlig distribution för [Azure App Service](app-service-web-overview.md). App Service möjliggör kontinuerlig distribution från BitBucket, GitHub, och [Azure DevOps-tjänsterna](https://www.visualstudio.com/team-services/) genom att dra i de senaste uppdateringarna från din befintliga lagringsplats i någon av dessa tjänster.

Att ta reda på hur du konfigurerar kontinuerlig distribution manuellt från en molnlagringsplats som inte visas i Azure Portal (till exempel [GitLab](https://gitlab.com/)), se [ställa in kontinuerlig distribution med manuella steg](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publicera din förberedda lagringsplats till en av tjänsterna som stöds. Mer information om hur du publicerar projektet till dessa tjänster finns i [skapa en repo (GitHub)], [skapa en repo (BitBucket)], och [Kom igång med Azure DevOps-tjänsterna].

## <a name="deploy-continuously-from-github"></a>Distribuera kontinuerligt från GitHub

Om du vill aktivera kontinuerlig distribution med GitHub, navigera till din App Service-appsida i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **GitHub** > **auktorisera**. Följ anvisningarna för auktorisering. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Du behöver bara en gång auktorisera med GitHub. Om du redan har auktoriserats kommer du bara på **Fortsätt**. Du kan ändra det auktoriserade GitHub-kontot genom att klicka på **ändra konto**.

![](media/app-service-continuous-deployment/github-continue.png)

I den **Build-provider** , Välj build-provider och klicka på > **Fortsätt**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Alternativ 1: Använd App Service Kudu-versionsserver

I den **konfigurera** väljer organisation, lagringsplatsen och grenen från vilken du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>Alternativ 2: Använd kontinuerlig leverans för Azure DevOps-tjänsterna

> [!NOTE]
> För App Service för att skapa nödvändiga Azure Pipelines i din organisation med Azure DevOps-tjänsterna, Azure-kontot måste ha rollen **ägare** i Azure-prenumerationen.
>

I den **konfigurera** sidan den **kod** väljer organisation, lagringsplatsen och grenen från vilken du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

I den **konfigurera** sidan den **skapa** avsnittet, konfigurera en ny Azure DevOps-tjänsterna organisation eller ange en befintlig organisation. När du är klar klickar du på **Fortsätt**.

> [!NOTE]
> Om du vill använda en befintlig Azure DevOps-tjänsterna organisation som inte visas kan du behöva [länka Azure DevOps-tjänsterna organisationen till din Azure-prenumeration](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

I den **Test** väljer du om du vill aktivera belastningstester och klicka sedan på **Fortsätt**.

Beroende på den [prisnivån](https://azure.microsoft.com/pricing/details/app-service/plans/) för din App Service-plan kan du också se en **distribuera till mellanlagring** sidan. Välj om du vill [aktivera distributionsplatser](web-sites-staged-publishing.md), klicka sedan på **Fortsätt**.

### <a name="finish-configuration"></a>Slut konfiguration

I den **sammanfattning** kontrollerar du dina alternativ och klickar på **Slutför**.

När konfigurationen är klar distribueras nya allokeringarna i den valda databasen kontinuerligt i din App Service-app.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Distribuera kontinuerligt från BitBucket

Om du vill aktivera kontinuerlig distribution med BitBucket, navigera till din App Service-appsida i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **BitBucket** > **auktorisera**. Följ anvisningarna för auktorisering. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Du behöver bara godkänna med BitBucket en gång. Om du redan har auktoriserats kommer du bara på **Fortsätt**. Du kan ändra det auktoriserade BitBucket-kontot genom att klicka på **ändra konto**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

I den **konfigurera** väljer lagringsplatsen och grenen från vilken du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

I den **sammanfattning** kontrollerar du dina alternativ och klickar på **Slutför**.

När konfigurationen är klar distribueras nya allokeringarna i den valda databasen kontinuerligt i din App Service-app.

## <a name="deploy-continuously-from-azure-devops-services"></a>Distribuera kontinuerligt från Azure DevOps-tjänster

Om du vill aktivera kontinuerlig distribution med Azure DevOps-tjänsterna, navigera till din App Service-appsida i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **Azure DevOps-tjänsterna** > **Fortsätt**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

I den **Build-provider** , Välj build-provider och klicka på > **Fortsätt**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Alternativ 1: Använd App Service Kudu-versionsserver

I den **konfigurera** väljer du den Azure DevOps-tjänsterna organisation, projekt, lagringsplatsen och grenen från vilken du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>Alternativ 2: Använd kontinuerlig leverans för Azure DevOps-tjänsterna

> [!NOTE]
> För App Service för att skapa nödvändiga Azure Pipelines i din organisation med Azure DevOps-tjänsterna, Azure-kontot måste ha rollen **ägare** i Azure-prenumerationen.
>

I den **konfigurera** sidan den **kod** väljer du den Azure DevOps-tjänsterna organisation, projekt, lagringsplatsen och grenen från vilken du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

> [!NOTE]
> Om du vill använda en befintlig Azure DevOps-tjänsterna organisation som inte visas kan du behöva [länka Azure DevOps-tjänsterna organisationen till din Azure-prenumeration](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

I den **konfigurera** sidan den **skapa** anger språkramverk som Azure DevOps-tjänsterna ska använda för att köra build-uppgifter för valda lagringsplatsen. När du är klar klickar du på **Fortsätt**.

I den **Test** väljer du om du vill aktivera belastningstester och klicka sedan på **Fortsätt**.

Beroende på den [prisnivån](https://azure.microsoft.com/pricing/details/app-service/plans/) för din App Service-plan kan du också se en **distribuera till mellanlagring** sidan. Välj om du vill [aktivera distributionsplatser](web-sites-staged-publishing.md), klicka sedan på **Fortsätt**. 

### <a name="finish-configuration"></a>Slut konfiguration

I den **sammanfattning** kontrollerar du dina alternativ och klickar på **Slutför**.

När konfigurationen är klar distribueras nya allokeringarna i den valda databasen kontinuerligt i din App Service-app.

## <a name="disable-continuous-deployment"></a>Inaktivera kontinuerlig distribution

Om du vill inaktivera kontinuerlig distribution, navigera till din App Service-appsida i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **GitHub** eller **Azure DevOps-tjänsterna** eller **BitBucket**  >  **Koppla från**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Ytterligare resurser

* [Undersöka vanliga problem med kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Använda PowerShell för Azure]
* [Git-dokumentation]
* [Kudu-projektet](https://github.com/projectkudu/kudu/wiki)
* [Använd Azure för att automatiskt generera en CI/CD-pipeline för att distribuera en ASP.NET 4-app](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Använda PowerShell för Azure]: /powershell/azureps-cmdlets-docs
[Git-dokumentation]: http://git-scm.com/documentation

[Skapa en repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Skapa en repo (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Kom igång med Azure DevOps-tjänsterna]: https://www.visualstudio.com/docs/vsts-tfs-overview
