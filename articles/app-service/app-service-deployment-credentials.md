---
title: "Autentiseringsuppgifter för distribution av Azure App Service | Microsoft Docs"
description: "Lär dig hur du använder autentiseringsuppgifter för Azure App Service-distribution."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: d66b5aa4eb2ad90596dfe9e26bbc18996c967295
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurera autentiseringsuppgifter för distribution för Azure App Service
[Azure Apptjänst](http://go.microsoft.com/fwlink/?LinkId=529714) stöder två typer av autentiseringsuppgifter för [lokal Git-distribution](app-service-deploy-local-git.md) och [FTP/S distribution](app-service-deploy-ftp.md). Dessa är inte samma som din Azure Active Directory-autentiseringsuppgifter.

* **Användarnivå autentiseringsuppgifter**: en uppsättning autentiseringsuppgifter för hela Azure-konto. Det kan användas för att distribuera till App Service för en app i en prenumeration som Azure-konto har behörighet att komma åt. Det här är en standarduppsättning autentiseringsuppgifter som du konfigurerar i **Apptjänster** > **&lt;appnamn >** > **distributionsbehörigheterna**. Detta är standardvärdet som angetts i GUI-portalen (som den **översikt** och **egenskaper** för din app [resurssida](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > När du delegerar åtkomst till Azure-resurser via rollbaserad åtkomstkontroll (RBAC) eller medadministratör behörigheter kan varje Azure användare som får åtkomst till en app använda hans/hennes personliga användarnivå autentiseringsuppgifter förrän åtkomst har återkallats. Dessa autentiseringsuppgifter för distribution bör inte delas med andra Azure-användare.
    >
    >

* **App-nivå autentiseringsuppgifter**: en uppsättning autentiseringsuppgifter för varje app. Den kan användas för att distribuera till appen endast. Autentiseringsuppgifterna publiceringsprofil för varje app ska genereras automatiskt vid appen skapas och finns i appens. Du kan inte manuellt konfigurera autentiseringsuppgifterna, men kan du återställa dem till en app när som helst.

    > [!NOTE]
    > För att ge någon åtkomst till dessa autentiseringsuppgifter via rollbaserad åtkomstkontroll (RBAC), måste du se dem deltagare eller högre kontinuerligt i webbprogrammet. Läsare tillåts inte att publicera, och därför inte åtkomst till dessa autentiseringsuppgifter.
    >
    >

## <a name="userscope"></a>Ange och återställa användarnivå autentiseringsuppgifter

Du kan konfigurera autentiseringsuppgifterna användarnivå i alla appar [resurssida](../azure-resource-manager/resource-group-portal.md#manage-resources). Oavsett vilken app du konfigurera dessa autentiseringsuppgifter, gäller det för alla appar och för alla prenumerationer i ditt Azure-konto. 

Konfigurera autentiseringsuppgifterna användarnivå:

1. I den [Azure-portalen](https://portal.azure.com), klicka på Apptjänst >  **&lt;any_app >** > **distributionsbehörigheterna**.

    > [!NOTE]
    > Du måste ha minst en app innan du kan öppna inloggningssidan för distribution i portalen. Med den [Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set), kan du konfigurera användarnivå autentiseringsuppgifter utan en befintlig app.

2. Konfigurera användarnamn och lösenord och klicka sedan på **spara**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

När du har angett dina autentiseringsuppgifter för distribution, du kan hitta den *Git* användarnamn för distribution i din app **översikt**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

och *FTP* användarnamn för distribution i din app **egenskaper**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure visar inte lösenordet på användarnivå distribution. Om du glömmer bort lösenordet kan du hämta den. Du kan dock återställa dina autentiseringsuppgifter genom att följa stegen i det här avsnittet.
>
>  

## <a name="appscope"></a>Hämta och Återställ autentiseringsuppgifterna för app-nivå
För varje app i App Service app-nivå autentiseringsuppgifterna lagras i XML-Publicera profil.

Hämta autentiseringsuppgifter för app-nivå:

1. I den [Azure-portalen](https://portal.azure.com), klicka på Apptjänst >  **&lt;any_app >** > **översikt**.

2. Klicka på **... Flera** > **Get publiceringsprofil**, och börjar hämtas för en. PublishSettings-filen.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Öppna den. PublishSettings-filen och hitta det `<publishProfile>` tagg med attributet `publishMethod="FTP"`. Sedan, dess `userName` och `password` attribut.
Dessa är autentiseringsuppgifterna som app-nivå.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Liksom för användarnivå autentiseringsuppgifter, användarnamn för FTP-distribution finns i formatet `<app_name>\<username>`, och användarnamn för Git-distribution är bara `<username>` utan föregående `<app_name>\`.

Så här återställer du app-nivå autentiseringsuppgifter:

1. I den [Azure-portalen](https://portal.azure.com), klicka på Apptjänst >  **&lt;any_app >** > **översikt**.

2. Klicka på **... Flera** > **Återställ publiceringsprofil**. Klicka på **Ja** bekräfta återställningen.

    Reset-åtgärden upphäver någon tidigare hämtade. PublishSettings-filer.

## <a name="next-steps"></a>Nästa steg

Ta reda på hur du använder dessa autentiseringsuppgifter för att distribuera din app från [lokala Git](app-service-deploy-local-git.md) eller med hjälp av [FTP/S](app-service-deploy-ftp.md).
