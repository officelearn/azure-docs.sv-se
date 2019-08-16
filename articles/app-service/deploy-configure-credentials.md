---
title: Konfigurera autentiseringsuppgifter för distribution – Azure App Service | Microsoft Docs
description: Lär dig hur du använder autentiseringsuppgifterna för Azure App Service distribution.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/14/2019
ms.author: cephalin
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 52bbc907d91dd4bf9066daf14d0d5de1b759b92f
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558546"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurera autentiseringsuppgifter för distribution för Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) stöder två typer av autentiseringsuppgifter för [lokal Git-distribution](deploy-local-git.md) och [FTP/S-distribution](deploy-ftp.md). Autentiseringsuppgifterna är inte desamma som dina autentiseringsuppgifter för Azure-prenumerationen.

* **Autentiseringsuppgifter för användar nivå**: en uppsättning autentiseringsuppgifter för hela Azure-kontot. Den kan användas för att distribuera till App Service för alla appar, i alla prenumerationer, som Azure-kontot har behörighet att komma åt. Det är den standard uppsättning som finns i portalens GUI (till exempel **Översikt** och **Egenskaper** för appens [resurs sida](../azure-resource-manager/manage-resources-portal.md#manage-resources)). När en användare beviljas åtkomst till appen via rollbaserad Access Control (RBAC) eller behörigheten administratör kan användaren använda sina egna autentiseringsuppgifter på användar nivå tills åtkomsten har återkallats. Dela inte dessa autentiseringsuppgifter med andra Azure-användare.

* **Autentiseringsuppgifter för app-nivå**: en uppsättning autentiseringsuppgifter för varje app. Den kan endast användas för att distribuera till appen. Autentiseringsuppgifterna för varje app genereras automatiskt när appen skapas. De kan inte konfigureras manuellt, men de kan återställas när som helst. För att en användare ska beviljas åtkomst till autentiseringsuppgifter för app-nivå via (RBAC) måste användaren vara deltagare eller högre i appen. Läsarna får inte publicera och har inte åtkomst till dessa autentiseringsuppgifter.

## <a name="userscope"></a>Konfigurera autentiseringsuppgifter på användar nivå

Du kan konfigurera dina autentiseringsuppgifter på användar nivå på alla appars [resurs sidor](../azure-resource-manager/manage-resources-portal.md#manage-resources). Oavsett vilken app du konfigurerar autentiseringsuppgifterna för, gäller den för alla appar och för alla prenumerationer i ditt Azure-konto. 

### <a name="in-the-cloud-shell"></a>I Cloud Shell

Om du vill konfigurera distributions användaren i [Cloud Shell](https://shell.azure.com)kör du kommandot [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) . Ersätt \<username > och \<Password > med ett användar namn och lösen ord för distributions användare. 

- Användar namnet måste vara unikt inom Azure, och för lokala git-push-meddelanden får inte innehålla symbolen @. 
- Lösen ordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON-utdata visar lösen ordet som `null`. Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

### <a name="in-the-portal"></a>I portalen

I Azure Portal måste du ha minst en app innan du kan komma åt sidan autentiseringsuppgifter för distribution. Så här konfigurerar du dina autentiseringsuppgifter på användar nivå:

1. I [Azure Portal](https://portal.azure.com)väljer du **app Services** >  **\<any_app >** distributions > Center**FTP**-instrumentpanel > på menyn till vänster. > 

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Om du redan har konfigurerat Git-distribution väljer du **app Services** >  **&lt;any_app >**  > **FTP/autentiseringsuppgifter**för**distributions Center** > .

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Väljanvändarautentiseringsuppgifter, konfigurera användar namn och lösen ord och välj sedan **Spara autentiseringsuppgifter**.

När du har angett dina autentiseringsuppgifter för distribution kan du hitta användar namnet för *git* -distributionen på appens översikts sida.

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Om Git-distribution har kon figurer ATS visar sidan ett **användar namn för Git/distribution**; annars är ett **FTP-/distributions användar namn**.

> [!NOTE]
> Azure visar inte lösen ordet för distribution på användar nivå. Om du glömmer bort lösen ordet kan du återställa dina autentiseringsuppgifter genom att följa stegen i det här avsnittet.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Använd autentiseringsuppgifter på användar nivå med FTP/FTPS

Autentisering till en FTP/FTPS-slutpunkt med autentiseringsuppgifter på användar nivå kräver ett användar namn i följande format:`<app-name>\<user-name>`

Eftersom autentiseringsuppgifter på användar nivå är länkade till användaren och inte en speciell resurs, måste användar namnet vara i det här formatet för att dirigera inloggnings åtgärden till rätt app-slutpunkt.

## <a name="appscope"></a>Hämta och Återställ autentiseringsuppgifter på program nivå
Hämta autentiseringsuppgifter för program nivå:

1. I [Azure Portal](https://portal.azure.com)väljer du **app Services** >  **&lt;any_app >**  > **distributions Center** > **FTP/autentiseringsuppgifter**på menyn till vänster.

2. Välj **autentiseringsuppgifter för appen**och kopiera sedan användar namnet eller lösen ordet genom att välja **Kopiera** länk.

Om du vill återställa autentiseringsuppgifter för program nivå väljer du **Återställ autentiseringsuppgifter** i samma dialog ruta.

## <a name="next-steps"></a>Nästa steg

Ta reda på hur du använder dessa autentiseringsuppgifter för att distribuera din app från [lokal git](deploy-local-git.md) eller via [FTP/S](deploy-ftp.md).
