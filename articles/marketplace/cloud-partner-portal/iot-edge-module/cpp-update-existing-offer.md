---
title: Uppdatera ett befintligt Azure IoT Edge-modulerbjudande | Azure Marketplace
description: Så här uppdaterar du ett befintligt IoT Edge-modulerbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 019711f35d249e6684013feddb6b453d509f66d4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985362"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Uppdatera ett befintligt IoT Edge-modulerbjudande

>[!Important]
>Från och med den 13 april 2020 börjar vi flytta hanteringen av din IoT Edge-modul till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett IoT Edge-modulerbjudande](https://aka.ms/AzureCreateIoT) för att hantera dina migrerade erbjudanden.

Den här artikeln går igenom de olika aspekterna av att uppdatera ditt IoT Edge-modulerbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com/) och sedan publicera erbjudandet igen.

Det finns flera anledningar till varför du kanske vill uppdatera erbjudandet, till exempel:

-  Lägga till en ny IoT Edge-modulavbildningsversion i befintliga SKU:er.
-  Lägga till nya SKU: er.
-  Uppdaterar marknadsplatsmetadata för erbjudandet eller enskilda SKU:er.

För att hjälpa dig med dessa ändringar erbjuder portalen funktionerna **Jämför** och **historik.**  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Otillåtna ändringar i IoT Edge-modulerbjudande eller SKU

Det finns attribut för ett IoT Edge-modulerbjudande eller SKU som inte kan ändras när erbjudandet är live på Azure Marketplace. Du kan inte ändra följande inställningar:

-  **Erbjudande-ID** och **Utgivar-ID** för erbjudandet
-  **SKU-ID** för befintliga SKU:er
-  Versionstaggar, till exempel:`1.0.1`
-  Ändringar av fakturerings-/licensmodell till befintliga SKU:er

## <a name="common-update-operations"></a>Vanliga uppdateringsåtgärder

Följande uppdateringsåtgärder är vanliga.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Uppdatera avbildningsversionen av IoT Edge-modulen för en SKU

Det är vanligt att en IoT Edge-modulavbildning uppdateras regelbundet med säkerhetskorrigeringar, ytterligare funktioner och så vidare. I det här fallet vill du uppdatera IoT Edge-modulen avbildning som din SKU refererar till med hjälp av följande steg:

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).

2.  Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.

3.  På fliken **SKU väljer** du den SKU som är associerad med avbildningen IoT Edge-modul som ska uppdateras.

4.  Under **Edge-modulbild**väljer du **+ Ny bildversion** om du vill lägga till en ny IoT Edge-modulavbildning.

5.  Ange de nya IoT **Edge-modulbildversionerna**. Bildversionen måste följa samma riktlinjer för taggar som tidigare versioner. Versionstaggar ska vara av formuläret X.Y.Z, där X, Y och Z är heltal. Kontrollera att den nya versionen du anger är större än alla tidigare versioner.

6.  Välj **Publicera om** du vill starta arbetsflödet för att publicera den nya IoT Edge-modulversionen på Azure Marketplace.

### <a name="add-a-new-sku"></a>Lägga till en ny SKU

Gör en ny SKU tillgänglig för ditt erbjudande: 

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).

2.  Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.

3.  Under fliken **SKU** väljer du **Lägg till ny SKU** och anger ett **SKU-ID** i popup-fönstret.

4.  Publicera om IoT Edge-modulen med hjälp av stegen som beskrivs i [Publicera en IoT Edge-modul till Azure Marketplace](./cpp-publish-offer.md).

5.  Välj **Publicera om** du vill starta arbetsflödet om du vill publicera den nya SKU:n.


### <a name="update-offer-marketplace-metadata"></a>Uppdatera metadata för erbjudandemarknadsplats

Följ följande steg för att uppdatera marketplace-metadata som är associerade med ditt erbjudande. (Till exempel: företagsnamn, logotyper, etc.)

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).

2.  Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.

3.  Gå till fliken **Marketplace.** Använd instruktionerna i artikeln [Publicera en IoT Edge-modul till Azure Marketplace](./cpp-publish-offer.md) för att göra metadataändringar.

4.  Välj **Publicera om** du vill starta arbetsflödet om du vill publicera ändringarna.

## <a name="compare-feature"></a>Jämför funktion

När du gör ändringar i ett publicerat erbjudande kan du använda funktionen **Jämför** för att granska de ändringar du har gjort. 

**Så här använder du funktionen Jämför:**

1.  När som helst i redigeringsprocessen väljer du **Jämför** för ditt erbjudande.

    ![Knappen Jämför funktion](./media/iot-edge-module-compare.png)


2.  Titta på sida vid sida-versioner av marknadsföringstillgångar och metadata.


## <a name="history-of-publishing-actions"></a>Historia av publiceringsåtgärder

Om du vill visa historisk publiceringsaktivitet väljer du fliken **Historik** på den vänstra navigeringsmenyraden i Cloud Partner Portal. Du kan se de tidsstämplade åtgärder som vidtagits under livstiden för dina Azure Marketplace-erbjudanden.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
