---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963325"
---
### <a name="launch-the-publish-wizard"></a>Starta publiceringsguiden

I **Solution Explorer** högerklickar du på projektet **SharingService** och väljer **Publicera**.

Publiceringsguiden startas automatiskt. Välj **App Service** > **Publicera** och öppna dialogrutan **Skapa App Service**.

### <a name="sign-in-to-azure"></a>Logga in på Azure

I dialogrutan **Skapa App Service** klickar du på **Lägg till ett konto** och loggar in till din Azure-prenumeration. Välj det konto du vill ha i listrutan om du redan är inloggad.

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

I dialogrutan **Konfigurera värdplan** använder du inställningarna i tabellen.

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|App Service-plan| MySharingServicePlan | Namnet på App Service-planen. |
| Plats | Västra USA | Datacenter som är värd för webbappen. |
| Storlek | Kostnadsfri | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avgör tillgängliga värdfunktioner. |

Välj **OK**.

### <a name="create-and-publish-the-web-app"></a>Skapa och publicera webbappen

I **Appnamn** skriver du ett unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) eller accepterar det automatiskt genererade unika namnet. Webbadressen till webbappen är `https://<app_name>.azurewebsites.net`, där `<app_name>` är appens namn.

Välj **Skapa** för att börja skapa Azure-resurser.

När guiden slutförs publiceras ASP.NET Core-webbappen till Azure och sedan öppnas appen i standardwebbläsaren.

![Publicerad ASP.NET-webbapp i Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Appnamnet som anges i steget [skapa och publicera](#create-and-publish-the-web-app) används som URL-prefixet i formatet `https://<app_name>.azurewebsites.net`. Anteckna den här URL:en eftersom den används senare.
