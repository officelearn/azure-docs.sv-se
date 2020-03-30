---
title: Uppdatera ett befintligt vm-erbjudande på Azure Marketplace
description: Förklarar hur du uppdaterar ett befintligt VM-erbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: fe1397d7ad00a2eb6e9fe04d8bfa50e49839fe34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288792"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Uppdatera ett befintligt vm-erbjudande på Azure Marketplace

I den här artikeln får du hjälp med de olika aspekterna av att uppdatera erbjudandet om virtuella datorer (VM) i [Cloud Partner Portal](https://cloudpartner.azure.com/) och sedan publicera erbjudandet igen. 

Det finns ett antal vanliga skäl för dig att uppdatera ditt erbjudande, inklusive:

-  Lägga till en ny vm-avbildningsversion i befintliga SKU:er
-  Ändra regioner som en SKU är tillgänglig
-  Lägg till nya SKU:er
-  Uppdatera marknadsplatsmetadata för erbjudandet eller enskilda SKU:er
-  Uppdatera priser på erbjudanden om användningsbaserad betalning

För att hjälpa dig med dessa ändringar erbjuder portalen funktionerna **Jämför** och **historik.**  

>[!Note]
>Opt-in (Cloud Solution Providers) partnerkanal är nu tillgänglig.  Se [Cloud Solution Providers](../../cloud-solution-providers.md) för mer information om marknadsföring ditt erbjudande via Microsoft CSP partnerkanaler.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Otillåtna ändringar av VM-erbjudande eller SKU

Det finns vissa attribut för ett VM-erbjudande eller en SKU som inte kan ändras när erbjudandet är live på Azure Marketplace, främst:

-  **Erbjudande-ID** och **Utgivar-ID** för erbjudandet
-  **SKU-ID** för befintliga SKU:er
-  Antal datadiskar för befintliga SKU:er
-  Ändringar av fakturerings-/licensmodell till befintliga SKU:er
-  Prishöjningar till en publicerad SKU


## <a name="common-update-operations"></a>Vanliga uppdateringsåtgärder

Även om det finns ett brett utbud av egenskaper som du kan ändra på ett vm-erbjudande, är följande åtgärder vanliga.

### <a name="update-the-vm-image-version-for-a-sku"></a>Uppdatera vm-avbildningsversionen för en SKU

Det är vanligt att en VM-avbildning uppdateras regelbundet med säkerhetskorrigeringar, ytterligare funktioner och så vidare.  Under sådana scenarier vill du uppdatera den VM-avbildning som SKU refererar till med hjälp av följande steg:

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).

2.  Under **Alla erbjudanden**hittar du erbjudandet att uppdatera.

3.  På fliken **SKU:er** klickar du på den SKU som är associerad med vm-avbildningen för att uppdatera.

4.  Under **Diskversion**klickar du på **+Ny diskversion** för att lägga till en ny vm-avbildning.

5.  Ange den nya **VM-avbildningsdiskversionen**. Diskversionen måste följa det [semantiska](https://semver.org/) versionsformatet. Versioner ska vara av formuläret X.Y.Z, där X, Y och Z är heltal. Kontrollera att den nya versionen som du anger är större än alla tidigare versioner. Annars visas inte den nya versionen i antingen portalen eller Azure Marketplace efter att den nya versionen har publicerats.

6.  För **OS VHD URL**anger du den [SAS-uri (Shared Access Signature) som](./cpp-get-sas-uri.md) skapats för operativsystemets virtuella hårddisk. 

    > [!WARNING] 
    > Antalet datadiskar kan inte ändras mellan olika versioner av SKU. Om tidigare versioner hade konfigurerat datadiskar måste den här nya versionen också ha samma antal datadiskar.

7.  Klicka på **Publicera** för att starta arbetsflödet för att publicera din nya VM-version på Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Ändra regiontillgänglighet för en SKU

Med tiden kanske du vill göra ditt erbjudande/SKU tillgängligt i fler regioner.  Alternativt kanske du vill sluta stödja erbjudandet/SKU i en viss region.
Så här ändrar du tillgänglighet:

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).

2.  Under **Alla erbjudanden** hittar du det erbjudande du vill uppdatera.

3.  Klicka på den SKU som du vill ändra tillgängligheten på fliken **SKU.**

4.  Klicka på knappen **Välj länder** under tillgänglighetsfältet **Land/region.**

5.  I popup-fönster för regiontillgänglighet lägger du till eller tar bort regioner för den här SKU:n.

6.  Klicka på **Publicera** om du vill starta publiceringsarbetsflödet för att uppdatera tillgängligheten för SKU-regionen.

Om en SKU görs tillgänglig i en ny region kan du ange priser för just den regionen via funktionen **Exportera prisdata.** Om du lägger till en region tillbaka som en gång var tillgänglig tidigare, kommer du inte att kunna uppdatera dess prissättning eftersom prisändringar inte är tillåtna.


### <a name="add-a-new-sku"></a>Lägga till en ny SKU

Gör en ny SKU tillgänglig för ditt befintliga erbjudande: 

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).

2.  Under **Alla erbjudanden** hittar du det erbjudande du vill uppdatera.

3.  Under fliken **SKU:er** klickar du på **Lägg till ny SKU** och anger ett **SKU-ID** i popup-programmet.

4.  Publicera om den virtuella datorn enligt beskrivningen i artikeln [Publicera en virtuell dator på Azure Marketplace](./cpp-publish-offer.md).

5.  Klicka på **Publicera** för att starta arbetsflödet för att publicera din nya SKU.


### <a name="update-offer-marketplace-metadata"></a>Uppdatera metadata för erbjudandemarknadsplats

Följ följande steg för att uppdatera marketplace-metadata – företagsnamn, logotyper osv.– som är kopplade till erbjudandet: 

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).

2.  Under **Alla erbjudanden** hittar du det erbjudande du vill uppdatera.

3.  Gå till fliken **Marketplace** följ sedan instruktionerna i artikeln [Publicera en virtuell dator till Azure Marketplace](./cpp-publish-offer.md) för att göra metadataändringar.

4.  Klicka på **Publicera** om du vill starta arbetsflödet för att publicera ändringarna.


### <a name="update-pricing-on-published-offers"></a>Uppdatera priser på publicerade erbjudanden

När ditt pay-as-you-go-erbjudande har publicerats kan du inte direkt öka SKU-prissättningen.  (Du kan dock skapa en ny SKU under samma erbjudande, ta bort den gamla SKU:n och sedan publicera om erbjudandet för nya kunder.)  Däremot kan du sänka priset på ett publicerat erbjudande med hjälp av följande steg:

1.  Logga in på [Molnpartnerportalen](https://cloudpartner.azure.com/).

2.  Under **Alla erbjudanden**hittar du erbjudandet att uppdatera.

3.  Klicka på den SKU som du vill minska priserna för.

4.  Om du har angett prissättningen i 1x1 GUI kan du ändra priset direkt i användargränssnittet. Om du anger priser via import/exportkalkylblad kan du bara sänka priserna via import-/exportfunktionen.

3.  Klicka på **Spara**.

4.  Klicka på **Publicera** om du vill starta arbetsflödet för att publicera ändringarna.

Den nya minskade prissättningen kommer att vara synlig för nya kunder när den är live på webbplatsen.  Det här nya priset påverkar dina kunder på följande sätt:

- Nya kunder kommer att debiteras denna nya kurs. 
- För befintliga kunder kommer prissänkningen att återspeglas retroaktivt till början av faktureringscykeln under vilken prissänkningen trädde i kraft.
Om de redan har fakturerats för den cykel under vilken en prissänkning inträffade, kommer de att få en återbetalning under nästa faktureringsperiod för att täcka det minskade priset.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Förenklad valutaprissättning

Från och med den 1 september 2018 läggs ett nytt avsnitt med namnet **Förenklad valutaprissättning** till i portalen. Microsoft effektiviserar Azure Marketplace-verksamheten genom att möjliggöra mer förutsägbara priser och samlingar från dina kunder över hela världen. Denna rationalisering kommer att omfatta att minska antalet valutor där vi fakturerar dina kunder.

Det nya avsnittet kommer att ta prissättning i dessa nya valutor.När alla kunder har migrerats till dessa nya kvittningsvalutor dras det ursprungliga prisavsnittet tillbaka och endast avsnittet Förenklad valutaprissättning kommer att finnas kvar.

Du har fram till den 1 november 2018 på dig att ange ett nytt pris för de regioner där kvittningsvalutan förändras. Du kommer inte att kunna höja priset för regioner där kvittningsvalutan inte ändras.

> [!NOTE] 
> Om du använder API:er för att publicera ditt erbjudande kan du se ett nytt avsnitt i Erbjudande-JSON. Detta skulle kommenteras `virtualMachinePricingV2` som `monthlyPricingV2`eller , beroende på vilken typ av erbjudande. 

Om du har några frågor om den här ändringen kontaktar du [Azure Marketplace Support](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Jämför funktion

När du gör ändringar i ett redan publicerat erbjudande kan du använda funktionen **Jämför** för att granska de ändringar som har gjorts. Gör så här för att använda funktionen:

1.  När som helst i redigeringsprocessen klickar du på **knappen Jämför** för ditt erbjudande.

    ![Knappen Jämför funktion](./media/publishvm_037.png)


2.  Visa versioner av marknadsföringsresurser och metadata sida vid sida.


## <a name="history-of-publishing-actions"></a>Historia av publiceringsåtgärder

Om du vill visa en historisk publiceringsaktivitet klickar du på **historikobjektet** i det vänstra navigeringsmenyfältet i Cloud Partner Portal. Här kan du visa tidsstämplade åtgärder som har vidtagits under livstiden för dina Azure Marketplace-erbjudanden.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

