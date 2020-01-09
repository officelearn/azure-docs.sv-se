---
title: Distribuera Azure API Management Services till flera Azure-regioner
titleSuffix: Azure API Management
description: Lär dig hur du distribuerar en Azure API Management-tjänstinstans till flera Azure-regioner.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442673"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Så här distribuerar du en Azure API Management-tjänstinstans till flera Azure-regioner

Azure API Management stöder distribution i flera regioner, vilket gör det möjligt för API-utgivare att distribuera en enda Azure API Management-tjänst i valfritt antal Azure-regioner som stöds. Funktionen flera regioner hjälper till att minska svars tiden för förfrågningar som uppfattas av geografiskt distribuerade API-konsumenter och förbättrar tjänstens tillgänglighet om en region kopplas från.

En ny Azure API Management-tjänst som ursprungligen bara innehåller en [enhet][unit] i en enda Azure-region, den primära regionen. Ytterligare regioner kan läggas till i den primära eller sekundära regionen. En API Management Gateway-komponent distribueras till varje vald primär och sekundär region. Inkommande API-begäranden dirigeras automatiskt till den närmaste regionen. Om en region försätts i offlineläge dirigeras API-begäranden automatiskt runt den felande regionen till nästa närmaste Gateway.

> [!NOTE]
> Endast Gateway-komponenten i API Management distribueras till alla regioner. Service Management-komponenten och Developer-portalen finns endast i den primära regionen. Därför kommer åtkomst till Developer-portalen och möjligheten att ändra konfigurationen (t. ex. att lägga till API: er, tillämpa principer) att frigöras tills den primära regionen är online igen, om det uppstår avbrott i den primära regionen. Även om den primära regionen är offline kan de sekundära regionerna fortsätta att betjäna API-trafik med den senaste konfigurationen som är tillgänglig för dem.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>Distribuera en API Management tjänst instans till en ny region

> [!NOTE]
> Om du ännu inte har skapat en API Management tjänst instans, se [skapa en API Management tjänst instans][create an api management service instance].

I Azure Portal navigerar du till sidan för **skalning och prissättning** för din API Management-tjänstinstans.

![Fliken skala][api-management-scale-service]

Om du vill distribuera till en ny region klickar du på **+ Lägg till region** i verktygsfältet.

![Lägg till region][api-management-add-region]

Välj plats i list rutan och ange antalet enheter för med skjutreglaget.

![Ange enheter][api-management-select-location-units]

Klicka på **Lägg till** för att placera ditt val i tabellen platser.

Upprepa processen tills du har konfigurerat alla platser och klicka på **Spara** i verktygsfältet för att starta distributions processen.

## <a name="remove-region"> </a>Ta bort en API Management tjänst instans från en plats

I Azure Portal navigerar du till sidan för **skalning och prissättning** för din API Management-tjänstinstans.

![Fliken skala][api-management-scale-service]

För den plats som du vill ta bort öppnar du snabb menyn med knappen **...** längst till höger i tabellen. Välj alternativet **ta bort** .

Bekräfta borttagningen och klicka på **Spara** för att tillämpa ändringarna.

## <a name="route-backend"> </a>Dirigera API-anrop till regionala Server dels tjänster

Azure API Management innehåller bara en URL för Server del tjänsten. Även om det finns Azure API Management-instanser i olika regioner vidarebefordrar API-gatewayen förfrågningar till samma server dels tjänst, som distribueras i endast en region. I det här fallet kommer prestanda ökningen bara att komma från svar som cachelagrats i Azure API Management i en region som är speciell för begäran, men att kontakta Server delen över hela världen kan fortfarande orsaka hög latens.

För att helt kunna utnyttja den geografiska distributionen av systemet bör Server dels tjänster distribueras i samma regioner som Azure API Management-instanser. Sedan kan du med hjälp av principer och `@(context.Deployment.Region)` egenskap dirigera trafiken till lokala instanser av Server delen.

1. Gå till Azure API Management-instansen och klicka på **API: er** på den vänstra menyn.
2. Välj önskat API.
3. Klicka på **kod redigeraren** i list rutan pil i den **inkommande bearbetningen**.

    ![API-kod redigerare](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Använd `set-backend` kombinerat med villkorliga `choose` principer för att skapa en korrekt routningsprincip i `<inbound> </inbound>`-avsnittet i filen.

    Exempelvis skulle XML-filen nedan fungera för USA, västra och Asien, östra regioner:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Du kan också använda backend-tjänster med [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), dirigera API-anrop till Traffic Manager och låta den matcha routningen automatiskt.

## <a name="custom-routing"> </a>Använda anpassad routning för att API Management regionala gateways

API Management dirigerar begär anden till en regional _Gateway_ baserat på [den lägsta svars tiden](../traffic-manager/traffic-manager-routing-methods.md#performance). Även om det inte går att åsidosätta den här inställningen i API Management kan du använda dina egna Traffic Manager med anpassade regler för routning.

1. Skapa din egen [Azure-Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Om du använder en anpassad domän använder du [den med Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) i stället för tjänsten API Management.
1. [Konfigurera API Management regionala slut punkter i Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). De regionala slut punkterna följer URL-mönstret för `https://<service-name>-<region>-01.regional.azure-api.net`, till exempel `https://contoso-westus2-01.regional.azure-api.net`.
1. [Konfigurera API Management regionala status slut punkter i Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). De regionala status slut punkterna följer URL-mönstret för `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, till exempel `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Ange [routningsmetod](../traffic-manager/traffic-manager-routing-methods.md) för Traffic Manager.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
