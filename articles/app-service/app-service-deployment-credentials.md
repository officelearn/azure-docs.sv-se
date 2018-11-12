---
title: Autentiseringsuppgifter för Azure App Service-distribution | Microsoft Docs
description: Lär dig hur du använder autentiseringsuppgifter för Azure App Service-distribution.
services: app-service
documentationcenter: ''
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
ms.openlocfilehash: 79841887ce8413970cb7dff0ca0099d4e0745a68
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259317"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurera autentiseringsuppgifter för distribution för Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) stöder två typer av autentiseringsuppgifter för [lokal Git-distribution](app-service-deploy-local-git.md) och [FTP/S distribution](app-service-deploy-ftp.md). Det är inte samma som din Azure Active Directory-autentiseringsuppgifter.

* **Användarnivå autentiseringsuppgifter**: en uppsättning autentiseringsuppgifter för hela Azure-konto. Den kan användas för att distribuera till App Service för en app i alla prenumerationer som Azure-kontot har behörighet att komma åt. Det här är en standarduppsättning autentiseringsuppgifter som du konfigurerar i **Apptjänster** > **&lt;app_name >** > **distributionsbehörigheterna**. Detta är standardinställningen som visas i portalen GUI (till exempel den **översikt** och **egenskaper** för din app [resurssida](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > När du delegerar åtkomst till Azure-resurser via rollbaserad åtkomstkontroll (RBAC) eller medadministratör behörigheter kan varje Azure-användare som får åtkomst till en app använda hans/hennes personliga användarnivå autentiseringsuppgifter förrän åtkomst har återkallats. Dessa autentiseringsuppgifter för distribution ska inte delas med andra Azure-användare.
    >
    >

* **Appnivå autentiseringsuppgifter**: en uppsättning autentiseringsuppgifter för varje app. Den kan användas för att distribuera till appen endast. Autentiseringsuppgifterna publiceringsprofil för varje app ska genereras automatiskt vid appskapande och finns i appens. Du kan inte konfigurera autentiseringsuppgifterna manuellt, men du kan återställa dem för en app när som helst.

    > [!NOTE]
    > För att ge någon åtkomst till dessa autentiseringsuppgifter via rollbaserad åtkomstkontroll (RBAC), måste du se dem deltagare eller senare på Webbappen. Läsare tillåts inte att publicera, och kan därför inte åtkomst till dessa autentiseringsuppgifter.
    >
    >

## <a name="userscope"></a>Ange och Återställ användarnivå autentiseringsuppgifter

Du kan konfigurera dina autentiseringsuppgifter på användarnivå i alla appar [resurssida](../azure-resource-manager/resource-group-portal.md#manage-resources). Oavsett vilken app du konfigurera dessa autentiseringsuppgifter, gäller det för alla appar och för alla prenumerationer i ditt Azure-konto. 

Så här konfigurerar du dina autentiseringsuppgifter för användarnivå:

1. I den [Azure-portalen](https://portal.azure.com), klickar du på App Service >  **&lt;any_app >** > **distributionsbehörigheterna**.

    > [!NOTE]
    > Du måste ha minst en app innan du kan komma åt sidan autentiseringsuppgifter för distribution i portalen. Men med den [Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), du kan konfigurera användarnivå autentiseringsuppgifter utan en befintlig app.

2. Ange det användarnamn och lösenord och klicka sedan på **spara**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

När du har angett dina autentiseringsuppgifter för distribution, kan du hitta den *Git* användarnamn för distribution i din app **översikt**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

och *FTP* användarnamn för distribution i din app **egenskaper**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure visar inte ditt distributionslösenord på användarnivå. Om du glömmer lösenordet kan inte kan du hämta den. Du kan dock återställa dina autentiseringsuppgifter genom att följa stegen i det här avsnittet.
>
>  

## <a name="appscope"></a>Hämta och återställa appnivå autentiseringsuppgifter
För varje app i App Service, dess appnivå autentiseringsuppgifter lagras i XML publiceringsprofil.

Hämta autentiseringsuppgifterna som app-nivå:

1. I den [Azure-portalen](https://portal.azure.com), klickar du på App Service >  **&lt;any_app >** > **översikt**.

2. Klicka på **... Mer** > **hämta publiceringsprofil**, och börjar hämtas för en. PublishSettings-filen.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Öppna den. PublishSettings-filen och hitta den `<publishProfile>` tagg med attributet `publishMethod="FTP"`. Sedan, dess `userName` och `password` attribut.
Det här är autentiseringsuppgifterna som app-nivå.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Liknar autentiseringsuppgifterna som användarnivå, användarnamn för FTP-distribution är i formatet `<app_name>\<username>`, och användarnamn för Git-distribution är bara `<username>` utan föregående `<app_name>\`.

Så här återställer appnivå autentiseringsuppgifter:

1. I den [Azure-portalen](https://portal.azure.com), klickar du på App Service >  **&lt;any_app >** > **översikt**.

2. Klicka på **... Mer** > **Återställ publiceringsprofil**. Klicka på **Ja** att bekräfta återställningen.

    Åtgärden Återställ upphäver någon tidigare hämtade. PublishSettings-filer.

## <a name="next-steps"></a>Nästa steg

Ta reda på hur du använder dessa autentiseringsuppgifter för att distribuera en app från [lokal Git](app-service-deploy-local-git.md) eller med hjälp av [FTP/S](app-service-deploy-ftp.md).
