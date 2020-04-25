---
title: 'SKU: er för en Azure Container-avbildning | Azure Marketplace'
description: 'Konfigurera SKU: er för en Azure-behållare.'
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 5f37d157e11b8cd7cf093fa558e81d9a1ce345a9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146189"
---
# <a name="container-skus-tab"></a>Fliken container-SKU: er

> [!IMPORTANT]
> Från den 13 april 2020 börjar vi flytta hanteringen av dina Azure Container-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett Azure Container-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) för att hantera dina migrerade erbjudanden.

På fliken **SKU: er** på sidan **ny erbjudande** kan du skapa en eller flera SKU: er och koppla dem till ditt nya erbjudande.  Du kan använda olika SKU: er för att särskilja en lösning med hjälp av funktions uppsättningar, fakturerings modeller eller andra egenskaper.

## <a name="sku-settings"></a>SKU-inställningar

När du börjar skapa ett nytt erbjudande finns det inga SKU: er kopplade till erbjudandet. Följ dessa steg om du vill skapa en ny SKU:

1. På fliken SKU: er väljer du **ny SKU**

   ![Ny SKU-prompt](./media/containers-sku-settings.png)

2. Ange nödvändig SKU och behållar information. Varje SKU motsvarar en behållar avbildning. Det finns två delar i en SKU:

    -   SKU-metadata
    -   Metadata för container


### <a name="sku-metadata"></a>SKU-metadata

SKU-metadata innehåller butik visar information om behållar listan.

![SKU-metadata](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadata för container

Behållar-metadata innehåller referensinformation om avbildnings lagrings informationen i Azure Container Registry (ACR). Azure Marketplace kopierar avbildningen till ett Marketplace-särskilt offentligt register och gör avbildningen tillgänglig för kunder efter certifiering. Alla förfrågningar från Azure-användaren att använda en behållar avbildning för Azure Marketplace hanteras från Marketplace: s offentliga register, inte ACR.

![Metadata för container](./media/containers-image-repository.png)
    
**Avbildnings lagrings informationen** i föregående skärm bild innehåller följande fält.  Obligatoriska fält förutsägs av en asterisk (*).

-   **Prenumerations\* -ID** – ID för Azure-prenumerationen där ACR finns.
-   **Resurs grupps\* namn** – resurs gruppens namn för ACR.
-   **Register namn\* ** – ACR namn.
-   **Databas namn\* ** – databasens namn. Det här värdet kan inte ändras när det här namnet har angetts. Använd ett unikt namn för att undvika konflikter med andra erbjudanden i ditt konto.
-   **Användar\* namn** – användar namnet (administratörens användar namn) som är associerat med ACR-avbildningen.
-   **Password\* ** – lösen ordet som är kopplat till ACR-avbildningen.

    >[!NOTE]
    >Användar namn och lösen ord krävs för att säkerställa att partners har åtkomst till de ACR som anges i publicerings processen.


### <a name="image-version"></a>Bildversion

När du publicerar en behållar avbildning kan du ange en eller flera avbildnings Taggar och SHA-sammandrag.

**Bildtagg\* eller sammanfattad**
 
- Den här taggen eller sammanfattningen måste `latest` innehålla en tagg och en versions tagg (t. `xx.xx.xx-` ex. från och med där xx är ett tal). De bör vara [manifest-Taggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla Taggar som en manifest tag refererar till måste också läggas till så att vi kan ladda upp dem. 
- Du kan lägga till flera versioner av behållare med hjälp av taggar. Alla manifest Taggar (utom `latest`) måste börja med antingen `X.Y-` eller `X.Y.Z-` där X, Y, Z är heltal. <br/> Om en `latest` tagg till exempel pekar på `1.0.1-linux-x64`, `1.0.1-linux-arm32`, och `1.0.1-windows-arm32`måste dessa taggar läggas till här.

>[!NOTE]
>Kom ihåg att lägga till en **test-tagg** till din avbildning så att du kan identifiera avbildningen under testningen.


## <a name="next-steps"></a>Nästa steg

Använd [fliken Marketplace](./cpp-marketplace-tab.md) för att skapa en Marketplace-beskrivning för ditt erbjudande. 
