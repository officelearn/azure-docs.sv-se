---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 80685dee7907b81832c94044d1feb8fcf2e41bde
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95998503"
---
### <a name="open-the-publish-wizard"></a>Öppna guiden publicera

I **Solution Explorer** högerklickar du på projektet **SharingService** och väljer sedan **publicera**.

Publicerings guiden startar. 

Välj **App Service**  >  **publicera** för att öppna fönstret **Skapa App Service** .

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen.

I fönstret **skapa App Service** väljer du **Lägg till ett konto** och loggar sedan in på din Azure-prenumeration. Om du redan är inloggad väljer du önskat konto i list rutan.

   > [!NOTE]
   > Välj inte **Skapa** ännu om du redan är inloggad.
   >

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [resource group intro text](resource-group.md)]

Välj **Ny** bredvid **Resursgrupp**.

Namnge resurs gruppen **myResourceGroup** och välj sedan **OK**.

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
