---
title: Konfigurera autentiseringsuppgifter för distribution
description: Lär dig vilka typer av distributionsautentiseringsuppgifter som finns i Azure App Service och hur du konfigurerar och använder dem.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266083"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurera distributionsautentiseringsuppgifter för Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) stöder två typer av autentiseringsuppgifter för [lokal Git-distribution](deploy-local-git.md) och [FTP/S-distribution](deploy-ftp.md). Dessa autentiseringsuppgifter är inte samma som dina Azure-prenumerationsautentiseringsuppgifter.

* **Autentiseringsuppgifter på användarnivå:** en uppsättning autentiseringsuppgifter för hela Azure-kontot. Den kan användas för att distribuera till App Service för alla appar, i alla prenumerationer, som Azure-kontot har behörighet att komma åt. Det är standarduppsättningen som visas i portalgränssnittet (till exempel **översikten** och **egenskaperna för** appens [resurssida).](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) När en användare beviljas appåtkomst via RBAC (Role-Based Access Control) eller coadmin-behörigheter kan användaren använda sina egna autentiseringsuppgifter på användarnivå tills åtkomsten har återkallats. Dela inte dessa autentiseringsuppgifter med andra Azure-användare.

* **Autentiseringsuppgifter på appnivå:** en uppsättning autentiseringsuppgifter för varje app. Den kan endast användas för att distribuera till den appen. Autentiseringsuppgifterna för varje app genereras automatiskt när appen skapas. De kan inte konfigureras manuellt, men kan återställas när som helst. För att en användare ska få åtkomst till autentiseringsuppgifter på appnivå via (RBAC) måste den användaren vara deltagare eller högre i appen (inklusive inbyggd roll för webbplatsdeltagare). Läsare får inte publicera och kan inte komma åt dessa autentiseringsuppgifter.

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Konfigurera autentiseringsuppgifter på användarnivå

Du kan konfigurera autentiseringsuppgifterna på användarnivå på en apps [resurssida](../azure-resource-manager/management/manage-resources-portal.md#manage-resources). Oavsett i vilken app du konfigurerar dessa autentiseringsuppgifter gäller den för alla appar och för alla prenumerationer i ditt Azure-konto. 

### <a name="in-the-cloud-shell"></a>I molnet Shell

Om du vill konfigurera distributionsanvändaren i [Cloud Shell](https://shell.azure.com)kör du kommandot [az webapp deployment user set.](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) Ersätt \<användarnamn> och \<lösenord> med användarnamn och lösenord för distributionsanvändare. 

- Användarnamnet måste vara unikt i Azure och för lokala Git-pushes får inte innehålla @-symbolen. 
- Lösenordet måste vara minst åtta tecken långt, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON-utdata visar `null`lösenordet som . Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

### <a name="in-the-portal"></a>I portalen

I Azure-portalen måste du ha minst en app innan du kan komma åt distributionsautentiseringssidan. Så här konfigurerar du autentiseringsuppgifterna på användarnivå:

1. Välj App**\<Services-any_app ** **App Services** > > > **FTP** > Dashboard på [Den](https://portal.azure.com)vänstra menyn i **Azure-portalen** > .**Dashboard**

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Eller, om du redan har konfigurerat Git-distribution, välj **App Services** > **&lt;any_app>**  >  **DISTRIBUTIONscenter** > **FTP/Autentiseringsuppgifter**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Välj **Användarreferenser,** konfigurera användarnamn och lösenord och välj sedan **Spara autentiseringsuppgifter**.

När du har angett dina distributionsuppgifter *Git* kan du hitta Git-distributionsanvändarnamnet på appens **översiktssida,**

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Om Git-distributionen är konfigurerad visas ett **användarnamn på Git/distribution.** Annars ett **användarnamn för FTP/distribution**.

> [!NOTE]
> Azure visar inte distributionslösenordet på användarnivå. Om du glömmer lösenordet kan du återställa dina autentiseringsuppgifter genom att följa stegen i det här avsnittet.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Använda autentiseringsuppgifter på användarnivå med FTP/FTPS

Autentisering till en FTP/FTPS-slutpunkt med autentiseringsuppgifter på användarnivå kräver ett användarnamn i följande format:`<app-name>\<user-name>`

Eftersom autentiseringsuppgifter på användarnivå är länkade till användaren och inte en specifik resurs måste användarnamnet vara i det här formatet för att dirigera inloggningsåtgärden till rätt appslutpunkt.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Hämta och återställa autentiseringsuppgifter på appnivå
Så här hämtar du autentiseringsuppgifter på appnivå:

1. Välj App**&lt;Services-any_app ** **App Services** > >**FTP/autentiseringsuppgifter**på  > Den vänstra menyn i >  **Azure-portalen**. [Azure portal](https://portal.azure.com)

2. Välj **Appautentiseringsuppgifter**och välj länken **Kopiera** för att kopiera användarnamnet eller lösenordet.

Om du vill återställa autentiseringsuppgifterna på appnivå väljer du **Återställ autentiseringsuppgifter** i samma dialogruta.

## <a name="next-steps"></a>Nästa steg

Ta reda på hur du använder dessa autentiseringsuppgifter för att distribuera din app från [lokala Git](deploy-local-git.md) eller med [FTP/S](deploy-ftp.md).
