---
title: Konfigurera autentiseringsuppgifter för distribution – Azure App Service | Microsoft Docs
description: Lär dig hur du använder autentiseringsuppgifter för Azure App Service-distribution.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/22/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 9f1d6714dd015af857c5e6156bc7254a944363c4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257194"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurera autentiseringsuppgifter för distribution för Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) stöder två typer av autentiseringsuppgifter för [lokal Git-distribution](app-service-deploy-local-git.md) och [FTP/S distribution](app-service-deploy-ftp.md). Det är inte samma som din Azure Active Directory-autentiseringsuppgifter.

* **Användarnivå autentiseringsuppgifter**: en uppsättning autentiseringsuppgifter för hela Azure-konto. Den kan användas för att distribuera till App Service för en app i alla prenumerationer som Azure-kontot har behörighet att komma åt. Det är en standarduppsättning som visas i portalen GUI (som den **översikt** och **egenskaper** för appens [resurssida](../azure-resource-manager/resource-group-portal.md#manage-resources)). När en användare beviljas åtkomst till appen via rollbaserad åtkomstkontroll (RBAC) eller coadmin behörigheter kan användaren använda sina egna användarnivå autentiseringsuppgifter förrän åtkomst har återkallats. Dela inte dessa autentiseringsuppgifter med andra Azure-användare.

* **Appnivå autentiseringsuppgifter**: en uppsättning autentiseringsuppgifter för varje app. Den kan användas för att distribuera till appen endast. Autentiseringsuppgifterna för varje app som genereras automatiskt när en app skapas. De kan inte konfigureras manuellt, men kan återställas när som helst. För en användare beviljas åtkomst på appnivå autentiseringsuppgifter via (RBAC), som är deltagare eller senare på appen. Läsare tillåts inte att publicera, och det går inte att få åtkomst till dessa autentiseringsuppgifter.

## <a name="userscope"></a>Ange och Återställ användarnivå autentiseringsuppgifter

Du kan konfigurera dina autentiseringsuppgifter på användarnivå i alla appar [resurssida](../azure-resource-manager/resource-group-portal.md#manage-resources). Oavsett vilken app du konfigurera dessa autentiseringsuppgifter, gäller det för alla appar och för alla prenumerationer i ditt Azure-konto. 

Så här konfigurerar du dina autentiseringsuppgifter för användarnivå:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra menyn klickar du på **Apptjänster** > **&lt;any_app >** > **distribution Center** > **distributionsbehörigheterna**.

    Du måste ha minst en app innan du kan komma åt sidan autentiseringsuppgifter för distribution i portalen. Men med den [Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), du kan konfigurera användarnivå autentiseringsuppgifter utan en befintlig app.

2. Klicka på **användarautentiseringsuppgifter**, ange det användarnamn och lösenord och klicka sedan på **autentiseringsuppgifter för att spara**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

När du har angett dina autentiseringsuppgifter för distribution, kan du hitta den *Git* användarnamn för distribution i din app **översikt**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

och *FTP* användarnamn för distribution i din app **egenskaper**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure visar inte ditt distributionslösenord på användarnivå. Om du glömmer lösenordet kan återställa du dina autentiseringsuppgifter genom att följa stegen i det här avsnittet.
>
>  

## <a name="appscope"></a>Hämta och återställa appnivå autentiseringsuppgifter
Hämta autentiseringsuppgifterna som app-nivå:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra menyn klickar du på **Apptjänster** > **&lt;any_app >** > **distribution Center** > **distributionsbehörigheterna**.

2. Klicka på **App autentiseringsuppgifter**, och klicka på den **kopia** länk för att kopiera användarnamnet eller lösenordet.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

Om du vill återställa appnivå autentiseringsuppgifterna, klickar du på **återställa autentiseringsuppgifter** i samma dialogrutan.

## <a name="next-steps"></a>Nästa steg

Ta reda på hur du använder dessa autentiseringsuppgifter för att distribuera en app från [lokal Git](app-service-deploy-local-git.md) eller med hjälp av [FTP/S](app-service-deploy-ftp.md).
