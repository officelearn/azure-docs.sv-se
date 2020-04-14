---
title: Fliken SKU:er för virtuella datorer i Cloud Partner Portal för Azure Marketplace
description: Beskriver fliken SKU:er som används för att skapa ett erbjudande om virtuella datorer på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: a8dbadf959107e733a94216c8a01495e6ebf5039
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273128"
---
# <a name="virtual-machine-skus-tab"></a>Fliken SKU:er för virtuell dator

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Virtual Machine-erbjudande](https://aka.ms/CreateAzureVMoffer) för att hantera dina migrerade erbjudanden.

På fliken **SKU:er** på sidan **Nytt erbjudande** kan du skapa en eller flera SKU:er och koppla dem till ditt nya erbjudande.  Olika SKU:er kan skilja en lösning efter funktionsuppsättningar, VM-avbildningstyper, dataflöde eller skalbarhet, faktureringsmodeller eller någon annan egenskap.


## <a name="create-a-sku"></a>Skapa en SKU

Inledningsvis har ett nytt erbjudande inga associerade SKU: er, så du skapar ett genom att klicka på **Ny SKU**.

![Knappen Ny SKU på fliken Nytt erbjudande för virtuella datorer](./media/publishvm_005.png)

<br/>

Dialogrutan **Ny SKU** visas.  Ange identifieraren för den nya SKU och klicka sedan på **OK**. (Se nedan för namngivningskonventioner för identifierare.)  På fliken **SKU** visar nu de fält som är tillgängliga för redigering.    En bifogad asterisk (*) i fältnamnet anger att det krävs.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Fliken SKU i formuläret Nytt erbjudande för virtuella datorer](./media/publishvm_006.png)

I följande tabell beskrivs syftet, innehållet och formateringen för dessa fält.  Obligatoriska fält åtalas av en asterisk (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU-inställningar*   |    |
| **SKU-ID\***       | Identifierare för den här SKU:n.  Det här namnet har högst 50 tecken, som består av gemener alfanumeriska tecken eller streck (-), men kan inte sluta med ett streck.  Det går inte att ändra när erbjudandet har publicerats.  |
|  *SKU-detaljer*   |  |
| **Titel\***        | Eget namn för erbjudandet för visning på marknaden. Maximal längd på 50 tecken. |
| **Sammanfattning\***      | Kortfattad beskrivning av erbjudandet för visning på marknaden. Maximal längd på 100 tecken. |
| **Beskrivning\***  | Beskrivningstext som ger en mer detaljerad förklaring av erbjudandet.  <!-- TD: max len/guidance? 3k characters -->  |
| **Dölj den här SKU:n\*** | Anger om SKU ska vara synlig på marknadsplatsen för kunder.  Du kanske vill dölja SKU om du bara vill att den bara ska vara tillgänglig via lösningsmallar och inte för köp individuellt.  Det kan också vara användbart för inledande tester eller för tillfälliga eller säsongsbetonade erbjudanden. |
| **Molntillgänglighet\*** | Bestämmer på vilka moln SKU ska vara tillgängliga.  Standard är den offentliga versionen av Azure.  Microsoft Azure Government är ett moln för myndigheter med kontrollerad åtkomst för de amerikanska federala, statliga, lokala eller stam- och stamregeringarna och deras certifierade partner.  Mer information om myndighetsmoln finns i [Välkommen till Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Är det här en privat SKU?\*** | Anger om SKU:n är privat eller offentlig. Standardvärdet är **Nej** (offentligt).  Mer information finns i [offentliga och privata SKU:er](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Tillgänglighet för land/region\*** | Bestämmer vilka länder eller världsregioner din SKU ska köpas. Välj minst en region/ett land. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Prissättning*   |  |
| **Licensmodell\***| Standardiserad faktureringsmodell att använda.  Om du väljer **Användningsbaserad månadsfäst SKU**öppnas ett dragspelsavsnitt så att du kan ange information om priser per kärna och om du vill erbjuda en kostnadsfri provperiod.  I det här avsnittet kan du också exportera och importera det här prisschemat till Excel. Mer information finns [i Faktureringsalternativ på Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *VM-avbildningar*   |  |
| **Familj för operativsystem\*** | Anger om lösningen VM är Windows- eller Linux-baserad. |
| **Välj typ av operativsystem** | Specifik leverantör eller utgivning av det angivna operativsystemet. |
| **OS-eget namn\*** | Operativsystemnamn som ska visas för kunder.  |
| **Rekommenderade VM-storlekar\*** | Aktiverar val av upp till sex rekommenderade VM-storlekar från en standardiserad lista.  Den här listan skickas vidare till Azure-portalen och Microsoft-marknadsplatserna.  Den första vm-storleken i den här listan som är giltig (för den kundprenumerationen, regionen, zonen osv.) anges som standard för den potentiella kunden.  Användaren kan ändra den här storleken till alla som är kompatibla med lösningsavbildningen. | 
| **Öppna portar**| Portar att öppna och protokoll för att stödja för SKU.  Dessa konfigurationer måste matcha det virtuella nätverk som du har konfigurerat för nätverket för lösningen VM. Dessa inställningar träder i kraft under vm-distribution. Portinställningarna kan dock ändras när du har publicerat en SKU. Ner information finns i artikeln om [hur du öppnar portar för en virtuell dator med Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Följande standardnätverksmappningar läggs till i alla virtuella datorer. &emsp;Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Diskversion**  | Associerad lösning VM, angiven av diskversionsnummer och disk-URL. Diskversionen måste vara i [semantisk versionsformat:](https://semver.org/) `<major>.<minor>.<patch>`.  URL:en är signatur-URI för delad åtkomst som skapats för operativsystemets virtuella hårddisk.  Även om du kan lägga till upp till åtta diskversioner per SKU visas endast det högsta diskversionsnumret för en SKU i Azure Marketplace. De andra versionerna visas bara via API:er.  <!--TD: Add more specific link to API --> <br/> Med avsnittet **Nytt datadiskspelning** kan du koppla upp till 15 datadiskar till den virtuella datorn.  När du har publicerat en SKU med en viss VM-version och associerade datadiskar kan den här konfigurationen inte ändras.  Om ytterligare VM-versioner läggs till i SKU måste de också ha samma antal datadiskar. <br/> Om du inte har skapat dina Azure-baserade VM-avbildningar kan du lägga till uppdatera det här fältet senare.  Information om hur du skapar den associerade VM-resursen finns i avsnittet [Skapa virtuella tekniska resurser](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Klicka på **Spara** om du vill spara dina framsteg. På nästa flik anger du om erbjudandet stöder [Test Drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Ytterligare prisöverväganden

Prismodellen som beskrivs ovan är en grundläggande beskrivning.  Den genomgår ändringar och kan påverkas av lokala eller regionala skatteregler och Microsofts prispolicyer. 

### <a name="new-core-sizes-added-on-722019"></a>Nya kärnstorlekar tillagda den 7/2/2019

VM-utgivare informerades den 2 juli 2019 om tillägg av nya priser för nya Azure-storlekar för virtuella datorer (baserat på antalet kärnor).  De nya priserna är för kärnstorlekarna 10, 44, 48, 60, 120, 208 och 416.  För befintliga VM erbjuder nya priser för dessa kärnor storlekar beräknades automatiskt baserat på aktuella priser.  Utgivare har fram till 1 augusti 2019 på sig att granska de extra priserna och göra önskade ändringar.  Efter detta datum, om inte redan återpublicerats av utgivaren, börjar de automatiskt beräknade priserna för dessa nya kärnstorlekar att gälla.


### <a name="simplified-currency-pricing"></a>Förenklad valutaprissättning

Från och med den 1 september 2018 läggs ett nytt avsnitt med namnet **Förenklad valutaprissättning** till i portalen. Microsoft effektiviserar Azure Marketplace-verksamheten genom att möjliggöra mer förutsägbara priser och samlingar från dina kunder över hela världen. Denna rationalisering kommer att omfatta att minska antalet valutor där vi fakturerar dina kunder.  Mer information finns i [Uppdatera ett befintligt vm-erbjudande på Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Ytterligare information om skatter och priser

* Microsoft klassificerar vissa länder/regioner som länder som *är skattebesända.*  I sådana länder/regioner tar Microsoft ut skatter från kunder och betalar sedan (eftersändelser) skatter till regeringen.  I andra länder/regioner är partner vanligtvis ansvariga för att samla in skatter från sina kunder och betala skatt till regeringen. Om du väljer att sälja i de senare länderna/regionerna måste du ha möjlighet att beräkna och betala lokala skatter.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Priserna kan inte ändras när ett erbjudande går live. Du kan dock fortfarande lägga till eller ta bort regioner som stöds. 
* Microsoft debiterar användaravgifter för kunden standard på Azure VM utöver dina schemalagda SKU-avgifter.
* Priserna anges för alla regioner i lokal valuta på tillgängliga valutakurser vid tidpunkten för inställningspriserna.  <!-- TD: Meaning? - Offer created, published, other? -->
* Om du vill ange varje regions pris individuellt exporterar du priskalkylbladet, tillämpar anpassad prissättning och importerar sedan. 


## <a name="next-steps"></a>Nästa steg

Du kan också ange [provkörningsinformation](./cpp-test-drive-tab.md) om du stöder den här funktionen. Annars tillhandahåller du [marknadsplatsdata](./cpp-marketplace-tab.md) för ditt erbjudande.
