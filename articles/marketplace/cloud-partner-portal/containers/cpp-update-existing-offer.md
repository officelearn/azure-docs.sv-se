---
title: Uppdatera ett befintligt Azure Containers-erbjudande | Azure Marketplace
description: Så här uppdaterar du ett befintligt behållarerbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 650ef45e56184b67efba67810580d9d6e763a4d0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271275"
---
# <a name="update-an-existing-container-offer"></a>Uppdatera ett befintligt behållarerbjudande

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Container-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Container-erbjudande](https://aka.ms/CreateContainerOffer) för att hantera dina migrerade erbjudanden.

Den här artikeln går igenom de olika aspekterna av att uppdatera ditt behållarerbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com/).

Det finns flera anledningar till varför du kanske vill uppdatera erbjudandet, till exempel:

-  Lägga till en ny behållaravbildningsversion i befintliga SKU:er.
-  Lägga till nya SKU: er.
-  Uppdaterar marknadsplatsmetadata för erbjudandet eller enskilda SKU:er.

För att hjälpa dig med dessa ändringar tillhandahåller portalen funktionerna **Jämför** och **historik.**  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Otillåtna ändringar i ett behållarerbjudande eller SKU

Det finns attribut för ett behållarerbjudande eller SKU som inte kan ändras när erbjudandet är live på Azure Marketplace. Du kan inte ändra följande inställningar:

-  **Erbjudande-ID** och **Utgivar-ID** för erbjudandet
-  **SKU-ID** för befintliga SKU:er
-  Versionstaggar, till exempel:`1.0.1`
-  Ändringar av fakturerings-/licensmodell till befintliga SKU:er

## <a name="common-update-operations"></a>Vanliga uppdateringsåtgärder

Följande uppdateringsåtgärder är vanliga.

### <a name="update-container-image-version-for-a-sku"></a>Uppdatera behållaravbildningsversion för en SKU

Det är vanligt att en behållaravbildning uppdateras regelbundet med säkerhetskorrigeringar, ytterligare funktioner och så vidare. I det här fallet vill du uppdatera behållaravbildningen som SKU refererar till med hjälp av följande steg:

1. Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2. Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3. På fliken **SKU väljer** du den SKU som är associerad med behållaravbildningen som ska uppdateras.
4. Under **Behållaravbildning**väljer du **+ Ny bildversion** om du vill lägga till en ny behållaravbildning.
5. Ange de nya **behållaravbildningsversionerna**. Bildversionen måste följa samma riktlinjer för taggar som tidigare versioner. Versionstaggar ska vara av formuläret X.Y.Z, där X, Y och Z är heltal. Kontrollera att den nya versionen du anger är större än alla tidigare versioner.
6. Välj **Publicera om** du vill starta arbetsflödet om du vill publicera den nya behållaravbildningsversionen på Azure Marketplace.

### <a name="add-a-new-sku"></a>Lägga till en ny SKU

Gör en ny SKU tillgänglig för ditt erbjudande:

1. Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2. Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3. Under fliken **SKU** väljer du **Lägg till ny SKU** och anger ett **SKU-ID** i popup-fönstret.
4. Publicera om behållaren med hjälp av stegen som beskrivs i [Erbjudandet publicera behållare](./cpp-publish-offer.md).
5. Välj **Publicera om** du vill starta arbetsflödet om du vill publicera den nya SKU:n.

### <a name="update-offer-marketplace-metadata"></a>Uppdatera metadata för erbjudandemarknadsplats

Följ följande steg för att uppdatera marketplace-metadata som är associerade med ditt erbjudande. (Till exempel: företagsnamn, logotyper och etc.)

1. Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2. Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3. Gå till fliken **Marketplace.** Använd instruktionerna i erbjudandeartikeln [Publicera behållare](./cpp-publish-offer.md) för att göra metadataändringar.
4. Välj **Publicera om** du vill starta arbetsflödet om du vill publicera ändringarna.

## <a name="compare-feature"></a>Jämför funktion

När du gör ändringar i ett publicerat erbjudande kan du använda funktionen **Jämför** för att granska de ändringar du har gjort.

### <a name="to-use-the-compare-feature"></a>Så här använder du funktionen Jämför:

1. När som helst i redigeringsprocessen väljer du Jämför för ditt erbjudande.
2. Titta på sida vid sida-versioner av marknadsföringstillgångar och metadata.


## <a name="history-of-publishing-actions"></a>Historia av publicerande handlingar

Om du vill visa historisk publiceringsaktivitet väljer du fliken **Historik** på den vänstra navigeringsmenyraden i Cloud Partner Portal. Du kan se de tidsstämplade åtgärder som vidtagits under livstiden för dina Azure Marketplace-erbjudanden.
