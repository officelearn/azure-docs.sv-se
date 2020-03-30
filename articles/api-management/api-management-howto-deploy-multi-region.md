---
title: Distribuera Azure API Management-tjänster till flera Azure-regioner
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442673"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Så distribuerar du en Azure API Management-tjänstinstans till flera Azure-regioner

Azure API Management stöder distribution i flera regioner, vilket gör det möjligt för API-utgivare att distribuera en enda Azure API-hanteringstjänst över valfritt antal Azure-regioner som stöds. Funktionen Flera regioner bidrar till att minska svarstiden för begäran som uppfattas av geografiskt distribuerade API-konsumenter och förbättrar tjänstens tillgänglighet om en region kopplas från.

En ny Azure API Management-tjänst innehåller ursprungligen bara en [enhet][unit] i en enda Azure-region, den primära regionen. Ytterligare regioner kan läggas till i primär- eller sekundärregionerna. En API Management gateway-komponent distribueras till alla valda primära och sekundära regioner. Inkommande API-begäranden dirigeras automatiskt till närmaste region. Om en region kopplas från dirigeras API-begäranden automatiskt runt den misslyckade regionen till nästa närmaste gateway.

> [!NOTE]
> Endast gateway-komponenten i API Management distribueras till alla regioner. Tjänsthanteringskomponenten och utvecklarportalen finns endast i regionen Primär. När det gäller avbrott i den primära regionen försämras därför åtkomsten till utvecklarportalen och möjligheten att ändra konfiguration (t.ex. lägga till API:er, tillämpa principer) tills den primära regionen är online igen. Medan den primära regionen är offline tillgänglig Sekundära regioner kommer att fortsätta att betjäna API-trafik med den senaste konfigurationen tillgängliga för dem.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>Distribuera en API Management-tjänstinstans till en ny region

> [!NOTE]
> Om du ännu inte har skapat en API Management-tjänstinstans läser du [Skapa en API Management-tjänstinstans][create an api management service instance].

I Azure-portalen navigerar du till sidan **Skala och priser** för din API Management-tjänstinstans.

![Fliken Skala][api-management-scale-service]

Om du vill distribuera till en ny region klickar du på **+ Lägg till region** från verktygsfältet.

![Lägg till region][api-management-add-region]

Välj plats i listrutan och ange antalet enheter för med skjutreglaget.

![Ange enheter][api-management-select-location-units]

Klicka på **Lägg till** om du vill placera ditt val i tabellen Platser.

Upprepa den här processen tills du har konfigurerat alla platser och klicka på **Spara** från verktygsfältet för att starta distributionsprocessen.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>Ta bort en API Management-tjänstinstans från en plats

I Azure-portalen navigerar du till sidan **Skala och priser** för din API Management-tjänstinstans.

![Fliken Skala][api-management-scale-service]

För den plats du vill ta bort öppnar du snabbmenyn med hjälp av **...-knappen** i den högra änden av tabellen. Välj alternativet **Ta bort.**

Bekräfta borttagningen och klicka på **Spara** om du vill tillämpa ändringarna.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Dirigera API-anrop till regionala serverdtjänster

Azure API Management har bara en url för serverdelar. Även om det finns Azure API Management-instanser i olika regioner vidarebefordrar API-gatewayen fortfarande begäranden till samma servergruppstjänst, som distribueras i endast en region. I det här fallet kommer prestandaökningen endast från svar som cachelagras inom Azure API Management i en region som är specifik för begäran, men att kontakta servergruppen över hela världen kan fortfarande orsaka hög svarstid.

Om du vill utnyttja den geografiska distributionen av ditt system fullt ut bör du ha serverdtjänster som distribueras i samma regioner som Azure API Management-instanser. Med hjälp av `@(context.Deployment.Region)` principer och egendom kan du sedan dirigera trafiken till lokala instanser av serverdelen.

1. Navigera till din Azure API Management-instans och klicka på **API:er** från den vänstra menyn.
2. Välj önskat API.
3. Klicka på **Kodredigeraren** från pilrutan i den **inkommande bearbetningen**.

    ![API-kodredigerare](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Använd `set-backend` den kombinerade `choose` med villkorliga principer för `<inbound> </inbound>` att skapa en korrekt routningsprincip i avsnittet i filen.

    Den xml-fil nedan skulle till exempel fungera för regioner i västra USA och Östasien:

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
> Du kan också fronta dina serverdelstjänster med [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), dirigera API-anropen till Traffic Manager och låta den lösa routningen automatiskt.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Använda anpassad routning till regionala gateways för API Management

API Management dirigerar begäranden till en regional _gateway_ baserat på [den lägsta svarstiden](../traffic-manager/traffic-manager-routing-methods.md#performance). Även om det inte går att åsidosätta den här inställningen i API Management kan du använda din egen Trafikhanterare med anpassade routningsregler.

1. Skapa din egen [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Om du använder en anpassad domän [använder du den med Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) i stället för API Management-tjänsten.
1. [Konfigurera de regionala slutpunkterna för API Management i Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). De regionala slutpunkterna följer `https://<service-name>-<region>-01.regional.azure-api.net`URL-mönstret för till exempel `https://contoso-westus2-01.regional.azure-api.net`.
1. [Konfigurera de regionala statusslutpunkterna för API Management i Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Slutpunkterna för regional status följer `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`URL-mönstret för till exempel `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Ange [routningsmetoden](../traffic-manager/traffic-manager-routing-methods.md) för Trafikhanteraren.

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
