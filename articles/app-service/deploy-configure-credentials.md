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
ms.date: 03/10/2019
ms.author: cephalin;byvinyal
ms.custom: seodec18
ms.openlocfilehash: df874ab77c88f05b048b1f9d10873943b7bebf36
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884395"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurera autentiseringsuppgifter för distribution för Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) stöder två typer av autentiseringsuppgifter för [lokal Git-distribution](deploy-local-git.md) och [FTP/S distribution](deploy-ftp.md). De här autentiseringsuppgifterna är inte samma som din Azure Active Directory-autentiseringsuppgifter.

* **Användarnivå autentiseringsuppgifter**: en uppsättning autentiseringsuppgifter för hela Azure-konto. Den kan användas för att distribuera till App Service för en app i alla prenumerationer som Azure-kontot har behörighet att komma åt. Det är en standarduppsättning som visas i portalen GUI (som den **översikt** och **egenskaper** för appens [resurssida](../azure-resource-manager/manage-resources-portal.md#manage-resources)). När en användare beviljas åtkomst till appen via rollbaserad åtkomstkontroll (RBAC) eller coadmin behörigheter kan användaren använda sina egna användarnivå autentiseringsuppgifter förrän åtkomst har återkallats. Dela inte dessa autentiseringsuppgifter med andra Azure-användare.

* **Appnivå autentiseringsuppgifter**: en uppsättning autentiseringsuppgifter för varje app. Den kan användas för att distribuera till appen endast. Autentiseringsuppgifterna för varje app som genereras automatiskt när en app skapas. De kan inte konfigureras manuellt, men kan återställas när som helst. För en användare beviljas åtkomst på appnivå autentiseringsuppgifter via (RBAC), som är deltagare eller senare på appen. Läsare tillåts inte att publicera, och det går inte att få åtkomst till dessa autentiseringsuppgifter.

## <a name="userscope"></a>Ange och Återställ användarnivå autentiseringsuppgifter

Du kan konfigurera dina autentiseringsuppgifter på användarnivå i alla appar [resurssida](../azure-resource-manager/manage-resources-portal.md#manage-resources). Oavsett vilken app du konfigurera dessa autentiseringsuppgifter, gäller det för alla appar och för alla prenumerationer i ditt Azure-konto. 

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

## <a name="use-user-level-credentials-with-ftpftps"></a>Använd användarnivå autentiseringsuppgifter med FTP/FTPS

Autentisering till en FTP/FTPS-slutpunkt som använder användarnivå autentiseringsuppgifter requirers ett användarnamn i formatet: `<app-name>\<user-name>`

Eftersom användarnivå autentiseringsuppgifter är länkade till användaren och inte en specifik resurs, måste användarnamnet vara i formatet att dirigera åtgärden logga in till rätt app-slutpunkten.

## <a name="appscope"></a>Hämta och återställa appnivå autentiseringsuppgifter
Hämta autentiseringsuppgifterna som app-nivå:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra menyn klickar du på **Apptjänster** > **&lt;any_app >** > **distribution Center** > **distributionsbehörigheterna**.

2. Klicka på **App autentiseringsuppgifter**, och klicka på den **kopia** länk för att kopiera användarnamnet eller lösenordet.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

Om du vill återställa appnivå autentiseringsuppgifterna, klickar du på **återställa autentiseringsuppgifter** i samma dialogrutan.

## <a name="next-steps"></a>Nästa steg

Ta reda på hur du använder dessa autentiseringsuppgifter för att distribuera en app från [lokal Git](deploy-local-git.md) eller med hjälp av [FTP/S](deploy-ftp.md).
