---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0e6f68daf22659b880827ad027268d9757c60063
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971433"
---
### <a name="open-the-publish-wizard"></a>Öppna guiden publicera

I **Solution Explorer**högerklickar du på projektet **SharingService** och väljer sedan **publicera**.

Publicerings guiden startar. 

Välj **App Service**  >  **publicera** för att öppna fönstret **Skapa App Service** .

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal.

I fönstret **skapa App Service** väljer du **Lägg till ett konto**och loggar sedan in på din Azure-prenumeration. Om du redan är inloggad väljer du önskat konto i list rutan.

   > [!NOTE]
   > Välj inte **Skapa** ännu om du redan är inloggad.
   >

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [resource group intro text](resource-group.md)]

Välj **Ny** bredvid **Resursgrupp**.

Namnge resurs gruppen **myResourceGroup**och välj sedan **OK**.

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [app-service-plan](app-service-plan.md)]

Bredvid **Värdplan** väljer du **Ny**.

Använd de här inställningarna i fönstret **Konfigurera värd plan** :

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|App Service-plan| MySharingServicePlan | Namnet på App Service plan |
| Plats | USA, västra | Data centret där webbappen finns |
| Storlek | Kostnadsfri | Den [pris nivå](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) som avgör värd funktioner |

Välj **OK**.

### <a name="create-and-publish-the-web-app"></a>Skapa och publicera webbappen

Ange ett unikt namn för appen i **appens namn**. Giltiga tecken är a-z, 0-9 och bindestreck (-), eller accepterar det automatiskt genererade unika namnet. Webbadressen till webbappen är `https://<app_name>.azurewebsites.net`, där `<app_name>` är appens namn.

Välj **Skapa** för att börja skapa Azure-resurser.

   När guiden har slutförts publiceras ASP.NET Core-webbappen till Azure och sedan öppnas appen i standard webbläsaren.

  ![Skärm bild av en publicerad ASP.NET-webbapp i Azure.](./media/spatial-anchors-azure/web-app-running-live.png)

Det app-namn som du använde i det här avsnittet används som URL-prefix i formatet `https://<app_name>.azurewebsites.net` . Kopiera denna URL till en text redigerare för senare användning.
