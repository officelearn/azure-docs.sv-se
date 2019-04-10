---
title: Distribuera Azure API Management-tjänster till flera Azure-regioner | Microsoft Docs
description: Lär dig hur du distribuerar en Azure API Management-tjänstinstans till flera Azure-regioner.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278657"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Hur du distribuerar en Azure API Management-tjänstinstans till flera Azure-regioner

Azure API Management har stöd för distribution i flera regioner, vilket gör det möjligt för API-utgivare att distribuera en enda Azure API management-tjänsten över valfritt antal önskade Azure-regioner. Detta bidrar till att minska begäran fördröjning uppfattas av geografiskt distribuerade API-kunderna och förbättrar även tillgänglighet om en region går offline.

En ny Azure API Management-tjänst från början bara innehåller en [enhet] [ unit] i en enda Azure-region, den primära regionen. Ytterligare regioner kan enkelt läggas till via Azure-portalen. En API Management gateway-servern har distribuerats till varje region och anrop trafik dirigeras till den närmaste gatewayen när det gäller svarstider. Om en region går offline dirigeras trafiken automatiskt till nästa närmaste gatewayen.

> [!NOTE]
> Azure API Management replikerar endast API gateway-komponenten i olika regioner. Komponenten service management finns bara i den primära regionen. Vid ett strömavbrott i den primära regionen är tillämpa konfigurationsändringar på en Azure API Management-tjänstinstans inte möjligt - inklusive inställningar eller principer för uppdateringar.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>Distribuera en API Management-tjänstinstans till en ny region

> [!NOTE]
> Om du inte har skapat en API Management-tjänstinstans, se [skapa en API Management-tjänstinstans][Create an API Management service instance].

I Azure-portalen går du till den **skalning och priser** sidan för din API Management-tjänstinstans. 

![Fliken Skala][api-management-scale-service]

Om du vill distribuera till en ny region, klickar du på **+ Lägg till region** från verktygsfältet.

![Lägg till region][api-management-add-region]

Välja platsen i den nedrullningsbara listrutan och ange hur många enheter för med skjutreglaget.

![Ange enheter][api-management-select-location-units]

Klicka på **Lägg till** att placera ditt val i tabellen platser. 

Upprepa processen tills du har alla platser som har konfigurerats och klicka på **spara** från verktygsfältet för att starta distributionsprocessen.

## <a name="remove-region"> </a>Ta bort en API Management-tjänstinstans från en plats

I Azure-portalen går du till den **skalning och priser** sidan för din API Management-tjänstinstans. 

![Fliken Skala][api-management-scale-service]

Öppna menyn kontext med för den plats som du vill ta bort den **...**  längst till höger i tabellen. Välj den **ta bort** alternativet.

Bekräfta borttagningen och klickar på **spara** att tillämpa ändringarna.

## <a name="route-backend"> </a>Väg API-anrop till regionala backend-tjänster

Azure API Management har endast en backend-tjänst-URL. Även om det finns Azure API Management-instanser i olika regioner, API-gatewayen kommer fortfarande vidarebefordra begäranden till samma backend-tjänsten som har distribuerats i endast en region. I det här fallet prestanda kommer endast från svar cachelagras i Azure API Management i en region som är specifika för begäran, men att kontakta serverdelen i hela världen kan fortfarande orsaka hög latens.

Om du vill utnyttjar fullt ut geografisk fördelning av systemet, bör du ha backend-tjänster som distribueras i samma regioner som Azure API Management-instanser. Sedan med hjälp av principer och `@(context.Deployment.Region)` egenskapen, du kan vidarebefordra trafiken till lokala instanser av din serverdel.

1. Gå till din Azure API Management-instans och klicka på **API: er** menyn till vänster.
2. Välj din önskade API.
3. Klicka på **Kodredigerare** i listrutan pilen i det **inkommande bearbetning**.

    ![API-Kodredigerare](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Använd den `set-backend` i kombination med villkorlig `choose` principer för att konstruera en korrekt routningsprincip i den `<inbound> </inbound>` i filen.

    Till exempel den nedan XML-fil skulle fungera för västra USA och östra Asien:

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
> Du kan också klientdelens backend-tjänster med [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)direkta API-anrop till Traffic Manager och låta det lösa routning automatiskt.

## <a name="custom-routing"> </a>Använd anpassad routning till regionala API Management-gateways

API Management dirigerar begärandena till en regional *gateway* utifrån [kortast svarstid](../traffic-manager/traffic-manager-routing-methods.md#performance). Det inte går att åsidosätta den här inställningen i API Management, men du kan använda din egen Traffic Manager med anpassade regler för routning.

1. Skapa en egen [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Om du använder en anpassad domän, [använder den med Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) i stället för API Management-tjänsten.
1. [Konfigurera API Management regionala slutpunkter i Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Regionala slutpunkterna följer mönstret för URL: en för `https://<service-name>-<region>-01.regional.azure-api.net`, till exempel `https://contoso-westus2-01.regional.azure-api.net`.
1. [Konfigurera API Management regionala status slutpunkter i Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Regionala status slutpunkter följer mönstret för URL: en för `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, till exempel `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Ange [routningmetod](../traffic-manager/traffic-manager-routing-methods.md) av Traffic Manager.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
