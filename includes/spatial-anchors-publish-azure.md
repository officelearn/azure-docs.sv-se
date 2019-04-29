---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681378"
---
### <a name="open-the-publish-wizard"></a>Öppna Publiceringsguiden

I **Solution Explorer**, högerklicka på den **SharingService** projektet och välj **publicera**.

Guiden Publicera startar. Välj **Apptjänst** > **publicera** att öppna den **skapa App Service** dialogrutan.

### <a name="sign-in-to-azure"></a>Logga in på Azure

I den **skapa App Service** dialogrutan **Lägg till ett konto** och logga in på Azure-prenumerationen. Om du har redan loggat in, väljer du det konto du vill från den nedrullningsbara listan.

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

I den **konfigurera som är värd för Plan** dialogrutan använder de här inställningarna:

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|App Service-plan| MySharingServicePlan | Namnet på App Service-planen. |
| Location | Västra USA | Datacenter som är värd för webbappen. |
| Storlek | Kostnadsfri | Den [prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) som avgör tillgängliga värdfunktioner. |

Välj **OK**.

### <a name="create-and-publish-the-web-app"></a>Skapa och publicera webbappen

I **Appnamn**, ange ett unikt appnamn (giltiga tecken är `a-z`, `0-9`, och `-`), eller acceptera det automatiskt genererade unika namnet. Webbadressen till webbappen är `https://<app_name>.azurewebsites.net`, där `<app_name>` är appens namn.

Välj **Skapa** för att börja skapa Azure-resurser.

När guiden slutförs publiceras ASP.NET Core-webbapp till Azure och sedan öppnas appen i standardwebbläsaren.

![Publicerad ASP.NET-webbapp i Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Appnamnet som du använde i det här avsnittet används som URL-prefixet i formatet `https://<app_name>.azurewebsites.net`. Anteckna denna URL eftersom du behöver den.
