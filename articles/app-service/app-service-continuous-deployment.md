---
title: Kontinuerlig distribution till Azure App Service | Microsoft Docs
description: "Läs om hur du aktiverar kontinuerlig distribution till Azure App Service."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: dariagrigoriu
ms.openlocfilehash: ef5924607868bcb3dc35e279539b78d5a0e17c1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuerligt distribution till Azure App Service
Den här kursen visar hur du konfigurerar ett arbetsflöde för kontinuerlig distribution för din [Azure Web Apps](app-service-web-overview.md). Apptjänst-integrering med BitBucket, GitHub och [Visual Studio Team Services VSTS ()](https://www.visualstudio.com/team-services/) aktiverar ett arbetsflöde för kontinuerlig distribution där Azure tar emot de senaste uppdateringarna från projektet publiceras på någon av dessa tjänster. Kontinuerlig distribution är ett bra alternativ för projekt där flera och ofta återkommande bidrag integreras.

Ta reda på hur du konfigurerar kontinuerlig distribution manuellt från en moln-databas som inte finns med i Azure Portal (t.ex [GitLab](https://gitlab.com/)), se [ställa in kontinuerlig distribution med manuella steg](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="overview"></a>Aktivera kontinuerlig distribution
Så här aktiverar du kontinuerlig distribution:

1. Publicera ditt appinnehåll på den lagringsplats som ska användas för kontinuerlig distribution.  
    Mer information om hur du publicerar projektet till dessa tjänster finns i avsnitten [Skapa en repo (GitHub)], [Skapa en repo (BitBucket)] samt [Kom igång med VSTS].
2. Gå till appmenyns blad i [Azure Portal] och klicka på **APPDISTRIBUTION > Distributionsalternativ**. Klicka på **Välj källa** och välj sedan distributionskällan.  
   
    ![](./media/app-service-continuous-deployment/cd_options.png)
   
   > [!NOTE]
   > Information att konfigurera ett VSTS-konto för App Service-distribution finns i denna [självstudie](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
   > 
   > 
3. Slutför auktoriseringsarbetsflödet.
4. På bladet **Distributionskälla** väljer du projektet och den förgrening som du vill distribuera från. Klicka på **OK** när du är klar.
   
    ![](./media/app-service-continuous-deployment/github_option.png)
   
   > [!NOTE]
   > När du aktiverar kontinuerlig distribution med GitHub eller BitBucket visas både offentliga och privata projekt.
   > 
   > 
   
    App Service-skapar en association till den valda lagringsplatsen, tar emot filer från den angivna förgreningen och underhåller en klon av lagringsplatsen för App Service-appen. När du konfigurerar kontinuerlig VSTS-distribution från Azure Portal använder integrationen [Kudu-distributionsmotor](https://github.com/projectkudu/kudu/wiki) för App Service, som redan automatiserar Build and Deployment-aktiviteter med varje `git push`. Du behöver inte konfigurera kontinuerlig distribution separat i VSTS. När den här processen är klar visas en aktiv distribution på appbladet **Distributionsalternativ** som anger att distributionen har slutförts.
5. Kontrollera att appen har distribuerats genom att klicka på **URL**:en överst på appbladet i Azure Portal.
6. Kontrollera att det sker en kontinuerlig distribution från önskad lagringsplats genom att ändringar push-överförs till lagringsplatsen. Din app ska uppdateras så att den avspeglar ändringarna strax efter det att push-överföringen till lagringsplatsen har slutförts. Du kan verifiera att den har hämtat uppdateringen på bladet **Distributionsalternativ** i appen.

## <a name="VSsolution"></a>Kontinuerlig distribution av en Visual Studio-lösning
Att push-överföra en Visual Studio-lösning till Azure App Service är lika enkelt som att push-överföra en enkel index.html-fil. App Service-distributionsprocessen effektiviserar all information, inklusive återställning av NuGet-beroenden, och bygger upp binärfilerna för programmet. Du kan följa bästa praxis för källkontroll genom att upprätthålla kod enbart på Git-lagringsplatsen och låta App Service-distributionen ta hand om resten.

Stegen för push-överföring av Visual Studio-lösningen till App Service är desamma som i [föregående avsnitt](#overview), förutsatt att du konfigurerar lösningen och lagringsplatsen på följande sätt:

* Använd alternativet Visual Studio-källkontroll för att skapa en `.gitignore`-fil som på bilden nedan eller lägg till en `.gitignore`-fil manuellt i lagringsplatsens rot med innehåll som liknar det här [.gitignore-exemplet](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore).
  
  ![](./media/app-service-continuous-deployment/VS_source_control.png)
* Lägg till hela lösningens katalogträd i lagringsplatsen, med .sln-filen i roten för lagringsplatsen.

När du har konfigurerat lagringsplatsen på det sätt som beskrivs, och konfigurerat appen i Azure för kontinuerlig publicering från en av Git-onlinedatabaserna, kan du utveckla ASP.NET-programmet lokalt i Visual Studio och kontinuerligt distribuera koden genom att push-överföra ändringarna till lagringsplatsen för Git-onlinedatabasen.

## <a name="disableCD"></a>Inaktivera kontinuerlig distribution
Så här inaktiverar du kontinuerlig distribution:

1. Gå till appmenyns blad i [Azure Portal] och klicka på **APPDISTRIBUTION > Distributionsalternativ**. Klicka sedan på **Koppla från** på bladet **Distributionsalternativ**.
   
    ![](./media/app-service-continuous-deployment/cd_disconnect.png)
2. När du har svarat **Ja** på bekräftelsemeddelandet kan du gå tillbaka till appbladet och klicka på **APPDISTRIBUTION > Distributionsalternativ** om du vill konfigurera publicering från en annan källa.

## <a name="additional-resources"></a>Ytterligare resurser
* [Undersöka vanliga problem med kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Använda PowerShell för Azure]
* [Använda Azures kommandoradsverktyg för Mac och Linux]
* [Git-dokumentation]
* [Kudu-projektet](https://github.com/projectkudu/kudu/wiki)
* [Använda Azure att automatiskt generera en CI/CD-pipeline för att distribuera en app för ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

> [!NOTE]
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

[Azure Portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Använda PowerShell för Azure]: /powershell/azureps-cmdlets-docs
[Använda Azures kommandoradsverktyg för Mac och Linux]:../cli-install-nodejs.md
[Git-dokumentation]: http://git-scm.com/documentation

[Skapa en repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Skapa en repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Kom igång med VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview
