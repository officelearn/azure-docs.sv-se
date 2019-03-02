---
title: Uppdatera en befintlig virtuell dator erbjuder på Azure Marketplace | Microsoft Docs
description: Beskriver hur du uppdaterar en befintlig virtuell dator-erbjudande på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: Ankit.Sud
ms.openlocfilehash: ee828c908cb3b103befa3890b43bb2c1f04f7cf0
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214052"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Uppdatera en befintlig VM-erbjudandet på Azure Marketplace

Den här artikeln vägleder dig genom de olika delarna för att uppdatera ditt erbjudande för virtuell dator (VM) i den [Cloud Partner Portal](https://cloudpartner.azure.com/) och sedan publicera erbjudandet. 

Det finns ett antal vanliga orsaker till att du kan uppdatera ditt erbjudande, inklusive:

-  Lägg till en ny version av VM-avbildning till befintliga SKU: er
-  Ändra regioner en SKU är tillgänglig
-  Lägg till nya SKU: er
-  Uppdatera marketplace-metadata för erbjudandet eller enskilda SKU: er
-  Uppdatera priser på användningsbaserad betalning

För att hjälpa dig på de här ändringarna, portalen erbjuder den **jämför** och **historik** funktioner.  

>[!Note]
>Cloud Solution Providers (CSP) partner channel anmälan är nu tillgänglig.  Se [Cloud Solution Providers](../../cloud-solution-providers.md) mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner kanaler.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Unpermitted ändringar i erbjudande för virtuell dator eller SKU

Det finns vissa attribut för ett erbjudande för virtuell dator eller en SKU som inte kan ändras när erbjudandet är live i Azure Marketplace, främst:

-  **ID för erbjudande** och **Publicerings-ID** i erbjudandet
-  **SKU-ID** befintliga SKU: er
-  Antal befintliga SKU: er som datadisk
-  Fakturerings-/ licensmodell ändras till befintliga SKU: er
-  Tillägg till en publicerad SKU


## <a name="common-update-operations"></a>Vanliga åtgärder för uppdatering

Även om det finns en mängd olika egenskaper som du kan ändra på ett erbjudande för virtuell dator, är följande åtgärder vanliga.

### <a name="update-the-vm-image-version-for-a-sku"></a>Uppdatera versionen av VM-avbildning för en SKU

Det är vanligt att en VM-avbildning uppdateras regelbundet med säkerhetskorrigeringar, extrafunktioner och så vidare.  I sådana fall som du vill uppdatera den Virtuella datoravbildningen som din SKU som refererar till med hjälp av följande steg:

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**, hitta erbjudandet att uppdatera.

3.  I den **SKU: er** fliken, klickar du på den SKU som är associerade med den Virtuella datoravbildningen att uppdatera.

4.  Under **Disk version**, klicka på **+ ny Disk Version** att lägga till en ny VM-avbildning.

5.  Ange de nya VM-avbildningarna **Disk version**. Disk-version måste följa den [sémantickou verzi](http://semver.org/) format. Versionerna bör vara i formatet X.Y.Z, där X, Y och Z är heltal. Kontrollera att den nya versionen som du anger är större än alla tidigare versioner. Annars när du publicerar visas den nya versionen inte i portalen eller Azure Marketplace.

6.  För **OS VHD URL**, ange den [signatur för delad åtkomst (SAS) URI](./cpp-get-sas-uri.md) skapade för det virtuella Hårddisken för operativsystemet. 

    > [!WARNING] 
    > Datadiskar kan inte ändra mellan olika versioner av SKU: N. Om tidigare versioner hade datadiskar som är konfigurerad, måste den nya versionen också ha samma antal datadiskar.

7.  Klicka på **publicera** att starta arbetsflödet för att publicera din nya VM-versionen på Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Ändra regiontillgänglighet för en SKU

Med tiden kan du göra ditt erbjudande/SKU tillgängligt i fler regioner.  Alternativt kan du stoppa stöder erbjudande/SKU i en viss region.
Om du vill ändra tillgänglighet, använder du följande steg:

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden** erbjudandet som du vill uppdatera.

3.  I den **SKU: er** fliken, klickar du på den SKU som du vill ändra dess tillgänglighet.

4.  Klicka på den **Välj länder** knappen den **Land/Region tillgänglighet** fält.

5.  I popup-regiontillgänglighet, lägga till eller ta bort regionerna för denna SKU.

6.  Klicka på **publicera** att starta arbetsflödet publicera för att uppdatera din regiontillgänglighet för SKU: er.

Om en SKU som görs tillgänglig i en ny region, får du möjligheten att ange priser för en viss regionen via den **exportera priser Data** funktioner. Om du lägger till en region tillbaka som tidigare när tillgänglig innan kan du inte uppdatera dess priser eftersom ändringar i prissättning inte är tillåtna.


### <a name="add-a-new-sku"></a>Lägg till en ny SKU

Använd följande steg för att göra en ny SKU som är tillgängliga för ditt befintliga erbjudande: 

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden** erbjudandet som du vill uppdatera.

3.  Under den **SKU: er** fliken, klickar du på **lägga till nya SKU: N** och ger en **SKU ID** i popup-fönstret.

4.  Publicera den virtuella datorn enligt anvisningarna i artikeln [publicera en virtuell dator på Azure Marketplace](./cpp-publish-offer.md).

5.  Klicka på **publicera** att starta arbetsflödet för att publicera din nya SKU: N.


### <a name="update-offer-marketplace-metadata"></a>Metadata för marketplace-erbjudande

Använd följande steg för att uppdatera metadata för marketplace, företagsnamn, logotyper, etc., som är associerade med ditt erbjudande: 

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden** erbjudandet som du vill uppdatera.

3.  Gå till den **Marketplace** fliken och följer instruktionerna i artikeln [publicera en virtuell dator på Azure Marketplace](./cpp-publish-offer.md) att göra ändringar i metadata.

4.  Klicka på **publicera** att starta arbetsflödet för att publicera dina ändringar.


### <a name="update-pricing-on-published-offers"></a>Uppdatera prissättning på publicerade erbjudanden

När din betalning har publicerats kan inte direkt öka priser för SKU.  (Men du kan skapa en ny SKU under samma erbjudandet, ta bort den gamla SKU: N och sedan publicera ditt erbjudande för nya kunder.)  Däremot kan du minska priset för en publicerade erbjudande med följande steg:

1.  Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**, hitta erbjudandet att uppdatera.

3.  Klicka på den SKU som du vill minska priser.

4.  Om du har angett baseras på 1 x 1 grafiska Användargränssnittet, kan du ändra priset direkt i Användargränssnittet. Om du ställer in priser via import/export-kalkylblad, kan du endast minska priserna via import/export-funktionen.

3.  Klicka på **Spara**.

4.  Klicka på **publicera** att starta arbetsflödet för att publicera dina ändringar.

De nya sämre priserna blir synliga för nya kunder när den är aktiv på webbplatsen.  Den här nya priset påverkar dina kunder på följande sätt:

- Nya kunder kommer att debiteras den här nya priserna. 
- För befintliga kunder återspeglas pris minskningen retroaktivt i början av den faktureringsperiod då priset minskningen trädde i kraft.
Om de redan har faktureras för cykeln då en minskning av priset inträffat, får de en återbetalning under deras nästa faktureringsperiod att täcka sämre priset.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Förenklad valuta priser

Från och med September 1 2018, ett nytt avsnitt kallas **förenklad valuta priser** kommer att läggas till på portalen. Microsoft effektivisera verksamheten Azure Marketplace genom att aktivera mer förutsägbara priser och samlingar från dina kunder över hela världen. Den här effektivisera omfattar att minska antalet valutor där vi fakturerar dina kunder.

Nytt avsnitt tar priser i dessa nya valutor. När alla kunder har migrerats till dessa nya avräkning valutor, ursprungliga prissättningsavsnittet kommer att dras tillbaka och bara avsnittet förenklad valuta priser finns kvar.

Du får förrän den 1 November 2018 att ange ett nytt pris för regionerna där avräkning valuta ändras. Du kommer inte att kunna öka priset för regioner där avräkning valuta inte ändras.

> [!NOTE] 
> Om du använder API: er för att publicera ditt erbjudande, kan du se ett nytt avsnitt i den JSON som erbjuder. Detta skulle kommenteras som `virtualMachinePricingV2` eller `monthlyPricingV2`, beroende på vilken typ av erbjudande. 

Om du har frågor om den här ändringen kan kontakta [Support för Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Jämför funktioner

När du gör ändringar på ett redan publicerat erbjudande kan du utnyttja den **jämför** funktionen för att granska de ändringar som har gjorts. Använda den här funktionen:

1.  När som helst i redigeringsprocessen klickar du på den **jämför** knappen för ditt erbjudande.

    ![Jämför funktionen knappen](./media/publishvm_037.png)


2.  Visa sida-vid-sida-versioner av marknadsföring tillgångar och metadata.


## <a name="history-of-publishing-actions"></a>Historik för publiceringen av åtgärder

Om du vill visa alla historiska publiceringsaktivitet, klickar du på den **historik** objekt i den vänstra menyraden i partnerportalen i molnet. Här kommer du att kunna visa tidsstämplad åtgärder som har vidtagits under livslängden för dina Azure Marketplace-erbjudanden.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

