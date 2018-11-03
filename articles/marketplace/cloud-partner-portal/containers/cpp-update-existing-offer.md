---
title: Uppdatera ett befintligt Azure Containers-erbjudande | Microsoft Docs
description: Så här att uppdatera en befintlig behållare erbjudandet på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 2b568717b6656fb9ae15e9a6dbd27441689c4372
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979836"
---
# <a name="update-an-existing-container-offer"></a>Uppdatera ett befintligt container-erbjudande

Den här artikeln visar via olika aspekter för att uppdatera erbjudandet behållaren i den [Cloud Partner Portal](https://cloudpartner.azure.com/).

Det finns flera orsaker till varför du kanske vill uppdatera ditt erbjudande, till exempel:

-  Lägger till en ny behållare Bildversion till befintliga SKU: er.
-  Lägger till nya SKU: er.
-  Uppdaterar marketplace-metadata för erbjudandet eller enskilda SKU: er.

För att hjälpa dig på de här ändringarna, portalen innehåller den **jämför** och **historik** funktioner.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Unpermitted ändringar i en behållare erbjudandet eller SKU

Det finns attribut för ett erbjudande för behållaren eller SKU: N som inte kan ändras när erbjudandet är aktiv på Azure Marketplace. Du kan inte ändra följande inställningar:

-  **ID för erbjudande** och **Publicerings-ID** i erbjudandet
-  **SKU-ID** befintliga SKU: er
-  Version taggar, till exempel: `1.0.1`
-  Fakturerings-/ licensmodell ändras till befintliga SKU: er

## <a name="common-update-operations"></a>Vanliga åtgärder för uppdatering

Följande uppdateringsåtgärder är vanliga.

### <a name="update-container-image-version-for-a-sku"></a>Uppdatera versionsnumret för avbildningen av behållare för en SKU

Det är vanligt att en behållaravbildning uppdateras regelbundet med säkerhetskorrigeringar, extrafunktioner och så vidare. I det här scenariot som du vill uppdatera behållaravbildningen som din SKU som refererar till med hjälp av följande steg:

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3. I den **SKU: er** väljer du en SKU som är associerade med behållaravbildningen att uppdatera.
4. Under **behållaravbildning**väljer **+ ny Avbildningsversion** att lägga till en ny behållaravbildning.
5. Ange den nya behållaren **bild versioner**. Versionsnumret för avbildningen måste följa samma riktlinjer som taggar som tidigare versioner. Version taggar ska vara i formatet X.Y.Z, där X, Y och Z är heltal. Kontrollera att den nya versionen som du anger är större än alla tidigare versioner.
6. Välj **publicera** att starta arbetsflödet för att publicera din nya behållare Avbildningsversion på Azure Marketplace.

### <a name="add-a-new-sku"></a>Lägg till en ny SKU

Använd följande steg för att göra en ny SKU som är tillgängliga för ditt erbjudande:

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3. Under den **SKU: er** fliken **lägga till nya SKU: N** och ger en **SKU ID** i popup-fönstret.
4. Publicera behållaren med hjälp av anvisningarna i [publicera behållare erbjudandet](./cpp-publish-offer.md).
5. Välj **publicera** att starta arbetsflödet för att publicera din nya SKU: N.

### <a name="update-offer-marketplace-metadata"></a>Metadata för marketplace-erbjudande

Använd följande steg för att uppdatera metadata för marketplace som är associerade med ditt erbjudande. (Till exempel: företagets namn, logotyper och osv.)

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3. Gå till den **Marketplace** fliken. Följ instruktionerna i den [publicera behållare erbjudandet](./cpp-publish-offer.md) erbjuder artikeln om du vill göra ändringar i metadata.
4. Välj **publicera** att starta arbetsflödet för att publicera dina ändringar.

## <a name="compare-feature"></a>Jämför funktioner

När du gör ändringar i en publicerad erbjudandet kan du använda den **jämför** funktionen för att granska de ändringar som du har gjort.

### <a name="to-use-the-compare-feature"></a>Använda jämförelsefunktionen:

1. När som helst redigeringsprocessen, väljer du jämför för ditt erbjudande.
2. Titta på sida-vid-sida-versioner av marknadsföring tillgångar och metadata.


## <a name="history-of-publishing-actions"></a>Historik för publiceringen av åtgärder

Om du vill se historiska publiceringsaktivitet, Välj den **historik** fliken på den vänstra menyn fältet av partnerportalen i molnet. Du kan se tidsstämplad-åtgärder som vidtagits under livslängden för dina Azure Marketplace-erbjudanden.