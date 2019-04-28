---
title: Uppdatera ett befintligt Azure IoT Edge-modulen erbjudande | Microsoft Docs
description: Så här att uppdatera en befintlig IoT Edge-modulen erbjudandet på Azure Marketplace.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: ca7bed26d91c28304638e85d6da93708bfcfcada
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914134"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Uppdatera en befintlig IoT Edge-modulen erbjudandet

Den här artikeln visar via olika aspekter för att uppdatera erbjudandet IoT Edge-modulen i den [Cloud Partner Portal](https://cloudpartner.azure.com/) och sedan publicera erbjudandet.

Det finns flera orsaker till varför du kanske vill uppdatera ditt erbjudande, till exempel:

-  Lägger till en ny version av IoT Edge-modul-avbildning till befintliga SKU: er.
-  Lägger till nya SKU: er.
-  Uppdaterar marketplace-metadata för erbjudandet eller enskilda SKU: er.

För att hjälpa dig på de här ändringarna, portalen erbjuder den **jämför** och **historik** funktioner.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Unpermitted ändringar i erbjudandet för IoT Edge-modul eller SKU

Det finns attribut för en IoT Edge-modulen erbjudandet eller SKU: N som inte kan ändras när erbjudandet är aktiv på Azure Marketplace. Du kan inte ändra följande inställningar:

-  **ID för erbjudande** och **Publicerings-ID** i erbjudandet
-  **SKU-ID** befintliga SKU: er
-  Version taggar, till exempel: `1.0.1`
-  Fakturerings-/ licensmodell ändras till befintliga SKU: er

## <a name="common-update-operations"></a>Vanliga åtgärder för uppdatering

Följande uppdateringsåtgärder är vanliga.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Uppdatera versionen av IoT Edge-modulen avbildning för en SKU

Det är vanligt för en IoT Edge-modulen avbildning uppdateras regelbundet med säkerhetskorrigeringar, extrafunktioner och så vidare. I det här scenariot som du vill uppdatera modulen IoT Edge-avbildning som din SKU som refererar till med hjälp av följande steg:

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**, erbjudandet som du vill uppdatera.

3.  I den **SKU: er** väljer du en SKU som är associerade med IoT Edge-modulen bilden att uppdatera.

4.  Under **Edge-modul-avbildning**väljer **+ ny Avbildningsversion** att lägga till en ny IoT Edge-modulen avbildning.

5.  Ange den nya IoT Edge-modulen **bild versioner**. Versionsnumret för avbildningen måste följa samma riktlinjer som taggar som tidigare versioner. Version taggar ska vara i formatet X.Y.Z, där X, Y och Z är heltal. Kontrollera att den nya versionen som du anger är större än alla tidigare versioner.

6.  Välj **publicera** att starta arbetsflödet för att publicera den nya versionen av IoT Edge-modul i Azure Marketplace.

### <a name="add-a-new-sku"></a>Lägg till en ny SKU

Använd följande steg för att göra en ny SKU som är tillgängliga för ditt erbjudande: 

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**, erbjudandet som du vill uppdatera.

3.  Under den **SKU: er** fliken **lägga till nya SKU: N** och ger en **SKU ID** i popup-fönstret.

4.  Publicera IoT Edge-modulen genom att följa anvisningarna i [publicera en IoT Edge-modul i Azure Marketplace](./cpp-publish-offer.md).

5.  Välj **publicera** att starta arbetsflödet för att publicera din nya SKU: N.


### <a name="update-offer-marketplace-metadata"></a>Metadata för marketplace-erbjudande

Använd följande steg för att uppdatera metadata för marketplace som är associerade med ditt erbjudande. (Till exempel: företagsnamn, logotyper, osv.)

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**, erbjudandet som du vill uppdatera.

3.  Gå till den **Marketplace** fliken. Följ instruktionerna i den [publicera en IoT Edge-modul i Azure Marketplace](./cpp-publish-offer.md) artikeln om du vill göra ändringar i metadata.

4.  Välj **publicera** att starta arbetsflödet för att publicera dina ändringar.

## <a name="compare-feature"></a>Jämför funktioner

När du gör ändringar på ett erbjudande för publicerade, kan du använda den **jämför** funktionen för att granska de ändringar som du har gjort. 

**Använda jämförelsefunktionen:**

1.  När som helst redigeringsprocessen, Välj **jämför** för ditt erbjudande.

    ![Jämför funktionen knappen](./media/iot-edge-module-compare.png)


2.  Titta på sida-vid-sida-versioner av marknadsföring tillgångar och metadata.


## <a name="history-of-publishing-actions"></a>Historik för publiceringen av åtgärder

Om du vill se historiska publiceringsaktivitet, Välj den **historik** fliken på den vänstra menyn fältet av partnerportalen i molnet. Du kan se tidsstämplad-åtgärder som vidtagits under livslängden för dina Azure Marketplace-erbjudanden.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
