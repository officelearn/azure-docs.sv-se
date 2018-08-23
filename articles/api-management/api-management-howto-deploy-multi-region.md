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
ms.date: 08/15/2018
ms.author: apimpm
ms.openlocfilehash: 2ec8d53b0d8da3a7d643362abf58d3a5d4b42e74
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42060931"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Hur du distribuerar en Azure API Management-tjänstinstans till flera Azure-regioner

Azure API Management har stöd för distribution i flera regioner, vilket gör det möjligt för API-utgivare att distribuera en enda Azure API management-tjänsten över valfritt antal önskade Azure-regioner. Detta bidrar till att minska begäran fördröjning uppfattas av geografiskt distribuerade API-kunderna och förbättrar även tillgänglighet om en region går offline.

En ny Azure API Management-tjänst från början bara innehåller en [enhet] [ unit] i en enda Azure-region, den primära regionen. Ytterligare regioner kan enkelt läggas till via Azure-portalen. En API Management gateway-servern har distribuerats till varje region och anrop trafik dirigeras till närmaste gatewayen. Om en region går offline dirigeras trafiken automatiskt till nästa närmaste gatewayen.

> [!IMPORTANT]
> Distribution i flera regioner är endast tillgänglig i den **[Premium] [ Premium]** nivå.

> [!NOTE]
> Azure API Management replikerar endast API gateway-komponenten i olika regioner. Komponenten service management finns bara i den primära regionen. Vid ett strömavbrott i den primära regionen är tillämpa konfigurationsändringar på en Azure API Management-tjänstinstans inte möjligt - inklusive inställningar eller principer för uppdateringar.

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

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/
