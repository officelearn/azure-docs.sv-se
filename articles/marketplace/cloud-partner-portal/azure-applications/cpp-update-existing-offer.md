---
title: Uppdatera ett befintligt Azure-program-erbjudande | Microsoft Docs
description: Så här att uppdatera en befintlig Azure-program-erbjudandet på Azure Marketplace.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 1019c06afbdab0a6b0e172133da316aea01a5504
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744626"
---
# <a name="update-an-existing-azure-application-offer"></a>Uppdatera ett befintligt Azure-program-erbjudande

Det finns olika typer av uppdateringar som du kan göra att ditt erbjudande när den har publicerats och är aktiv. Alla ändringar du gör i den nya versionen av ditt erbjudande ska sparas och publiceras igen att den återspeglar i Marketplace. Den här artikeln visar via olika aspekter för att uppdatera ditt erbjudande om hanterat program i den [Cloud Partner Portal](https://cloudpartner.azure.com/).

Det finns flera orsaker till varför du kanske vill uppdatera ditt erbjudande, till exempel:

- Lägger till en ny Avbildningsversion till befintliga SKU: er.
- Lägger till nya SKU: er.
- Uppdaterar marketplace-metadata för erbjudandet eller enskilda SKU: er.

För att hjälpa dig på de här ändringarna, portalen innehåller den **jämför** och **historik** funktioner.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Unpermitted ändringar i ett erbjudande för Azure-program eller en SKU

Det finns attribut för ett erbjudande för behållaren eller SKU: N som inte kan ändras när erbjudandet är aktiv på Azure Marketplace. Du kan inte ändra följande inställningar:

- Erbjudande-ID och Publicerings-ID för erbjudandet
- SKU-ID för befintliga SKU: er
- Version taggar, till exempel: `1.0.1`
- Fakturerings-/ licensmodell ändras till befintliga SKU: er

## <a name="common-update-operations"></a>Vanliga åtgärder för uppdatering

Följande uppdateringsåtgärder är vanliga.

### <a name="update-image-version-for-a-sku"></a>Uppdatera versionsnumret för avbildningen för en SKU

Det är vanligt att en avbildning uppdateras regelbundet med säkerhetskorrigeringar, extrafunktioner och så vidare. I det här scenariot som du vill uppdatera den avbildning som din SKU som refererar till med hjälp av följande steg:

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3. I den **SKU: er** , Välj den SKU som är kopplade till avbildningen att uppdatera.
4. Välj **+ ny Avbildningsversion** att lägga till en ny avbildning.
5. Ange de nya versionerna av avbildningen. Versionsnumret för avbildningen måste följa samma riktlinjer som taggar som tidigare versioner. Version taggar ska vara i formatet X.Y.Z, där X, Y och Z är heltal. Kontrollera att den nya versionen som du anger är större än alla tidigare versioner.
6. Välj **publicera** att starta arbetsflödet för att publicera din nya behållare Avbildningsversion på Azure Marketplace.

### <a name="add-a-new-sku"></a>Lägg till en ny SKU

Använd följande steg för att göra en ny SKU som är tillgängliga för ditt erbjudande:

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3. Under den **SKU: er** fliken **lägga till nya SKU: N** och ger en **SKU ID** i popup-fönstret.
4. Publicera erbjudande med hjälp av anvisningarna i [publicera Azure-erbjudande för programmet](./cpp-publish-offer.md).
5. Välj **publicera** att starta arbetsflödet för att publicera din nya SKU: N.

### <a name="update-offer-marketplace-metadata"></a>Metadata för marketplace-erbjudande

Använd följande steg för att uppdatera metadata för marketplace som är associerade med ditt erbjudande. (Till exempel: företagsnamn, logotyper, osv.)

1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).
2. Under **alla erbjudanden**, erbjudandet som du vill uppdatera.
3. Gå till den **Marketplace** fliken. Följ instruktionerna i [publicera Azure-erbjudande för programmet](./cpp-publish-offer.md) att göra ändringar i metadata.
4. Välj **publicera** att starta arbetsflödet för att publicera dina ändringar.
 
>[!Note]
>Cloud Solution Providers (CSP) partner channel anmälan är nu tillgänglig.  Se [Cloud Solution Providers](../../cloud-solution-providers.md) mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner kanaler.

## <a name="deleting-an-existing-offer"></a>Tar bort ett befintligt erbjudande

Du kan välja att ta bort erbjudandet från Marketplace. Tar bort ett erbjudande påverkar inte aktuella inköp av erbjudandet. Dessa kundinköp fortsätter att fungera som tidigare. Men erbjudandet inte tillgängligt för alla nya inköp när borttagningen är klar.

Uppsägning av erbjudandet handlar om att säga upp tjänsten och/eller licensavtalet mellan dig och dina befintliga kunder.
Vägledning och principer som relaterar till borttagning av erbjudande och uppsägning regleras av Publiceringsavtalet för Microsoft Marketplace (se avsnitt 7) och Deltagandepolicyer (se avsnittet 6.2).

Mer information finns i [erbjudande/SKU: er från Azure Marketplace och ta bort](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Jämför funktioner

När du gör ändringar i ett publicerade erbjudande, använder du jämförelsefunktionen för att granska de ändringar som du har gjort.

Använda jämförelsefunktionen:

1. När som helst redigeringsprocessen, väljer du jämför för ditt erbjudande.
2. Titta på sida-vid-sida-versioner av marknadsföring tillgångar och metadata.

## <a name="history-of-publishing-actions"></a>Historik för publiceringen av åtgärder

Om du vill se historiska publiceringsaktivitet, Välj den **historik** fliken på den vänstra menyn fältet av partnerportalen i molnet. Du kan se tidsstämplad-åtgärder som vidtagits under livslängden för dina Azure Marketplace-erbjudanden.

## <a name="next-steps"></a>Nästa steg

[Erbjudande för Azure-program](./cpp-azure-app-offer.md)