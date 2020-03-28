---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67187032"
---
### <a name="open-the-publish-wizard"></a>Öppna publiceringsguiden

Högerklicka på **SharingService-projektet** i **Solution Explorer**och välj **Publicera**.

Publiceringsguiden startar. Välj **App Service** > **Publicera** om du vill öppna dialogrutan **Skapa apptjänst.**

### <a name="sign-in-to-azure"></a>Logga in på Azure

I dialogrutan **Skapa apptjänst** väljer du **Lägg till ett konto** och loggar in på din Azure-prenumeration. Om du redan är inloggad väljer du det konto du vill använda i listrutan.

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

Använd följande inställningar i dialogrutan **Konfigurera värdplan:**

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|App Service-plan| MySharingServicePlan | Namnet på App Service-planen. |
| Location | USA, västra | Datacenter som är värd för webbappen. |
| Storlek | Kostnadsfri | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) som bestämmer värdfunktioner. |

Välj **OK**.

### <a name="create-and-publish-the-web-app"></a>Skapa och publicera webbappen

I **Appnamn**anger du ett unikt `a-z`appnamn (giltiga tecken är , `0-9`, och `-`) eller acceptera det automatiskt genererade unika namnet. Webbadressen till webbappen är `https://<app_name>.azurewebsites.net`, där `<app_name>` är appens namn.

Välj **Skapa** för att börja skapa Azure-resurser.

När guiden är klar publicerar den ASP.NET Core-webbappen till Azure och öppnar sedan appen i standardwebbläsaren.

![Publicerad ASP.NET-webbapp i Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Appnamnet som du använde i det här avsnittet används `https://<app_name>.azurewebsites.net`som URL-prefix i formatet . Ta del av den här webbadressen eftersom du behöver den.
