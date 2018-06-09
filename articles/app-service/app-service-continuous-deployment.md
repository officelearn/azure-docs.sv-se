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
ms.openlocfilehash: d83d1ad74d04356f73f18a744c2d1509b5efc280
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233852"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuerlig distribution till Azure App Service
Den här artikeln visar hur du konfigurerar kontinuerlig distribution för [Azure App Service](app-service-web-overview.md). Apptjänst kan kontinuerlig distribution från BitBucket, GitHub och [Visual Studio Team Services VSTS ()](https://www.visualstudio.com/team-services/) genom att dra i de senaste uppdateringarna från din befintliga databas i någon av dessa tjänster.

Ta reda på hur du konfigurerar kontinuerlig distribution manuellt från en moln-databas som inte finns med i Azure Portal (t.ex [GitLab](https://gitlab.com/)), se [ställa in kontinuerlig distribution med manuella steg](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publicera förberedda databasen till en av tjänsterna som stöds. Mer information om hur du publicerar projektet till dessa tjänster finns i avsnitten [Skapa en repo (GitHub)], [Skapa en repo (BitBucket)] samt [Kom igång med VSTS].

## <a name="deploy-continuously-from-github"></a>Distribuera kontinuerligt från GitHub

Om du vill aktivera kontinuerlig distribution med GitHub, navigera till din Apptjänst appsida i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **GitHub** > **auktorisera**. Följ anvisningarna för auktorisering. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Du behöver bara godkänna med GitHub en gång. Om du redan är behörig bara på **Fortsätt**. Du kan ändra behöriga GitHub-konto genom att klicka på **ändra konto**.

![](media/app-service-continuous-deployment/github-continue.png)

I den **Build-provider** , Välj build-providern och klickar på > **Fortsätt**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Alternativ 1: Använd App Service Kudu build-server

I den **konfigurera** väljer organisationen, databasen och avdelningskontor där du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Alternativ 2: Använd VSTS kontinuerlig leverans

> [!NOTE]
> För App Service att skapa den nödvändiga versionen och frigöra definitioner i VSTS kontot är ditt Azure-konto måste ha rollen för **ägare** i din Azure-prenumeration.
>

I den **konfigurera** sidan den **kod** väljer organisationen, databasen och avdelningskontor där du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

I den **konfigurera** sidan den **skapa** avsnittet, konfigurera ett nytt konto i VSTS eller ange ett befintligt konto. När du är klar klickar du på **Fortsätt**.

> [!NOTE]
> Om du vill använda ett befintligt VSTS-konto som inte visas måste du [länka VSTS-konto till din Azure-prenumeration](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

I den **Test** väljer du om du vill aktivera belastningstester och klicka sedan på **Fortsätt**.

Beroende på den [prisnivån](/pricing/details/app-service/plans/) av din programtjänstplan kan du också se en **till mellanlagring** sidan. Välj om du vill [aktivera distributionsplatser](web-sites-staged-publishing.md), klicka på **Fortsätt**.

### <a name="finish-configuration"></a>Slut konfiguration

I den **sammanfattning** , kontrollera alternativen och klickar på **Slutför**.

När konfigurationen är klar distribueras nya incheckningar i den valda databasen kontinuerligt i din Apptjänst-app.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Distribuera kontinuerligt från BitBucket

Om du vill aktivera kontinuerlig distribution med BitBucket, gå till sidan Apptjänst-app i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **BitBucket** > **auktorisera**. Följ anvisningarna för auktorisering. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Du behöver bara godkänna med BitBucket en gång. Om du redan är behörig bara på **Fortsätt**. Du kan ändra kontot behöriga BitBucket genom att klicka på **ändra konto**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

I den **konfigurera** markerar du databasen och grenen som du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

I den **sammanfattning** , kontrollera alternativen och klickar på **Slutför**.

När konfigurationen är klar distribueras nya incheckningar i den valda databasen kontinuerligt i din Apptjänst-app.

## <a name="deploy-continuously-from-vsts"></a>Distribuera kontinuerligt från VSTS

Om du vill aktivera kontinuerlig distribution med VSTS navigerar du till sidan Apptjänst-app i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **VSTS** > **Fortsätt**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

I den **Build-provider** , Välj build-providern och klickar på > **Fortsätt**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Alternativ 1: Använd App Service Kudu build-server

I den **konfigurera** väljer VSTS konto, projekt, databasen och grenen du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Alternativ 2: Använd VSTS kontinuerlig leverans

> [!NOTE]
> För App Service att skapa den nödvändiga versionen och frigöra definitioner i VSTS kontot är ditt Azure-konto måste ha rollen för **ägare** i din Azure-prenumeration.
>

I den **konfigurera** sidan den **kod** väljer VSTS konto, projekt, databasen och grenen du vill distribuera kontinuerligt. När du är klar klickar du på **Fortsätt**.

> [!NOTE]
> Om du vill använda ett befintligt VSTS-konto som inte visas måste du [länka VSTS-konto till din Azure-prenumeration](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

I den **konfigurera** sidan den **skapa** ange språkramverket VSTS ska använda för att köra build-uppgifter för din valda databas. När du är klar klickar du på **Fortsätt**.

I den **Test** väljer du om du vill aktivera belastningstester och klicka sedan på **Fortsätt**.

Beroende på den [prisnivån](/pricing/details/app-service/plans/) av din programtjänstplan kan du också se en **till mellanlagring** sidan. Välj om du vill [aktivera distributionsplatser](web-sites-staged-publishing.md), klicka på **Fortsätt**. 

### <a name="finish-configuration"></a>Slut konfiguration

I den **sammanfattning** , kontrollera alternativen och klickar på **Slutför**.

När konfigurationen är klar distribueras nya incheckningar i den valda databasen kontinuerligt i din Apptjänst-app.

## <a name="disable-continuous-deployment"></a>Inaktivera kontinuerlig distribution

För att inaktivera kontinuerlig distribution, gå till sidan Apptjänst-app i den [Azure-portalen](https://portal.azure.com).

I den vänstra menyn klickar du på **Deployment Center** > **GitHub** eller **VSTS** eller **BitBucket**  >  **Koppla från**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Ytterligare resurser

* [Undersöka vanliga problem med kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Använda PowerShell för Azure]
* [Git-dokumentation]
* [Kudu-projektet](https://github.com/projectkudu/kudu/wiki)
* [Använda Azure att automatiskt generera en CI/CD-pipeline för att distribuera en app för ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Använda PowerShell för Azure]: /powershell/azureps-cmdlets-docs
[Git-dokumentation]: http://git-scm.com/documentation

[Skapa en repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Skapa en repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Kom igång med VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview
