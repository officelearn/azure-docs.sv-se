---
title: Uppdatera marknadsplatserbjudanden | Azure Marketplace
description: Uppdatera erbjudanden på Azure- och AppSource Marketplaces med cloud partnerportalen
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288520"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Uppdatera Azure Marketplace- och AppSource-erbjudanden

Det finns olika typer av uppdateringar som du kan använda för ditt erbjudande när det har publicerats.  [Cloud Partner Portal](https://cloudpartner.azure.com/) hjälper dig att korrekt ändra attribut för ett erbjudande, inklusive:

-  Lägga till ny avbildning eller paketversion för virtuella datorer (VM) i en befintlig SKU
-  Ändra regioner som en SKU är tillgänglig i
-  Lägga till nya SKU:er
-  Uppdatera marketplace-metadata för erbjudanden eller SKU:er 
-  Uppdatera priser på erbjudanden om användningsbaserad betalning

Portalen har också funktioner, till exempel möjligheten att jämföra funktioner och visa en historik över funktioner för ett erbjudande, som hjälper dig att hantera ändringar.  När du har ändrat ett erbjudande eller SKU måste det publiceras på nytt innan ändringarna går "live".  I den här artikeln får du hjälp med de olika aspekterna av att uppdatera ditt marketplace-erbjudande.

## <a name="unpermitted-changes-to-an-offersku"></a>Otillåtna ändringar i ett erbjudande/SKU

Det finns vissa attribut för ett erbjudande eller en SKU som inte kan ändras när det har publicerats på marknadsplatsen.  Motsvarande fält är inaktiverade på fliken **Redigerare** i portalen, till exempel:  

- Erbjudande-ID och Publisher-ID
- SKU-ID 
- Antal datadiskar för befintliga SKU:er
- Ändringar av fakturerings-/licensmodell för befintliga SKU:er
- Versionstaggar, till exempel:`1.0.1`


## <a name="common-update-operations"></a>Vanliga uppdateringsåtgärder

I följande avsnitt beskrivs hur du utför några av de mest uppdateringsåtgärderna.  Dessa åtgärder är inte tillgängliga för alla erbjudandetyper.  Du måste logga in på Cloud Partner Portal för att starta någon av dessa åtgärder.


### <a name="update-offer-contacts"></a>Uppdatera erbjudandekontakter

Följ följande steg för att uppdatera supportkontakterna för ditt erbjudande.
1. Välj erbjudandet på sidan **Alla erbjudanden.**
2. Välj fliken **Kontakter.**
3. Välj knappen **Spara**.
4. Välj **Publicera** om du vill starta publiceringsprocessen.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Ändra regioner ett erbjudande eller SKU är tillgängligt i

Med tiden kanske du vill göra ditt erbjudande/SKU tillgängligt i fler regioner.
Alternativt kanske du vill sluta stödja erbjudandet/SKU i en viss region.
Följ följande steg för att implementera dessa ändringar.

1. På sidan **Alla erbjudanden** hittar du det erbjudande du vill uppdatera.

För Azure Marketplace-erbjudanden:

1. Välj fliken **SKU:er.**  Välj den SKU som ska ändras.
1. Klicka på knappen **Välj länder** under tillgänglighetsfältet **Land/Region.**
1. Lägg till eller ta bort regioner för den här SKU:n i dialogrutan Regiontillgänglighet.

För AppSource-erbjudanden:

1. Välj fliken **Information om skyltfönster.**
1. Klicka på **Länder/regioner** **som stöds.** 
1. I dialogrutan länder/regioner som stöds lägger du till eller tar bort regionerna för det här erbjudandet.

För antingen marknadsplats:

1. Klicka på **Publicera** om du vill starta publiceringsprocessen. 

Om en SKU görs tillgänglig i en ny region kan du ange priser för just den regionen via funktionen **Exportera prisdata.** Om du lägger till en region tillbaka som tidigare var tillgänglig, kan du inte uppdatera dess prissättning eftersom prisändringar inte är tillåtna.


### <a name="add-a-new-sku"></a>Lägga till en ny SKU 

Så här gör du en ny SKU tillgänglig för ett befintligt erbjudande:

1. På sidan **Alla erbjudanden** hittar du erbjudandet.
3. Klicka på **Lägg till ny SKU** under formuläret **SKU** och ange ett **SKU-ID** i popup-programmet.
4. Följ resten av stegen som beskrivs i [Publicera ett erbjudande om virtuell dator](../virtual-machine/cpp-publish-offer.md).
5. Klicka på **Publicera** om du vill starta publiceringsprocessen.


### <a name="update-offer-marketplace-assets"></a>Uppdatera marknadsplatstillgångar för erbjudanden

Du kan ha scenarier där du behöver uppdatera marknadsplats text-baserade och bildtillgångar, såsom ditt företag logotyper, erbjudande beskrivning, etc. Följ följande steg för att uppdatera dessa tillgångar.

1. På sidan **Alla erbjudanden** hittar du ditt erbjudande. 
2. Välj fliken **Marketplace** och följ instruktionerna i ditt erbjudandes *Marketplace-flikämne.*
3. Klicka på **Publicera** om du vill starta publiceringsprocessen.


### <a name="update-pricing-on-published-offers"></a>Uppdatera priser på publicerade erbjudanden

När ditt pay-as-you-go-erbjudande har publicerats kan du inte höja priset på en befintlig SKU.  Skapa i stället en SKU under samma erbjudande, ta bort den gamla SKU:n och publicera sedan erbjudandet igen. Du kan sänka priset på tidigare publicerade erbjudanden. Så här minskar du ditt erbjudandepris:

1. Välj den SKU som du vill minska priserna för.
2. Du måste ange det lägre priset med samma mekanism som du ursprungligen använde: antingen direkt i portalgränssnittet eller med import-/exportkalkylbladet.
3. Klicka på **Spara**.
4. Klicka på **Publicera** om du vill starta publiceringsprocessen.

Prissättningen är synlig för nya kunder när den är live på marknaden, och alla nya kunder kommer då att betala det nya sänkta priset.  För befintliga kunder återspeglas prissänkningen retroaktivt i början av faktureringscykeln under vilken prissänkningen trädde i kraft.  Om de redan har fakturerats för den cykel under vilken en prissänkning inträffade, kommer de att få en återbetalning under nästa faktureringsperiod för att täcka det minskade priset.


## <a name="compare-feature"></a>Jämför funktion

När du gör ändringar i ett publicerat erbjudande kan du använda funktionen *Jämför* för att granska ändringarna. Så här använder du den här funktionen:

1. När som helst i redigeringsprocessen kan du klicka på knappen **Jämför** på fliken **Redigerare** för ditt erbjudande.
2. Ett jämförelsefönster visar versioner sida vid sida av de sparade ändringarna i det här erbjudandet jämfört med marketplace-erbjudandet. 

![Knappen Jämför erbjudande på fliken Redigerare](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historia av publicerande handlingar

Om du vill visa historisk publiceringsaktivitet väljer du fliken **Historik** i det vänstra lodräta menyraden i Cloud Partner Portal.  Sidan Historik ger flexibel filtrering med flera egenskaper och stöder kolumnordning.  Varje publiceringshändelse är tidsstämplad.  Mer information finns på [sidan Granskningshistorik](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Nästa steg

Du kan också använda Cloud Partner Portal för att [ta bort en publicerad SKU eller ett erbjudande](./cpp-delete-offer.md).
