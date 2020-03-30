---
title: Uppdatera ett befintligt Azure-programerbjudande | Azure Marketplace
description: Så här uppdaterar du ett befintligt Azure-programerbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: dsindona
ms.openlocfilehash: 152fd24fbc5d2762d381ffce2a937bc448858b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275959"
---
# <a name="update-an-existing-azure-application-offer"></a>Uppdatera ett befintligt Azure-programerbjudande

Det finns olika typer av uppdateringar som du kanske vill göra med ditt erbjudande efter att det har publicerats och är live. Alla ändringar du gör i din nya version av erbjudandet ska sparas och publiceras på nytt så att det visas på Marketplace. Den här artikeln går igenom de olika aspekterna av att uppdatera ditt hanterade programerbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com/).

Det finns flera anledningar till varför du kanske vill uppdatera erbjudandet, till exempel:

- Lägga till en ny bildversion i befintliga SKU:er.
- Lägga till nya SKU: er.
- Uppdaterar marknadsplatsmetadata för erbjudandet eller enskilda SKU:er.

För att hjälpa dig med dessa ändringar tillhandahåller portalen funktionerna **Jämför** och **historik.**

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Otillåtna ändringar i ett Azure-programerbjudande eller SKU

Det finns attribut för ett behållarerbjudande eller SKU som inte kan ändras när erbjudandet är live på Azure Marketplace. Du kan inte ändra följande inställningar:

- Erbjudande-ID och Utgivar-ID för erbjudandet
- SKU-ID för befintliga SKU:er
- Versionstaggar, till exempel:`1.0.1`
- Ändringar av fakturerings-/licensmodell till befintliga SKU:er

## <a name="common-update-operations"></a>Vanliga uppdateringsåtgärder

Följande uppdateringsåtgärder är vanliga.

### <a name="update-image-version-for-a-sku"></a>Uppdatera bildversion för en SKU

Det är vanligt att en bild uppdateras regelbundet med säkerhetskorrigeringar, ytterligare funktioner och så vidare. I det här fallet vill du uppdatera avbildningen som din SKU refererar till med hjälp av följande steg:

1. Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2. Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3. På fliken **SKU väljer** du den SKU som är associerad med avbildningen som ska uppdateras.
4. Välj **+ Ny bildversion** om du vill lägga till en ny bild.
5. Ange de nya bildversionerna. Bildversionen måste följa samma riktlinjer för taggar som tidigare versioner. Versionstaggar ska vara av formuläret X.Y.Z, där X, Y och Z är heltal. Kontrollera att den nya versionen du anger är större än alla tidigare versioner.
6. Välj **Publicera om** du vill starta arbetsflödet om du vill publicera den nya behållaravbildningsversionen på Azure Marketplace.

### <a name="add-a-new-sku"></a>Lägga till en ny SKU

Gör en ny SKU tillgänglig för ditt erbjudande:

1. Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2. Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3. Under fliken **SKU** väljer du **Lägg till ny SKU** och anger ett **SKU-ID** i popup-fönstret.
4. Publicera erbjudandet igen med hjälp av stegen som beskrivs i [Erbjudandet publicera Azure-program](./cpp-publish-offer.md).
5. Välj **Publicera om** du vill starta arbetsflödet om du vill publicera den nya SKU:n.

### <a name="update-offer-marketplace-metadata"></a>Uppdatera metadata för erbjudandemarknadsplats

Följ följande steg för att uppdatera marketplace-metadata som är associerade med ditt erbjudande. (Till exempel: företagsnamn, logotyper, etc.)

1. Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).
2. Under **Alla erbjudanden**hittar du det erbjudande du vill uppdatera.
3. Gå till fliken **Marketplace.** Använd instruktionerna i [Erbjudandet publicera Azure-program](./cpp-publish-offer.md) för att göra metadataändringar.
4. Välj **Publicera om** du vill starta arbetsflödet om du vill publicera ändringarna.
 
>[!Note]
>Opt-in (Cloud Solution Providers) partnerkanal är nu tillgänglig.  Se [Cloud Solution Providers](../../cloud-solution-providers.md) för mer information om marknadsföring ditt erbjudande via Microsoft CSP partnerkanaler.

## <a name="deleting-an-existing-offer"></a>Ta bort ett befintligt erbjudande

Du kan välja att ta bort ditt erbjudande från Marketplace. Om du tar bort ett erbjudande påverkas inte aktuella köp av erbjudandet. Dessa kundköp kommer att fortsätta att fungera som tidigare. Erbjudandet kommer dock inte att vara tillgängligt för nya köp när borttagningen är klar.

Erbjudandet Uppsägning är processen att avsluta tjänsten och / eller licensavtal mellan dig och dina befintliga kunder.
Vägledning och policyer för borttagning och uppsägning av erbjudanden regleras av Microsoft Marketplace Publisher Agreement (se avsnitt 7) och deltagandepolicyerna (se avsnitt 6.2).

Mer information finns i [Ta bort och erbjuda/SKU från Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Jämför funktion

När du gör ändringar i ett publicerat erbjudande kan du använda funktionen Jämför för att granska de ändringar du har gjort.

Så här använder du funktionen Jämför:

1. När som helst i redigeringsprocessen väljer du Jämför för ditt erbjudande.
2. Titta på sida vid sida-versioner av marknadsföringstillgångar och metadata.

## <a name="history-of-publishing-actions"></a>Historia av publicerande handlingar

Om du vill visa historisk publiceringsaktivitet väljer du fliken **Historik** på den vänstra navigeringsmenyraden i Cloud Partner Portal. Du kan se de tidsstämplade åtgärder som vidtagits under livstiden för dina Azure Marketplace-erbjudanden.

## <a name="next-steps"></a>Nästa steg

[Erbjudande om Azure-program](./cpp-azure-app-offer.md)
