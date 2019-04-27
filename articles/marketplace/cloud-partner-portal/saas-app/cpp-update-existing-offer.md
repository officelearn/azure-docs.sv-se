---
title: Uppdatera befintligt erbjudande för Azure SaaS-program | Microsoft Docs
description: Så här att uppdatera erbjudandet för en befintlig SaaS-program på Azure Marketplace.
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
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d490a0ec09e351e4cee00e7c30c9161bdfac3e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594287"
---
# <a name="update-an-existing-saas-application-offer"></a>Uppdatera ett befintligt erbjudande för SaaS-program

Det finns olika typer av uppdateringar som du kan göra att ditt erbjudande när den har publicerats och är aktiv. Alla ändringar du gör i den nya versionen av ditt erbjudande ska sparas och publiceras igen att den återspeglar i Marketplace. Den här artikeln vägleder genom de olika delarna av uppdaterar din SaaS-erbjudande i den [Cloud Partner Portal](https://cloudpartner.azure.com/).

Det finns flera orsaker till varför du kanske vill uppdatera ditt erbjudande, till exempel:

- Lägga till en ny version till en befintlig app.
- Uppdaterar en app.
- Lägger till nya funktioner till en app.
- Uppdaterar marketplace-metadata för erbjudandet.

För att hjälpa dig på de här ändringarna, portalen innehåller den **jämför** och **historik** funktioner.

## <a name="unpermitted-changes-to-a-saas-offer"></a>Unpermitted ändringar till ett SaaS-erbjudande

Det finns attribut för ett SaaS-erbjudande som inte kan ändras när erbjudandet är aktiv på Azure Marketplace. Du kan inte ändra följande inställningar:

- Erbjudande-ID och Publicerings-ID för erbjudandet
- Version taggar, till exempel: `1.0.1`
- Fakturerings-/ licensmodell ändras till befintliga erbjudanden.

## <a name="common-update-operations"></a>Vanliga åtgärder för uppdatering
 
Följande uppdateringsåtgärder är vanliga.

### <a name="update-offer-contacts"></a>Uppdatera erbjudandet kontakter

Använd följande steg för att uppdatera supportkontakter för ditt erbjudande.

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3. Gå till den **kontakter** fliken. Uppdatera dina kontakter.
4. Välj **publicera** att starta arbetsflödet för att publicera dina ändringar.


### <a name="update-offer-marketplace-metadata"></a>Metadata för marketplace-erbjudande

Använd följande steg för att uppdatera metadata för marketplace som är associerade med ditt erbjudande. (Till exempel: företagsnamn, logotyper, osv.)

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3. Gå till den **Storefront information** fliken. Följ instruktionerna i den [publicera SaaS erbjuder](./cpp-publish-offer.md) artikeln om du vill göra ändringar i metadata.
4. Välj **publicera** att starta arbetsflödet för att publicera dina ändringar.

## <a name="compare-feature"></a>Jämför funktioner

När du gör ändringar i ett publicerade erbjudande, använder du jämförelsefunktionen för att granska de ändringar som du har gjort. Nästa skärmdump visar alternativet jämför till en publicerad.

![Använda jämfört med för att se erbjuder ändringar i partnerportalen i molnet](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>Använda jämförelsefunktionen:

1. När som helst redigeringsprocessen, väljer du jämför för ditt erbjudande.
2. Titta på sida-vid-sida-versioner av marknadsföring tillgångar och metadata.

## <a name="publishing-history"></a>Publicera historik

Om du vill se historiska publiceringsaktivitet, Välj den **historik** fliken på den vänstra menyn fältet av partnerportalen i molnet. Du kan se tidsstämplad-åtgärder som vidtagits under livslängden för dina Azure Marketplace-erbjudanden.

![Se erbjuder historiken i partnerportalen i molnet](./media/saas-offer-history.png)

Du kan använda sidan Granska tidigare för att söka efter ett specifikt erbjudande och filter som utgivare, erbjudande och händelsetyp (till exempel OfferGoLiveRequested.) Du kan också hämta granskningshistoriken som en csv-fil.


## <a name="next-steps"></a>Nästa steg

[Erbjudande för SaaS-program](./cpp-saas-offer.md)