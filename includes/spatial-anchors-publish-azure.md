---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187032"
---
### <a name="open-the-publish-wizard"></a>Öppna guiden publicera

I **Solution Explorer**högerklickar du på projektet **SharingService** och väljer **publicera**.

Publicerings guiden startar. Välj **App Service**  >  **publicera** för att öppna dialog rutan **Skapa App Service** .

### <a name="sign-in-to-azure"></a>Logga in på Azure

I dialog rutan **skapa App Service** väljer du **Lägg till ett konto** och loggar in på din Azure-prenumeration. Om du redan är inloggad väljer du önskat konto i list rutan.

> [!NOTE]
> Välj inte **Skapa** ännu om du redan är inloggad.
>

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [resource group intro text](resource-group.md)]

Välj **Ny** bredvid **Resursgrupp**.

Ge resursgruppen namnet **myResourceGroup** och välj **OK**.

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [app-service-plan](app-service-plan.md)]

Bredvid **Värdplan** väljer du **Ny**.

Använd de här inställningarna i dialog rutan **Konfigurera värd plan** :

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|App Service-plan| MySharingServicePlan | Namnet på App Service-planen. |
| Location | USA, västra | Datacenter som är värd för webbappen. |
| Storlek | Kostnadsfri | Den [pris nivå](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) som avgör värd funktionerna. |

Välj **OK**.

### <a name="create-and-publish-the-web-app"></a>Skapa och publicera webbappen

Ange ett unikt namn på appen i **appens**namn (giltiga tecken är `a-z` , `0-9` och `-` ) eller godkänn det unika namnet som skapas automatiskt. Webbadressen till webbappen är `https://<app_name>.azurewebsites.net`, där `<app_name>` är appens namn.

Välj **Skapa** för att börja skapa Azure-resurser.

När guiden har slutförts publiceras ASP.NET Core-webbappen till Azure och sedan öppnas appen i standard webbläsaren.

![Publicerad ASP.NET-webbapp i Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Det app-namn som du använde i det här avsnittet används som URL-prefix i formatet `https://<app_name>.azurewebsites.net` . Anteckna den här URL: en eftersom du behöver den.
