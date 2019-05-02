---
title: 'SKU: er för en avbildning i Azure-behållare | Azure Marketplace'
description: 'Konfigurera SKU: er för en Azure-behållare.'
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 6953329bfabe99fc4bb28f2494cb412ba9cbbba0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942918"
---
# <a name="container-skus-tab"></a>Fliken för behållaren SKU: er

Den **SKU: er** fliken den **nytt erbjudande** sidan kan du skapa en eller flera SKU: er och koppla dem till din nya erbjudandet.  Du kan använda olika SKU: er för att skilja mellan en lösning av funktioner, faktureringsmodellerna eller andra egenskaper.

## <a name="sku-settings"></a>SKU-inställningar

När du börjar skapa ett nytt erbjudande, det är inte alla SKU: er som är kopplad till erbjudandet. Följ dessa steg om du vill skapa en ny SKU:

1. Välj fliken SKU: er **nya SKU: N**

   ![Nya SKU-fråga](./media/containers-sku-settings.png)

2. Ange nödvändig information för SKU: N och behållare. Varje SKU motsvarar en behållaravbildning. Det finns två delar till en SKU:

    -   SKU-metadata
    -   Behållare för metadata


### <a name="sku-metadata"></a>SKU-metadata

SKU-metadata innehåller storefront visningsinformation container-lista.

![SKU-metadata](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Behållare för metadata

Behållare-metadata innehåller referensinformation om din lagringsplats avbildningsinformation i Azure Container Registry (ACR). Azure Marketplace kopierar den här avbildningen till ett Marketplace-specifika, offentliga register och sedan gör avbildningen tillgängliga för kunder efter certifiering. Från Marketplace-offentligt register, inte ACR hanteras alla förfrågningar från Azure-användare att använda en behållaravbildning för Azure Marketplace.

![Behållare för metadata](./media/containers-image-repository.png)
    
Den **Avbildningsdetaljerna som databasen** i den föregående skärmbilden avbildning innehåller följande fält.  Required fields are indicted by an asterisk (*).

-   **Prenumerations-ID\***  -prenumerations-ID för Azure där i ACR förekommer.
-   **Resursgruppens namn\***  -resursgruppens namn för ACR.
-   **Registernamn\***  -namnet på ACR.
-   **Namnet på lagringsplatsen\***  -namnet på lagringsplatsen. Det här värdet kan inte ändras när du har angett det här namnet. Använd ett unikt namn för att undvika en konflikt med andra erbjudanden i ditt konto.
-   **Användarnamn\***  -användarnamn (administratörsanvändarnamn) kopplade till ACR-avbildningen.
-   **Lösenordet\***  -lösenordet som associeras med ACR-avbildning.

    >[!NOTE]
    >Det användarnamn och lösenord krävs för att se till att partner har åtkomst till ACR som nämns i publiceringsprocessen.


### <a name="image-version"></a>Bildversion

När du publicerar en behållaravbildning, du kan ange en eller flera taggar och SHA Överför sammanfattningar.

**Bild-taggen\* eller sammanfattad**
 
- Den här tagg- eller måste innehålla en `latest` tagg och en tagg för version (till exempel att starta med `xx.xx.xx-` där xx är ett tal). De ska vara [manifest taggar](https://github.com/estesp/manifest-tool) till når flera plattformar. Alla taggar som refereras till av en manifest tagg måste också läggas till så att vi kan ladda upp dem. 
- Du kan lägga till flera versioner av behållaren med hjälp av taggar. Alla manifest taggar (utom `latest`) måste börja med antingen `X.Y-` eller `X.Y.Z-` där X, Y, Z är heltal. <br/> Till exempel om en `latest` tagga pekar `1.0.1-linux-x64`, `1.0.1-linux-arm32`, och `1.0.1-windows-arm32`, dessa taggar måste läggas till här.

>[!NOTE]
>Kom ihåg att lägga till en **testtagg** i avbildningen så att du kan identifiera avbildningen under testningen.


## <a name="next-steps"></a>Nästa steg

Använd den [Marketplace fliken](./cpp-marketplace-tab.md) att skapa en marketplace-beskrivning av erbjudandet. 
