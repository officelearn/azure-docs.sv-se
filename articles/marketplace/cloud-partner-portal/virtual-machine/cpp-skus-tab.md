---
title: 'VM SKU: er flik i Cloud Partner Portal för Azure Marketplace | Microsoft Docs'
description: 'Beskriver fliken SKU: er som används för att skapa ett erbjudande för virtuell dator på Azure Marketplace.'
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
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
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: e05813297707165d59d1b1ed969b54fb0f433277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844499"
---
# <a name="virtual-machine-skus-tab"></a>Fliken för VM-SKU: er

Den **SKU: er** fliken den **nytt erbjudande** sidan kan du skapa en eller flera SKU: er och koppla dem till din nya erbjudandet.  Olika SKU: er kan skilja en lösning av funktioner, VM-avbildningstyper, dataflöde eller skalbarhet, faktureringsmodellerna eller vissa andra egenskaper.

## <a name="create-a-sku"></a>Skapa en SKU

Inledningsvis är ett nytt erbjudande inte har några associerade SKU: er, så skapar du en genom att klicka på **nya SKU: N**.

![Nya SKU-knappen på fliken nytt erbjudande för virtuella datorer](./media/publishvm_005.png)

<br/>

Den **nya SKU: N** dialogrutan visas.  Anger identifieraren för den nya SKU: N och klickar sedan **OK**. (Se nedan för namnkonventioner för identifierare.)  Den **SKU: er** fliken visar nu fält som är tillgängliga för redigering.    En tillagda asterisk (*) på fältnamnet indikerar att det krävs.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![SKU-flik för i formuläret nytt erbjudande för virtuella datorer](./media/publishvm_006.png)

I följande tabell beskrivs syftet, innehåll, och formatering av de här fälten.

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU Settings*   |  |
| **SKU ID**       | Identifierare för denna SKU.  Det här namnet med högst 50 tecken, som består av gemena alfanumeriska tecken eller bindestreck (-), men får inte sluta med ett bindestreck.  Det kan inte ändras när erbjudandet har publicerats.  |
|  *SKU-information*   |  |
| **Rubrik**        | Eget namn för erbjudandet för visning i marketplace. Högst 50 tecken. |
| **Sammanfattning**      | Kortfattad beskrivning av erbjudandet för visning i marketplace. Högst 100 tecken. |
| **Beskrivning**  | Beskrivningstext som ger en mer detaljerad förklaring av erbjudandet.  <!-- TD: max len/guidance? 3k characters -->  |
| **Dölj den här SKU: N** | Anger om SKU: N ska visas i marketplace för kunder.  Du kanske vill dölja SKU: N om du bara vill att det finns endast via lösningsmallar och inte för köp individuellt.  Det kan också vara användbart för första testningen eller för tillfälliga eller säsongens erbjudanden. |
| **Tillgänglighet på moln** | Anger på vilka moln SKU: N ska vara tillgängliga.  Standardvärdet är den offentliga versionen av Azure.  Microsoft Azure Government är ett myndighetscommunitymoln med kontrollerad åtkomst för den amerikanska federala myndigheter, statliga, lokala eller stambaserade myndigheter och deras certifierade partner.  Läs mer om molnet för myndigheter, [Välkommen till Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Är detta en privat SKU?** | Anger om SKU: N är privat eller offentlig. Standardvärdet är **nr** (offentliga).  Mer information finns i [offentliga och privata SKU: er](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Tillgänglighet för land/Region** | Anger vilka länder eller regioner som din SKU kommer att köpas. Välj minst en region/land. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Prissättning*   |  |
| **License-modell**| Standardiserade faktureringsmodellen för att använda.  Om du väljer **användningsbaserad varje månad faktureras SKU**, en accordion avsnittet öppnas så att du kan ange information om priser per kärna och om du vill att erbjuda en kostnadsfri utvärderingsperiod.  Det här avsnittet kan du exportera och importera den här prissättningsschema till Excel. Mer information finns i [betalningsalternativ på Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *VM-avbildningar*   |  |
| **Operativsystemets familj** | Anger om lösningen VM är Windows - eller Linux-baserade. |
| **Välj typ av operativsystem** | Viss leverantör eller version av den angivna Operativsystemet. |
| **Eget namn** | Operativsystemets namn som ska visas för kunder.  |
| **Rekommenderade storlekar för Virtuella datorer** | Gör det möjligt för val av upp till sex storlekar som rekommenderas i en standardiserad lista.  Även om de här rekommendationerna visas på en framträdande plats för potentiella kunder, kan de ange vilken storlek som är kompatibel med lösningen avbildningen. | 
| **Öppna portar**| Portar öppna och protokoll för att ge stöd för SKU.  Dessa konfigurationer måste matcha det virtuella nätverket som du har konfigurerat för det virtuella nätverket av VM-lösningen. De här inställningarna ska börja gälla under distribution av virtuella datorer. Portinställningar kan dock ändras när du har publicerat en SKU. Mer information finns i [hur du öppnar portar till en virtuell dator med Azure portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Följande nätverk standardmappningar läggs till i alla virtuella datorer. &emsp; Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Disk-Version**  | Associerade lösning VM, anges av disk versionsnummer och Webbadress till disk. Disken måste vara i [sémantickou verzi](https://semver.org/) format: `<major>.<minor>.<patch>`.  URL: en är signaturen för delad åtkomst URI: N som skapats för det virtuella Hårddisken för operativsystemet.  Även om du kan lägga till upp till åtta versioner per SKU, visas endast det högsta disk versionsnumret för en SKU: N på Azure Marketplace. De andra versionerna kommer endast att vara synliga via API: er.  <!--TD: Add more specific link to API --> <br/> Den **ny datadisk** accordion avsnittet kan du ansluta upp till 15 datadiskar till den virtuella datorn.  När du publicerar en SKU med en viss VM-version och associerade datadiskar, kan inte den här konfigurationen ändras.  Om ytterligare VM-versioner läggs till i SKU: N, måste de också stöder samma antal datadiskar. <br/> Om du inte har skapat din Azure-baserad VM-avbildningar kan du lägga till uppdatera det här fältet senare.  Information om hur du skapar den associerade VM-resursen finns i avsnittet [Create VM för tekniska resurser](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Klicka på **spara** spara förloppet. I nästa flik ska du ange om stöd för ditt erbjudande [Test Drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Ytterligare priser överväganden

Prissättningsmodellen som beskrivs ovan är en grundläggande beskrivning.  Den genomgår ändringar och kan påverkas av lokala eller regionala momsregler och Microsoft priser principer. 

### <a name="simplified-currency-pricing"></a>Förenklad valuta priser

Från och med September 1 2018, ett nytt avsnitt kallas **förenklad valuta priser** kommer att läggas till på portalen. Microsoft effektivisera verksamheten Azure Marketplace genom att aktivera mer förutsägbara priser och samlingar från dina kunder över hela världen. Den här effektivisera omfattar att minska antalet valutor där vi fakturerar dina kunder.  Mer information finns i [uppdatera en befintlig virtuell dator erbjuder på Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Mer information om skatter och priser

* Microsoft klassificerar vissa länder som *skatt efterges länder*.  I dessa länder Microsoft samlar in skatter från kunder och betalar (inkomna ärenden) skatt till myndigheterna.  Partner är normalt ansvarig för att samla in skatter från sina kunder och betala in skatt till myndigheterna i andra länder. Du måste ha möjlighet att beräkna och betala lokala skatter om du vill sälja i dessa länder.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Priserna är inte kan ändras när ett erbjudande lanseras. Du kan fortfarande lägga till eller ta bort regioner som stöds. 
* Microsoft debiterar kundens-Användningsavgift för standard Azure VM förutom din schemalagda SKU-avgifter.
* Priserna anges för alla regioner i lokal valuta på valuta som är tillgängliga vid tidpunkten för att priser.  <!-- TD: Meaning? - Offer created, published, other? -->
* Om du vill ange priset för varje region individuellt exportera prissättning kalkylbladet, tillämpa anpassad prissättning och sedan importera. 

<!-- TD: The detailed information in the table and supplemental notes should be centralized in another topic, maybe "Billing Options" in AMP tree. Need to include a common section on export/import pricing-->

