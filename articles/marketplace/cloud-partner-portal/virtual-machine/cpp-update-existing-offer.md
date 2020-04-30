---
title: Uppdatera ett befintligt VM-erbjudande på Azure Marketplace
description: Förklarar hur du uppdaterar ett befintligt VM-erbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 934be24d07c01c76c8caf5e16af4b765df79c964
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142923"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Uppdatera ett befintligt VM-erbjudande på Azure Marketplace

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett erbjudande för virtuell Azure-dator](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) för att hantera dina migrerade erbjudanden.

Den här artikeln vägleder dig genom de olika aspekterna av att uppdatera ditt virtuella dator erbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com/) och sedan publicera om erbjudandet.

Det finns ett antal vanliga-orsaker för att du ska kunna uppdatera erbjudandet, inklusive:

-  Lägg till en ny version av VM-avbildningen i befintliga SKU: er
-  Ändra regioner en SKU är tillgänglig
-  Lägg till nya SKU: er
-  Uppdatera Marketplace-metadata för erbjudandet eller enskilda SKU: er
-  Uppdatera priserna för betala per användning-erbjudandet

För att hjälpa dig i dessa ändringar erbjuder portalen funktionerna **Jämför** och **Historik** .  

>[!Note]
>Partner kanal för Cloud solution providers (CSP) är nu tillgängligt.  Se [leverantörer av moln lösningar](../../cloud-solution-providers.md) för mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner-kanaler.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Otillåtna ändringar i VM-erbjudandet eller SKU

Det finns vissa attribut för ett virtuellt dator erbjudande eller SKU som inte kan ändras när erbjudandet är aktivt på Azure Marketplace, främst:

-  Erbjudandets **ID** och **utgivar-ID**
-  **SKU-ID** för befintliga SKU: er
-  Antal data diskar för befintliga SKU: er
-  Ändringar i fakturering/licens modell för befintliga SKU: er
-  Priset ökar med en publicerad SKU


## <a name="common-update-operations"></a>Vanliga uppdaterings åtgärder

Även om det finns många olika egenskaper som du kan ändra på ett virtuellt dator erbjudande är följande åtgärder vanliga.

### <a name="update-the-vm-image-version-for-a-sku"></a>Uppdatera VM-avbildningens version för en SKU

Det är vanligt att en VM-avbildning uppdateras regelbundet med säkerhets korrigeringar, ytterligare funktioner och så vidare.  Under sådana scenarier vill du uppdatera den virtuella dator avbildningen som din SKU refererar till med hjälp av följande steg:

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**hittar du erbjudandet att uppdatera.

3.  På fliken **SKU** klickar du på den SKU som är kopplad till den virtuella dator avbildningen som ska uppdateras.

4.  Under **disk version**klickar du på **+ ny disk version** för att lägga till en ny VM-avbildning.

5.  Ange den nya **disk versionen**för VM-avbildningar. Disk versionen måste följa det [semantiska versions](https://semver.org/) formatet. Versionerna ska vara av formatet X. Y. Z, där X, Y och Z är heltal. Kontrol lera att den nya version som du anger är större än alla tidigare versioner. annars visas inte den nya versionen i portalen eller på Azure Marketplace.

6.  För **OS VHD-URL**anger du URL: en för [signaturen för delad åtkomst (SAS)](./cpp-get-sas-uri.md) som skapats för operativ systemets virtuella hård disk. 

    > [!WARNING] 
    > Antalet data diskar kan inte ändras mellan olika versioner av SKU: n. Om tidigare versioner hade data diskar konfigurerade måste den här nya versionen också ha samma antal data diskar.

7.  Klicka på **publicera** för att starta arbets flödet för att publicera den nya VM-versionen på Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Ändra regions tillgänglighet för en SKU

Med tiden kanske du vill göra ditt erbjudande/SKU tillgängligt i fler regioner.  Alternativt kanske du vill sluta stödja Erbjudandet/SKU: n i en specifik region.
Använd följande steg för att ändra tillgänglighet:

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden** hittar du det erbjudande som du vill uppdatera.

3.  På fliken **SKU** klickar du på den SKU som du vill ändra dess tillgänglighet.

4.  Klicka på knappen **Välj länder** under **tillgänglighets fältet land/region** .

5.  I popup-fönstret region tillgänglighet lägger du till eller tar bort regionerna för denna SKU.

6.  Klicka på **publicera** för att starta publicerings arbets flödet för att uppdatera SKU-regionens tillgänglighet.

Om en SKU görs tillgänglig i en ny region kan du ange prissättning för just den regionen via funktionen **Exportera pris data** . Om du lägger till en region tillbaka som var tillgänglig tidigare, kommer du inte att kunna uppdatera dess priser eftersom pris ändringar inte är tillåtna.


### <a name="add-a-new-sku"></a>Lägg till en ny SKU

Använd följande steg för att göra en ny SKU tillgänglig för ditt befintliga erbjudande: 

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden** hittar du det erbjudande som du vill uppdatera.

3.  Under fliken **SKU** klickar du på **Lägg till ny SKU** och anger ett **SKU-ID** i popup-fönstret.

4.  Publicera om den virtuella datorn enligt beskrivningen i artikeln [publicera en virtuell dator på Azure Marketplace](./cpp-publish-offer.md).

5.  Klicka på **publicera** för att starta arbets flödet för att publicera den nya SKU: n.


### <a name="update-offer-marketplace-metadata"></a>Uppdatera metadata för Marketplace för erbjudande

Använd följande steg för att uppdatera Marketplace-metadata, företags namn, logo typer, etc. – som är associerade med ditt erbjudande: 

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden** hittar du det erbjudande som du vill uppdatera.

3.  Gå till **Marketplace** -fliken och följ sedan anvisningarna i artikeln [publicera en virtuell dator på Azure Marketplace](./cpp-publish-offer.md) för att göra ändringar i metadata.

4.  Klicka på **publicera** för att starta arbets flödet för att publicera ändringarna.


### <a name="update-pricing-on-published-offers"></a>Uppdatera prissättningen på publicerade erbjudanden

När ditt erbjudande för att betala per användning har publicerats kan du inte öka SKU-priset direkt.  (Du kan dock skapa en ny SKU under samma erbjudande, ta bort den gamla SKU: n och sedan publicera om ditt erbjudande för nya kunder.)  Du kan däremot minska priset för ett publicerat erbjudande med följande steg:

1.  Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Under **alla erbjudanden**hittar du erbjudandet att uppdatera.

3.  Klicka på den SKU som du vill minska priset för.

4.  Om du har angett prissättningen i 1x1-ANVÄNDARGRÄNSSNITTET kan du ändra priset direkt i användar gränssnittet. Om du ställer in prissättning via import/export-kalkylblad kan du bara minska priserna via import/export-funktionen.

3.  Klicka på **Spara**.

4.  Klicka på **publicera** för att starta arbets flödet för att publicera ändringarna.

Den nya minskade prissättningen visas för nya kunder när det är aktivt på webbplatsen.  Det nya priset kommer att påverka dina kunder på följande sätt:

- Nya kunder debiteras det nya priset. 
- För befintliga kunder kommer pris minskningen att avspeglas retroaktivt i början av den fakturerings period under vilken prisminskningen blev giltig.
Om de redan har fakturerats för den cykel under vilken en prissänkning har skett, får de ett bidrag under nästa fakturerings period för att ta upp det minskade priset.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Pris för förenklad valuta

Från och med den 1 2018 september kommer ett nytt avsnitt med namnet **förenklad valuta prissättning** att läggas till i portalen. Microsoft effektiviserar Azure Marketplace-verksamheten genom att möjliggöra mer förutsägbara priser och samlingar från dina kunder över hela världen. Den här effektiviserat omfattar att minska antalet valutor som vi fakturerar kunderna i.

Det nya avsnittet tar pris i dessa nya valutor.När alla kunder har migrerats till dessa nya kvittnings valutor kommer det ursprungliga pris avsnittet att dras tillbaka och bara pris avsnittet för förenklad valuta kommer att finnas kvar.

Du kommer att ha fram till den 1 november 2018 för att ange ett nytt pris för regionerna där värdet för kvittnings valutan ändras. Du kommer inte att kunna öka priset för regioner där-kvittnings valutan inte ändras.

> [!NOTE] 
> Om du använder API: er för att publicera ditt erbjudande kan du se ett nytt avsnitt i erbjudandet JSON. Detta skulle kommenteras som `virtualMachinePricingV2` eller `monthlyPricingV2`, beroende på typen av erbjudande. 

Om du har några frågor om den här ändringen kan du kontakta [supporten för Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Jämför funktion

När du gör ändringar i ett redan publicerat erbjudande kan du utnyttja funktionen **Jämför** för att granska de ändringar som har gjorts. Gör så här för att använda funktionen:

1.  Klicka på knappen **Jämför** för ditt erbjudande när som helst i redigerings processen.

    ![Knappen Jämför funktion](./media/publishvm_037.png)


2.  Visa sida-vid-sida-versioner av marknadsförings till gångar och metadata.


## <a name="history-of-publishing-actions"></a>Historik för publicerings åtgärder

Om du vill visa en historisk publicerings aktivitet klickar du på objektet **Historik** i det vänstra navigerings fönstret för Cloud Partner Portal. Här kommer du att kunna se tidsstämplade åtgärder som har vidtagits under Azure Marketplace-erbjudandena.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

