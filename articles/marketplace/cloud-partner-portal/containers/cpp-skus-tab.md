---
title: SKU:er för en Avbildning av Azure Containers | Azure Marketplace
description: Konfigurera SKU:er för en Azure-behållare.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: adbe3d4c498c8f4e4968ca903f78c34aedca9a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280005"
---
# <a name="container-skus-tab"></a>Fliken Behållarskus

På fliken **SKU:er** på sidan **Nytt erbjudande** kan du skapa en eller flera SKU:er och koppla dem till ditt nya erbjudande.  Du kan använda olika SKU:er för att skilja en lösning efter funktionsuppsättningar, faktureringsmodeller eller andra egenskaper.

## <a name="sku-settings"></a>SKU-inställningar

När du börjar skapa ett nytt erbjudande finns det inga SKU:er som är associerade med erbjudandet. Så här skapar du en ny SKU:

1. Välj **Ny SKU** på fliken SKU

   ![Ny SKU-prompt](./media/containers-sku-settings.png)

2. Ange nödvändig SKU- och behållarinformation. Varje SKU motsvarar en behållaravbildning. Det finns två delar till en SKU:

    -   SKU-metadata
    -   Metadata för container


### <a name="sku-metadata"></a>SKU-metadata

SKU-metadata innehåller visningsinformation för butiksfönster för behållarlistan.

![SKU-metadata](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadata för container

Behållarmetadata har referensinformation för information om avbildningsdatabasen i Azure Container Registry (ACR). Azure Marketplace kopierar den här avbildningen till ett Marketplace-specifikt, offentligt register och gör sedan avbildningen tillgänglig för kunder efter certifieringen. Alla begäranden från Azure-användaren om att använda en Azure Marketplace-behållaravbildning visas från Marketplaces offentliga register, inte ACR.

![Metadata för container](./media/containers-image-repository.png)
    
**Information om bilddatabasen** i föregående skärmfångst innehåller följande fält.  Obligatoriska fält åtalas av en asterisk (*).

-   **Prenumerations-ID\* ** - Azure-prenumerations-ID där ACR finns.
-   **Resursgruppsnamn\* ** - Resursgruppnamnet för ACR.
-   **Registernamn\* ** - ACR-namnet.
-   **Databasnamn\* ** - Databasnamnet. När det här namnet har angetts kan det här värdet inte ändras. Använd ett unikt namn för att undvika en konflikt med andra erbjudanden i ditt konto.
-   **Användarnamn\* ** - Användarnamnet (admin användarnamn) i samband med ACR-avbildningen.
-   **Lösenord\* ** - Lösenordet som är associerat med ACR-avbildningen.

    >[!NOTE]
    >Användarnamnet och lösenordet krävs för att säkerställa att partner har tillgång till den ACR som nämns i publiceringsprocessen.


### <a name="image-version"></a>Bildversion

När du publicerar en behållaravbildning kan du ange en eller flera bildtaggar och SHA-sammanfattningar.

**Bildtagg\* eller sammanfattning**
 
- Den här taggen `latest` eller sammanfattningen måste innehålla en `xx.xx.xx-` tagg och en versionstagg (till exempel från där xx är ett tal). De bör vara [manifesttaggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla taggar som refereras av en manifesttagg måste också läggas till så att vi kan ladda upp dem. 
- Du kan lägga till flera versioner av behållaren med hjälp av taggar. Alla manifesttaggar `latest`(utom) `X.Y-` måste `X.Y.Z-` börja med antingen eller där X, Y, Z är heltal. <br/> Om till exempel `latest` en `1.0.1-linux-x64`tagg `1.0.1-linux-arm32`pekar `1.0.1-windows-arm32`på , och dessa taggar måste läggas till här.

>[!NOTE]
>Kom ihåg att lägga till en **testtagg** i bilden så att du kan identifiera bilden under testningen.


## <a name="next-steps"></a>Nästa steg

Använd [fliken Marketplace](./cpp-marketplace-tab.md) för att skapa en marknadsplatsbeskrivning för ditt erbjudande. 
