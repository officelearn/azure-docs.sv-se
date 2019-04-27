---
title: Uppdatera Marketplace-erbjudanden – Azure Marketplace | Microsoft Docs
description: Update hittar du i Azure och AppSource marknadsplatser med partnerportalen i molnet
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: 76b607502324c3ca25b3536d5197a97dbb80399d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729646"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Uppdatera Azure Marketplace och AppSource erbjudanden

Det finns olika typer av uppdateringar som du kan använda för ditt erbjudande när den har publicerats.  Den [Cloud Partner Portal](https://cloudpartner.azure.com/) hjälper dig att korrekt ändra attribut för ett erbjudande, inklusive:

-  Lägger till ny virtuell dator (VM)-avbildning eller ett paket version till en befintlig SKU
-  Ändra de regioner som en SKU finns i
-  Att lägga till nya SKU: er
-  Uppdatera metadata för marketplace för erbjudanden och SKU: er 
-  Uppdatera priser på användningsbaserad betalning ger

Portalen innehåller också funktioner, till exempel möjligheten att jämförelse av funktioner och visa en historik över funktioner i ett erbjudande som hjälper dig att hantera ändringar.  När du har ändrat ett erbjudande eller SKU måste du publicera den innan ändringarna börjar ”live”.  Den här artikeln vägleder dig genom de olika delarna av uppdaterar dina marketplace-erbjudande.

## <a name="unpermitted-changes-to-an-offersku"></a>Unpermitted ändringar i ett erbjudande/SKU

Det finns vissa attribut för ett erbjudande eller en SKU som inte kan ändras när den har publicerats i marketplace.  Motsvarande fält är inaktiverade i den **redigeraren** fliken i portalen, till exempel:  

- Erbjudande-ID och Publicerings-ID
- SKU-ID 
- Antal befintliga SKU: er som datadisk
- Fakturering/licens Modelländringar av befintliga SKU: er
- Version taggar, till exempel: `1.0.1`


## <a name="common-update-operations"></a>Vanliga åtgärder för uppdatering

I följande avsnitt beskrivs hur du utför några av de flesta uppdateringsåtgärder.  Dessa åtgärder är inte tillgängliga för alla typer av erbjudanden.  Du måste logga in Cloud Partner Portal för att starta någon av dessa åtgärder.


### <a name="update-offer-contacts"></a>Uppdatera erbjudandet kontakter

Använd följande steg för att uppdatera supportkontakter för ditt erbjudande.
1. I den **alla erbjuder** väljer erbjudandet.
2. Välj den **kontakter** fliken. Uppdatera dina kontakter.
3. Välj knappen **Spara**.
4. Välj **publicera** att starta publiceringsprocessen.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Ändra ett erbjudande eller SKU är tillgängligt i de regioner

Med tiden kan du göra ditt erbjudande/SKU tillgängligt i fler regioner.
Alternativt kan du stoppa stöder erbjudande/SKU i en viss region.
Följ stegen nedan för att implementera dessa ändringar.

1. I den **alla erbjudanden** sidan, erbjudandet som du vill uppdatera.

Azure Marketplace-erbjudanden:

1. Välj den **SKU: er** fliken.  Välj SKU för att ändra.
1. Klicka på den **Välj länder** knappen den **Land/Region tillgänglighet** fält.
1. I region tillgänglighet dialogrutan Lägg till eller ta bort regionerna för denna SKU.

AppSource-erbjudanden:

1. Välj den **Storefront information** fliken.
1. Bredvid den **länder/regioner som stöds** etikett, klickar du på **länder/regioner som stöds**. 
1. I dialogrutan för länder/regioner, Lägg till eller ta bort regioner för det här erbjudandet.

För antingen marketplace:

1. Klicka på **publicera** att starta publiceringsprocessen. 

Om en SKU gjorts tillgängliga i en ny region har du möjlighet att ange priser för en viss regionen via den **exportera priser Data** funktioner. Du kan inte uppdatera dess priser eftersom prisändringar inte tillåts om du lägger till en region tillbaka som tidigare var tillgängligt.


### <a name="add-a-new-sku"></a>Lägg till en ny SKU 

Om du vill göra en ny SKU som är tillgängliga i ett befintligt erbjudande, använder du följande steg:

1. I den **alla erbjudanden** sidan, hitta erbjudandet.
3. Under den **SKU: er** klickar du på **lägga till nya SKU: N** och ger en **SKU ID** i popup-fönstret.
4. Följ resten av stegen som beskrivs i [publicera ett erbjudande för virtuell dator](../virtual-machine/cpp-publish-offer.md).
5. Klicka på **publicera** att starta publiceringsprocessen.


### <a name="update-offer-marketplace-assets"></a>Uppdatera erbjudandet marketplace tillgångar

Du kan ha scenarier där du behöver uppdatera marketplace textbaserade och bildresurser, sådana din företagslogotyper erbjuder beskrivning osv. Använd följande steg för att uppdatera dessa tillgångar.

1. I den **alla erbjudanden** sidan, hitta ditt erbjudande. 
2. Välj den **Marketplace** fliken och följ instruktionerna i ditt erbjudande *Marketplace fliken* avsnittet.
3. Klicka på **publicera** att starta publiceringsprocessen.


### <a name="update-pricing-on-published-offers"></a>Uppdatera prissättning på publicerade erbjudanden

När din betalning har publicerats kan du öka priset för en befintlig SKU.  I stället skapa en SKU under samma erbjudandet, ta bort den gamla SKU: N och sedan publicera ditt erbjudande. Du kan minska priset på tidigare publicerade erbjudanden. Att minska ditt erbjudande pris:

1. Välj den SKU som du vill minska priser.
2. Du måste ange det lägre priset med samma metod som du ursprungligen använde: antingen direkt i portalens användargränssnitt eller med import/export-kalkylblad.
3. Klicka på **Spara**.
4. Klicka på **publicera** att starta publiceringsprocessen.

Priserna är synlig för nya kunder när den är aktiv på marketplace och alla nya kunder tjänar sedan det nya sämre priset.  Priset minskningen återspeglas retroaktivt för befintliga kunder i början av den faktureringsperiod då priset minskningen trädde i kraft.  Om de redan har faktureras för cykeln då en minskning av priset inträffat, får de en återbetalning under deras nästa faktureringsperiod att täcka sämre priset.


## <a name="compare-feature"></a>Jämför funktioner

När du gör ändringar på ett erbjudande för publicerade, kan du använda den *jämför* funktionen för att granska ändringarna. Kan använda den här funktionen:

1. När som helst redigeringsprocessen, kan du klicka på den **jämför** knappen i den **redigeraren** fliken för ditt erbjudande.
2. En jämförelse av fönstret visar sida-vid-sida-versioner av sparade ändringar till det här erbjudandet från marketplace-erbjudande. 

![Jämför erbjudande-knappen i flik för redigerare](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historik för publiceringen av åtgärder

Om du vill visa historiska publiceringsaktivitet, Välj den **historik** flik i den vänstra lodräta menyraden i partnerportalen i molnet.  Sidan tidigare ger flexibla filtrerar efter flera egenskaper och har stöd för kolumnordning.  Varje publicering är tidsstämplad.  Mer information finns i [granska historiksidan](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Nästa steg

Du kan också använda partnerportalen i molnet till [ta bort en publicerade SKU eller erbjuda](./cpp-delete-offer.md).
