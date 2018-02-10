---
title: "Distribuera Azure API Management-tjänster i Azure-regioner | Microsoft Docs"
description: "Lär dig hur du distribuerar en Azure API Management service-instans till Azure-regioner."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 6f84c1b2092f71eaeba0005a36018390242869ea
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Distribuera ett Azure API Management service-instans till Azure-regioner
API-hantering stöder distribution av flera regioner där API utgivare att distribuera en enda API-tjänsten till alla önskade Azure-regioner. Detta minskar begäran latens uppfattas av geografiskt distribuerat API-konsumenter och förbättrar även tjänsttillgängligheten om en region tas offline. 

När en API Management-tjänst skapas från början, innehåller endast en [enhet] [ unit] och finns i en enda Azure-region, som anges som den primära regionen. Ytterligare regioner kan enkelt lägga via Azure Portal. En API Management gateway-servern har distribuerats till varje region och anrop trafik vidarebefordras till närmaste gateway. Om en region försätts offline, är trafiken automatiskt igen dirigerad till nästa närmaste gateway. 

> [!IMPORTANT]
> Distribution av flera regioner är endast tillgängligt i den  **[Premium] [ Premium]**  nivå.
> 
> 

## <a name="add-region"></a>Distribuera en API Management service-instans till en ny region
> [!NOTE]
> Om du inte har skapat en instans för API Management-tjänsten finns [skapa en instans för API Management-tjänsten][Create an API Management service instance].
> 
> 

I Azure Portal går du till den **skala och prissättning** för din API Management service-instans. 

![Skala][api-management-scale-service]

Om du vill distribuera till en ny region, klickar du på **+ Lägg till region** i verktygsfältet.

![Lägg till region][api-management-add-region]

Välja platsen i den nedrullningsbara listrutan och ange antalet enheter för med skjutreglaget.

![Ange enheter][api-management-select-location-units]

Klicka på **Lägg till** att placera ditt val i tabellen platser. 

Upprepa den här processen tills du har alla platser som har konfigurerats och klicka på **spara** från verktygsfältet för att starta distributionsprocessen.

## <a name="remove-region"></a>Ta bort en instans för API Management-tjänsten från en plats

I Azure Portal går du till den **skala och prissättning** för din API Management service-instans. 

![Skala][api-management-scale-service]

Öppna menyn kontext med för den plats som du vill ta bort den **...**  längst till höger i tabellen. Välj den **ta bort** alternativet.

Bekräfta borttagningen och klicka på **spara** att tillämpa ändringarna.

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

