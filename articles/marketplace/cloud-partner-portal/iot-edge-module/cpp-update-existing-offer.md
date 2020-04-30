---
title: Uppdatera en befintlig Azure IoT Edge modul-erbjudande | Azure Marketplace
description: Så här uppdaterar du ett befintligt IoT Edge modul-erbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 17cce766f2d56766a9fcf260416d8bbf3e43d0c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142256"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Uppdatera ett befintligt IoT Edge modul-erbjudande

>[!Important]
>Från och med 13 april 2020 kommer vi att börja flytta hantering av IoT Edge modul-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett IoT Edge modul-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) för att hantera dina migrerade erbjudanden.

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
-  Versions taggar, till exempel:`1.0.1`
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
