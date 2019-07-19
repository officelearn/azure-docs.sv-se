---
title: 'Fliken SKU: er för virtuell dator i Cloud Partner Portal för Azure Marketplace'
description: 'Beskriver fliken SKU: er som används för att skapa ett erbjudande för virtuella datorer på Azure Marketplace.'
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: dceb82af73061b91676ffb7061c7495995e76667
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868706"
---
# <a name="virtual-machine-skus-tab"></a>Fliken SKU: er för virtuell dator

På fliken **SKU: er** på sidan **ny erbjudande** kan du skapa en eller flera SKU: er och koppla dem till ditt nya erbjudande.  Olika SKU: er kan särskilja en lösning med funktions uppsättningar, avbildnings typer för virtuella datorer, data flöde eller skalbarhet, fakturerings modeller eller någon annan egenskap.


## <a name="create-a-sku"></a>Skapa en SKU

Ett nytt erbjudande har inlednings vis inga tillhör ande SKU: er, så du kommer att skapa ett genom att klicka på **ny SKU**.

![Ny SKU-knapp på fliken ny erbjudande för virtuella datorer](./media/publishvm_005.png)

<br/>

Dialog rutan **ny SKU** visas.  Ange ID: t för den nya SKU: n och klicka sedan på **OK**. (Se nedan för namngivnings konventioner för identifierare.)  På fliken **SKU** visas nu de fält som är tillgängliga för redigering.    En asterisk (*) som lagts till i fält namnet anger att det är obligatoriskt.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Fliken SKU i formuläret ny erbjudande för virtuella datorer](./media/publishvm_006.png)

I följande tabell beskrivs syftet, innehållet och formateringen för dessa fält.  Obligatoriska fält förutsägs av en asterisk (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU-inställningar*   |    |
| **SKU-ID\***       | Identifierare för denna SKU.  Namnet får innehålla högst 50 tecken, bestående av gemena alfanumeriska tecken eller bindestreck (-), men får inte sluta med ett bindestreck.  Den kan inte ändras efter att erbjudandet har publicerats.  |
|  *SKU-information*   |  |
| **Avdelning\***        | Eget namn på erbjudandet för visning i Marketplace. Maximal längd på 50 tecken. |
| **Drag\***      | Kortfattad beskrivning av erbjudandet för visning i Marketplace. Maximal längd på 100 tecken. |
| **Beteckning\***  | Beskrivnings text som ger en mer detaljerad förklaring av erbjudandet.  <!-- TD: max len/guidance? 3k characters -->  |
| **Dölj denna SKU\*** | Anger om SKU: n ska vara synlig i marknads platsen för kunder.  Du kanske vill dölja SKU: n om du bara vill att den endast är tillgänglig via Solution-mallar och inte för att köpa en och en.  Det kan också vara användbart för inledande testning eller för tillfälliga eller säsongs erbjudanden. |
| **Moln tillgänglighet\*** | Anger vilka moln som SKU: n ska vara tillgänglig för.  Standardvärdet är den offentliga versionen av Azure.  Microsoft Azure Government är ett moln i en community med kontrollerad åtkomst till amerikanska federala, statliga, lokala eller stambaserad myndigheter och deras certifierade partner.  Mer information om myndighets moln finns i [Välkommen till Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Är detta en privat SKU?\*** | Anger om SKU: n är privat eller offentlig. Standardvärdet är **no** (public).  Mer information finns i [offentliga och privata SKU: er](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Tillgänglighet för land/region\*** | Fastställer vilka länder eller regioner som din SKU ska vara tillgänglig för köp. Välj minst en region/ett land. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Prissättning*   |  |
| **Licens modell\***| Standardiserad fakturerings modell som ska användas.  Om du väljer **användning-baserad månatlig fakturerad SKU**öppnas ett avsnitt där du kan ange information om priser per kärna och om du vill erbjuda en kostnads fri utvärderings period.  I det här avsnittet kan du också exportera och importera detta pris schema till Excel. Mer information finns i [fakturerings alternativ i Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *VM-avbildningar*   |  |
| **Operativ system familj\*** | Anger om den virtuella lösningen är Windows-eller Linux-baserad. |
| **Välj typ av operativ system** | Specifik leverantör eller version av det angivna operativ systemet. |
| **Eget namn på operativ system\*** | Namn på operativ system som ska visas för kunderna.  |
| **Rekommenderade VM-storlekar\*** | Aktiverar val av upp till sex rekommenderade VM-storlekar från en standardiserad lista.  Den här listan skickas vidare till Azure Portal och Microsoft Marketplace.  Den första storleken på den virtuella datorn i listan som är giltig (för kund prenumeration, region, zon osv.) anges som standard för den potentiella kunden.  Användaren kan ändra den här storleken till valfri kompatibel med lösnings avbildningen. | 
| **Öppna portar**| Portar som ska öppnas och protokoll som stöds för SKU: n.  Dessa konfigurationer måste matcha det virtuella nätverk som du har konfigurerat för nätverket för den virtuella lösningen. De här inställningarna träder i vägen under distributionen av virtuella datorer. Port inställningarna kan dock ändras när du har publicerat en SKU. Mer information finns i [så här öppnar du portar till en virtuell dator med Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Följande standard nätverks mappningar läggs till i alla virtuella datorer. &emsp;Aktivitets 3389-> 3389 TCP, 5986-> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Disk version**  | Associerad virtuell lösning VM, anges av diskens versions nummer och disk-URL. Disk versionen måste vara i [semantiskt versions](https://semver.org/) format `<major>.<minor>.<patch>`:.  URL: en är URL: en för signaturen för delad åtkomst som skapats för operativ systemets virtuella hård disk.  Även om du kan lägga till upp till åtta disk versioner per SKU visas bara det högsta disk versions numret för en SKU i Azure Marketplace. De andra versionerna visas bara via API: er.  <!--TD: Add more specific link to API --> <br/> I avsnittet **ny datadisk** -spel kan du ansluta upp till 15 data diskar till den virtuella datorn.  Den här konfigurationen kan inte ändras när du har publicerat en SKU med en specifik VM-version och tillhör ande data diskar.  Om ytterligare VM-versioner läggs till i SKU: n måste de också ha stöd för samma antal data diskar. <br/> Om du inte har skapat dina Azure-baserade VM-avbildningar kan du lägga till uppdateringar i det här fältet senare.  Information om hur du skapar den associerade VM-resursen finns i avsnittet [skapa tekniska till gångar för virtuella datorer](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Klicka på **Spara** för att spara förloppet. På nästa flik anger du om erbjudandet stöder [test enheten](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Ytterligare pris överväganden

Pris modellen som beskrivs ovan är en grundläggande Beskrivning.  Den genomgår ändringar och kan påverkas av lokala eller regionala skatte regler och Microsofts pris principer. 

### <a name="new-core-sizes-added-on-722019"></a>Nya kärn storlekar som lagts till på 7/2/2019

VM-utgivaren meddelades den 2 juli 2019 om tillägg av nya priser för nya storlekar för virtuella Azure-datorer (baserat på antalet kärnor).  De nya priserna är för kärn storlekarna 10, 44, 48, 60, 120, 208 och 416.  För befintliga virtuella datorer har nya priser för dessa kärnor automatiskt beräknats baserat på aktuella priser.  Utgivare har fram till den 1 augusti 2019 att granska ytterligare priser och göra önskade ändringar.  Efter det här datumet börjar de automatiskt beräknade priserna för de nya kärn storlekarna att gälla, om de inte redan publiceras om av utgivaren.


### <a name="simplified-currency-pricing"></a>Pris för förenklad valuta

Från och med den 1 2018 september kommer ett nytt avsnitt med namnet förenklad **valuta prissättning** att läggas till i portalen. Microsoft effektiviserar Azure Marketplace-verksamheten genom att möjliggöra mer förutsägbara priser och samlingar från dina kunder över hela världen. Den här effektiviserat omfattar att minska antalet valutor som vi fakturerar kunderna i.  Mer information finns i [Uppdatera ett befintligt VM-erbjudande på Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Ytterligare information om skatter och priser

* Microsoft klassificerar vissa länder/regioner som *skatte mottagare*.  I sådana länder/regioner samlar Microsoft in skatter från kunder och betalar (betalar) skatter till regeringen.  I andra länder/regioner ansvarar partner vanligt vis för att samla in skatter från sina kunder och betala skatter till regeringen. Om du väljer att sälja i de sistnämnda länderna/regionerna måste du ha möjlighet att beräkna och betala lokala skatter.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Priserna kan inte ändras när ett erbjudande går live. Du kan dock fortfarande lägga till eller ta bort regioner som stöds. 
* Microsoft debiterar kund standard avgifterna för virtuella Azure-datorer utöver de schemalagda SKU-avgifterna.
* Priser anges för alla regioner i lokal valuta för tillgängliga valuta priser vid tidpunkten för att ställa in priser.  <!-- TD: Meaning? - Offer created, published, other? -->
* Om du vill ange varje regions pris individuellt exporterar du pris kalkyl bladet, använder anpassad prissättning och importerar sedan. 


## <a name="next-steps"></a>Nästa steg

Om du har stöd för den här funktionen kan du ange [test enhets](./cpp-test-drive-tab.md) information. annars tillhandahåller du [Marketplace](./cpp-marketplace-tab.md) -data för ditt erbjudande.
