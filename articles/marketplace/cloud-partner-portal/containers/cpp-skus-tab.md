---
title: 'SKU: er för en Azure Container-avbildning | Azure Marketplace'
description: 'Konfigurera SKU: er för en Azure-behållare.'
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5b69eea8ad7fd4c62925b50434b653118890e280
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823152"
---
# <a name="container-skus-tab"></a>Fliken container-SKU: er

På fliken **SKU: er** på sidan **ny erbjudande** kan du skapa en eller flera SKU: er och koppla dem till ditt nya erbjudande.  Du kan använda olika SKU: er för att särskilja en lösning med hjälp av funktions uppsättningar, fakturerings modeller eller andra egenskaper.

## <a name="sku-settings"></a>SKU-inställningar

När du börjar skapa ett nytt erbjudande finns det inga SKU: er kopplade till erbjudandet. Följ dessa steg om du vill skapa en ny SKU:

1. På fliken SKU: er väljer du **ny SKU**

   ![Ny SKU-prompt](./media/containers-sku-settings.png)

2. Ange nödvändig SKU och behållar information. Varje SKU motsvarar en behållar avbildning. Det finns två delar i en SKU:

    -   SKU-metadata
    -   Metadata för behållare


### <a name="sku-metadata"></a>SKU-metadata

SKU-metadata innehåller butik visar information om behållar listan.

![SKU-metadata](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadata för behållare

Behållar-metadata innehåller referensinformation om avbildnings lagrings informationen i Azure Container Registry (ACR). Azure Marketplace kopierar avbildningen till ett Marketplace-särskilt offentligt register och gör avbildningen tillgänglig för kunder efter certifiering. Alla förfrågningar från Azure-användaren att använda en behållar avbildning för Azure Marketplace hanteras från Marketplace: s offentliga register, inte ACR.

![Metadata för behållare](./media/containers-image-repository.png)
    
**Avbildnings lagrings informationen** i föregående skärm bild innehåller följande fält.  Obligatoriska fält förutsägs av en asterisk (*).

-   **Prenumerations-id\*** -det ID för Azure-prenumeration där ACR finns.
-   **Resurs grupp namn\*** – resurs gruppens namn för ACR.
-   **Register namn\*** -ACR namn.
-   **Namn på databas\*** – lagrings platsens namn. Det här värdet kan inte ändras när det här namnet har angetts. Använd ett unikt namn för att undvika konflikter med andra erbjudanden i ditt konto.
-   **Användar namn\*** -det användar namn (admin-användarnamn) som är associerat med ACR-avbildningen.
-   **Lösen ord\*** – lösen ordet som är kopplat till ACR-avbildningen.

    >[!NOTE]
    >Användar namn och lösen ord krävs för att säkerställa att partners har åtkomst till de ACR som anges i publicerings processen.


### <a name="image-version"></a>Bildversion

När du publicerar en behållar avbildning kan du ange en eller flera avbildnings Taggar och SHA-sammandrag.

**Bildtagg\* eller sammandrag**
 
- Den här taggen eller sammanfattningen måste innehålla en `latest`-tagg och en versions tagg (till exempel från och med `xx.xx.xx-` där xx är ett tal). De bör vara [manifest-Taggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla Taggar som en manifest tag refererar till måste också läggas till så att vi kan ladda upp dem. 
- Du kan lägga till flera versioner av behållare med hjälp av taggar. Alla manifest Taggar (utom `latest`) måste börja med antingen `X.Y-` eller `X.Y.Z-` där X, Y, Z är heltal. <br/> Om en `latest` tag till exempel pekar på `1.0.1-linux-x64`, `1.0.1-linux-arm32`och `1.0.1-windows-arm32`, måste dessa taggar läggas till här.

>[!NOTE]
>Kom ihåg att lägga till en **test-tagg** till din avbildning så att du kan identifiera avbildningen under testningen.


## <a name="next-steps"></a>Nästa steg

Använd [fliken Marketplace](./cpp-marketplace-tab.md) för att skapa en Marketplace-beskrivning för ditt erbjudande. 
