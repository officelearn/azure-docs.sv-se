---
title: Uppdatering av Marketplace-erbjudanden | Azure Marketplace
description: Uppdaterings erbjudanden på Azure-och AppSource-Marketplace med hjälp av Cloud Partner Portal
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288520"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Uppdatera Azure Marketplace-och AppSource-erbjudanden

Det finns olika typer av uppdateringar som du kan använda för ditt erbjudande när det har publicerats.  [Cloud Partner Portal](https://cloudpartner.azure.com/) hjälper dig att ändra attribut för ett erbjudande på rätt sätt, inklusive:

-  Lägga till ny avbildning av virtuell dator (VM) eller paket version till en befintlig SKU
-  Ändra regioner en SKU är tillgänglig i
-  Lägga till nya SKU: er
-  Uppdatera Marketplace-metadata för erbjudanden eller SKU: er 
-  Uppdatering av priser för betala per användning-erbjudanden

Portalen har också funktioner, till exempel möjlighet att jämföra funktioner och visa en historik över funktioner för ett erbjudande, som hjälper dig att hantera ändringar.  När du har ändrat ett erbjudande eller en SKU måste det publiceras igen innan ändringarna går in i Live.  Den här artikeln vägleder dig genom de olika aspekterna av att uppdatera Marketplace-erbjudandet.

## <a name="unpermitted-changes-to-an-offersku"></a>Otillåtna ändringar i ett erbjudande/SKU

Det finns vissa attribut för ett erbjudande eller SKU som inte kan ändras när det har publicerats på Marketplace.  Motsvarande fält är inaktiverade på fliken **redigerare** i portalen, till exempel:  

- ID för erbjudande och utgivarens ID
- SKU-ID 
- Antal data diskar för befintliga SKU: er
- Ändringar av befintliga SKU: er för fakturering/licens modell
- Versions taggar, till exempel:`1.0.1`


## <a name="common-update-operations"></a>Vanliga uppdaterings åtgärder

I följande avsnitt beskrivs hur du utför några av de flesta uppdaterings åtgärder.  Dessa åtgärder är inte tillgängliga för alla erbjudande typer.  Du måste logga in på Cloud Partner Portal för att starta någon av dessa åtgärder.


### <a name="update-offer-contacts"></a>Uppdatera erbjudande kontakter

Använd följande steg för att uppdatera support kontakterna för ditt erbjudande.
1. På sidan **alla erbjudanden** väljer du erbjudandet.
2. Välj fliken **kontakter** . uppdatera dina kontakter.
3. Välj knappen **Spara**.
4. Välj **publicera** för att starta publicerings processen.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Ändra regioner ett erbjudande eller en SKU är tillgänglig i

Med tiden kanske du vill göra ditt erbjudande/SKU tillgängligt i fler regioner.
Alternativt kanske du vill sluta stödja Erbjudandet/SKU: n i en specifik region.
Följ stegen nedan om du vill implementera ändringarna.

1. På sidan **alla erbjudanden** hittar du det erbjudande som du vill uppdatera.

För Azure Marketplace-erbjudanden:

1. Välj fliken **SKU: er** .  Välj den SKU som ska ändras.
1. Klicka på knappen **Välj länder** under **tillgänglighets fältet land/region** .
1. I dialog rutan region tillgänglighet lägger du till eller tar bort regionerna för denna SKU.

För AppSource-erbjudanden:

1. Välj fliken **butik information** .
1. Bredvid etiketten **länder/regioner som stöds** klickar du på **länder/regioner som stöds**. 
1. Lägg till eller ta bort regioner för det här erbjudandet i dialog rutan länder/regioner som stöds.

För antingen Marketplace:

1. Klicka på **publicera** för att starta publicerings processen. 

Om en SKU görs tillgänglig i en ny region kan du ange prissättning för den specifika regionen via funktionen **Exportera pris data** . Om du lägger till en region tillbaka som tidigare var tillgänglig kan du inte uppdatera dess priser eftersom pris ändringar inte är tillåtna.


### <a name="add-a-new-sku"></a>Lägg till en ny SKU 

Använd följande steg för att göra en ny SKU tillgänglig för ett befintligt erbjudande:

1. På sidan **alla erbjudanden** hittar du erbjudandet.
3. Under **SKU** -formuläret klickar du på **Lägg till ny SKU** och anger ett **SKU-ID** i popup-fönstret.
4. Följ resten av stegen som beskrivs i [publicera ett erbjudande för virtuella datorer](../virtual-machine/cpp-publish-offer.md).
5. Klicka på **publicera** för att starta publicerings processen.


### <a name="update-offer-marketplace-assets"></a>Uppdatera erbjud Marketplace till gångar

Du kan ha scenarier där du behöver uppdatera platshållartexten och bildfilerna i Marketplace, t. ex. företags logo typer, erbjudande beskrivning osv. Använd följande steg för att uppdatera dessa till gångar.

1. På sidan **alla erbjudanden** hittar du ditt erbjudande. 
2. Välj fliken **Marketplace** och följ anvisningarna på *fliken Marketplace* för ditt erbjudande.
3. Klicka på **publicera** för att starta publicerings processen.


### <a name="update-pricing-on-published-offers"></a>Uppdatera prissättningen på publicerade erbjudanden

När du har publicerat erbjudandet för betalning per användning kan du inte öka priset på en befintlig SKU.  Skapa i stället en SKU under samma erbjudande, ta bort den gamla SKU: n och publicera sedan om erbjudandet. Du kan minska priset för tidigare publicerade erbjudanden. Så här minskar du priset för erbjudandet:

1. Välj den SKU som du vill minska priset för.
2. Du måste ange det lägre priset med samma mekanism som du ursprungligen använde: antingen direkt i Portal gränssnittet eller med kalkyl bladet importera/exportera.
3. Klicka på **Spara**.
4. Klicka på **publicera** för att starta publicerings processen.

Priset är synligt för nya kunder när det är aktivt på Marketplace, och alla nya kunder betalar det nya minskade priset.  För befintliga kunder återspeglas prisminskningen retroaktivt till början av den fakturerings period under vilken pris minskningen blev giltig.  Om de redan har fakturerats för den cykel under vilken en prissänkning har skett, får de ett bidrag under nästa fakturerings period för att ta upp det minskade priset.


## <a name="compare-feature"></a>Jämför funktion

När du gör ändringar i ett publicerat erbjudande kan du använda funktionen *Jämför* för att granska ändringarna. För att använda den här funktionen:

1. När som helst i redigerings processen kan du klicka på knappen **Jämför** på fliken **redigerare** för ditt erbjudande.
2. Ett jämförelse fönster visar versioner sida vid sida av de sparade ändringarna i det här erbjudandet jämfört med Marketplace-erbjudandet. 

![Knappen Jämför erbjudande på fliken redigerare](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historik för publicerings åtgärder

Om du vill visa historisk publicerings aktivitet väljer du fliken **Historik** i den vänstra lodräta meny raden i Cloud Partner Portal.  Historik sidan ger flexibel filtrering av flera egenskaper och stöder kolumn sortering.  Varje publicerings händelse är tidsstämpel.  Mer information finns på [sidan gransknings historik](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Nästa steg

Du kan också använda Cloud Partner Portal för att [ta bort en publicerad SKU eller ett erbjudande](./cpp-delete-offer.md).
