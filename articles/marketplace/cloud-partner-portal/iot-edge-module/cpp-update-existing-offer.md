---
title: Uppdatera en befintlig Azure IoT Edge modul-erbjudande | Azure Marketplace
description: Så här uppdaterar du ett befintligt IoT Edge modul-erbjudande på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: cd0167e1af5bf8ef667df88237d83e9f33ed41f9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813399"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Uppdatera ett befintligt IoT Edge modul-erbjudande

I den här artikeln beskrivs olika aspekter av att uppdatera IoT Edge modul-erbjudandet i [Cloud Partner Portal](https://cloudpartner.azure.com/) och sedan publicera om erbjudandet.

Det finns flera orsaker till varför du kanske vill uppdatera erbjudandet, till exempel:

-  Lägga till en ny IoT Edge modul avbildnings version till befintliga SKU: er.
-  Lägger till nya SKU: er.
-  Uppdaterar Marketplace-metadata för erbjudandet eller enskilda SKU: er.

För att hjälpa dig i dessa ändringar erbjuder portalen funktionerna **Jämför** och **Historik** .  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Otillåtna ändringar av IoT Edge-modulens erbjudande eller SKU

Det finns attribut för ett IoT Edge modul-erbjudande eller SKU som inte kan ändras när erbjudandet är aktivt på Azure Marketplace. Du kan inte ändra följande inställningar:

-  Erbjudandets **ID** och **utgivar-ID**
-  **SKU-ID** för befintliga SKU: er
-  Versions taggar, till exempel: `1.0.1`
-  Ändringar i fakturering/licens modell för befintliga SKU: er

## <a name="common-update-operations"></a>Vanliga uppdaterings åtgärder

Följande uppdaterings åtgärder är vanliga.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Uppdatera avbildnings versionen av IoT Edge module för en SKU

Det är vanligt att en IoT Edge modul-avbildning uppdateras regelbundet med säkerhets korrigeringar, ytterligare funktioner och så vidare. I det här scenariot vill du uppdatera IoT Edge module-avbildningen som din SKU refererar till med hjälp av följande steg:

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**hittar du det erbjudande du vill uppdatera.

3.  På fliken **SKU: er** väljer du den SKU som är kopplad till IoT Edge modul avbildningen som ska uppdateras.

4.  Under **bild av Edge-modul**väljer du **+ ny avbildnings version** för att lägga till en ny IoT Edge modul-avbildning.

5.  Ange **avbildnings versioner**för nya IoT Edge module. Avbildnings versionen måste följa rikt linjerna för taggar som tidigare versioner. Versions Taggar ska vara av formatet X. Y. Z, där X, Y och Z är heltal. Kontrol lera att den nya version som du anger är större än alla tidigare versioner.

6.  Välj **publicera** för att starta arbets flödet för att publicera den nya IoT Edge-modulens version på Azure Marketplace.

### <a name="add-a-new-sku"></a>Lägg till en ny SKU

Använd följande steg för att göra en ny SKU tillgänglig för ditt erbjudande: 

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**hittar du det erbjudande du vill uppdatera.

3.  Under fliken **SKU: er** väljer du **Lägg till ny SKU** och anger ett **SKU-ID** i popup-fönstret.

4.  Publicera IoT Edge-modulen igen med hjälp av stegen som beskrivs i [publicera en IoT Edge-modul på Azure Marketplace](./cpp-publish-offer.md).

5.  Välj **publicera** för att starta arbets flödet för att publicera den nya SKU: n.


### <a name="update-offer-marketplace-metadata"></a>Uppdatera metadata för Marketplace för erbjudande

Använd följande steg för att uppdatera Marketplace-metadata som är kopplade till ditt erbjudande. (Till exempel: företags namn, logo typer osv.)

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**hittar du det erbjudande som du vill uppdatera.

3.  Gå till **Marketplace** -fliken. Följ anvisningarna i artikeln [publicera en IoT Edge-modul till Azure Marketplace](./cpp-publish-offer.md) för att göra ändringar i metadata.

4.  Klicka på **publicera** för att starta arbets flödet för att publicera ändringarna.

## <a name="compare-feature"></a>Jämför funktion

När du gör ändringar i ett publicerat erbjudande kan du använda funktionen **Jämför** för att granska de ändringar som du har gjort. 

**Så här använder du funktionen compare:**

1.  Välj **Jämför** för ditt erbjudande när som helst i redigerings processen.

    ![Knappen Jämför funktion](./media/iot-edge-module-compare.png)


2.  Titta på sida-vid-sida-versioner av marknadsförings till gångar och metadata.


## <a name="history-of-publishing-actions"></a>Historik för publicerings åtgärder

Om du vill se historisk publicerings aktivitet väljer du fliken **Historik** i det vänstra navigerings meny fältet i Cloud Partner Portal. Du kan se de tidsstämplade åtgärderna som vidtagits under livs längden för dina Azure Marketplace-erbjudanden.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
