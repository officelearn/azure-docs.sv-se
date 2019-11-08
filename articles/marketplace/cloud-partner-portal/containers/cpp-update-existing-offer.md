---
title: Uppdatera ett befintligt erbjudande för Azure-behållare | Azure Marketplace
description: Så här uppdaterar du ett befintligt behållar erbjudande på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 40cd375b11eb5b7ad5943fea9839b6339a7d002f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823075"
---
# <a name="update-an-existing-container-offer"></a>Uppdatera ett befintligt container erbjudande

I den här artikeln beskrivs olika aspekter av att uppdatera behållar erbjudandet i [Cloud Partner Portal](https://cloudpartner.azure.com/).

Det finns flera orsaker till varför du kanske vill uppdatera erbjudandet, till exempel:

-  Lägga till en ny behållar avbildnings version i befintliga SKU: er.
-  Lägger till nya SKU: er.
-  Uppdaterar Marketplace-metadata för erbjudandet eller enskilda SKU: er.

För att hjälpa dig i dessa ändringar tillhandahåller portalen funktionerna **Jämför** och **Historik** .  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Otillåtna ändringar i ett behållar erbjudande eller SKU

Det finns attribut för ett behållar erbjudande eller SKU som inte kan ändras när erbjudandet är aktivt på Azure Marketplace. Du kan inte ändra följande inställningar:

-  Erbjudandets **ID** och **utgivar-ID**
-  **SKU-ID** för befintliga SKU: er
-  Versions taggar, till exempel: `1.0.1`
-  Ändringar i fakturering/licens modell för befintliga SKU: er

## <a name="common-update-operations"></a>Vanliga uppdaterings åtgärder

Följande uppdaterings åtgärder är vanliga.

### <a name="update-container-image-version-for-a-sku"></a>Uppdatera behållar avbildnings version för en SKU

Det är vanligt att en behållar avbildning uppdateras regelbundet med säkerhets korrigeringar, ytterligare funktioner och så vidare. I det här scenariot vill du uppdatera behållar avbildningen som din SKU refererar till med hjälp av följande steg:

1. Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3. På fliken **SKU: er** väljer du den SKU som är kopplad till behållar avbildningen som ska uppdateras.
4. Under **behållar avbildning**väljer du **+ ny avbildnings version** för att lägga till en ny behållar avbildning.
5. Ange de nya behållar **avbildnings versionerna**. Avbildnings versionen måste följa rikt linjerna för taggar som tidigare versioner. Versions Taggar ska vara av formatet X. Y. Z, där X, Y och Z är heltal. Kontrol lera att den nya version som du anger är större än alla tidigare versioner.
6. Välj **publicera** för att starta arbets flödet för att publicera den nya behållar avbildnings versionen på Azure Marketplace.

### <a name="add-a-new-sku"></a>Lägg till en ny SKU

Använd följande steg för att göra en ny SKU tillgänglig för ditt erbjudande:

1. Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3. Under fliken **SKU: er** väljer du **Lägg till ny SKU** och anger ett **SKU-ID** i popup-fönstret.
4. Publicera om behållaren med hjälp av stegen som beskrivs i [publicera container erbjudande](./cpp-publish-offer.md).
5. Välj **publicera** för att starta arbets flödet för att publicera den nya SKU: n.

### <a name="update-offer-marketplace-metadata"></a>Uppdatera metadata för Marketplace för erbjudande

Använd följande steg för att uppdatera Marketplace-metadata som är kopplade till ditt erbjudande. (Till exempel: företags namn, logo typer och så vidare)

1. Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3. Gå till **Marketplace** -fliken. Följ anvisningarna i artikeln [publicera container erbjudandet](./cpp-publish-offer.md) för att göra ändringar i metadata.
4. Klicka på **publicera** för att starta arbets flödet för att publicera ändringarna.

## <a name="compare-feature"></a>Jämför funktion

När du gör ändringar i ett publicerat erbjudande kan du använda funktionen **Jämför** för att granska de ändringar som du har gjort.

### <a name="to-use-the-compare-feature"></a>Så här använder du funktionen compare:

1. Välj jämför för ditt erbjudande när som helst i redigerings processen.
2. Titta på sida-vid-sida-versioner av marknadsförings till gångar och metadata.


## <a name="history-of-publishing-actions"></a>Historik för publicerings åtgärder

Om du vill se historisk publicerings aktivitet väljer du fliken **Historik** i det vänstra navigerings meny fältet i Cloud Partner Portal. Du kan se de tidsstämplade åtgärderna som vidtagits under livs längden för dina Azure Marketplace-erbjudanden.