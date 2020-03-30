---
title: Förstå användning av virtuella Azure-datorer
description: Förstå användningsinformation för virtuella datorer
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: 2aa175d97787d82aae062a95ed519f35ff65816b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982266"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Förstå användning av virtuella Azure-datorer
Genom att analysera dina Azure-användningsdata kan kraftfulla förbrukningsinsikter vinnas – insikter som kan möjliggöra bättre kostnadshantering och allokering i hela organisationen. Det här dokumentet innehåller en djupdykning i din Azure Compute-förbrukningsinformation. Mer information om allmän Azure-användning finns i [Förstå din faktura](../../cost-management-billing/understand/review-individual-bill.md).

## <a name="download-your-usage-details"></a>Ladda ned din användningsinformation
Börja [med att ladda ner dina användningsuppgifter](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md). Tabellen nedan innehåller definition och exempelvärden för användning för virtuella datorer som distribueras via Azure Resource Manager. Det här dokumentet innehåller inte detaljerad information för virtuella datorer som distribueras via vår klassiska modell.


| Fält             | Betydelse                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Exempel på värden                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Datum för användning         | Det datum då resursen användes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  "11/23/2017"                                                                                                                                                                                                                                                                                                                                                     |
| Mätar-ID           | Identifierar den tjänst på den högsta nivån som den här användningen tillhör.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Virtuella datorer"                                                                                                                                                                                                                                                                                                                                               |
| Mätarens underkategori | Identifierare för faktureringsmätaren. <ul><li>För beräkningstimmeanvändning finns det en mätare för varje VM-storlek + OS (Windows, icke-Windows) + Region.</li><li>För Premium-programanvändning finns det en mätare för varje programvarutyp. De flesta premiumprogrambilder har olika mätare för varje kärnstorlek. Mer information finns på [sidan Beräkna priser.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Mätarnamn         | Detta är specifikt för varje tjänst i Azure. För beräkning är det alltid "Beräkningstimmar".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Beräkna timmar"                                                                                                                                                                                                                                                                                                                                                  |
| Mätarregion       | Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret ligger.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "JA Öst"                                                                                                                                                                                                                                                                                                                                                       |
| Enhet               | Identifierar i vilken enhet tjänsten debiteras. Beräkningsresurser faktureras per timme.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Timmar"                                                                                                                                                                                                                                                                                                                                                          |
| Förbrukad           | Mängden resurs som har förbrukats för den dagen. För Beräkning fakturerar vi för varje minut som den virtuella datorn körde under en viss timme (upp till 6 decimaler noggrannhet).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    "1", "0.5"                                                                                                                                                                                                                                                                                                                                                    |
| Resursplats  | Identifierar det datacenter där resursen körs.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "JA Öst"                                                                                                                                                                                                                                                                                                                                                        |
| Förbrukad tjänst   | Azure-plattformstjänsten som du använde.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Resursgrupp     | Resursgruppen där den distribuerade resursen körs. Mer information finns i [Översikt över Azure Resource Manager.](../../azure-resource-manager/management/overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "Myrg"                                                                                                                                                                                                                                                                                                                                                        |
| Instans-ID        | Identifieraren för resursen. Identifieraren innehåller namnet du angav för resursen när den skapades. För virtuella datorer innehåller instans-ID:t SubscriptionId, ResourceGroupName och VMName (eller skalningsuppsättningsnamn för skalningsuppsättningsanvändning).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>eller<br><br>"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Taggar               | Tagg som du tilldelar resursen. Använd taggar för att gruppera faktureringsposter. Läs om hur du [taggar dina virtuella datorer.](tag.md) Detta är endast tillgängligt för virtuella datorer i Resource Manager.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Ytterligare info    | Tjänstspecifika metadata. För virtuella datorer fyller vi i följande i fältet ytterligare information: <ul><li>Bildtyp- specifik bild som du körde. Hitta hela listan över strängar som stöds nedan under Bildtyper.</li><li>Tjänsttyp: storleken som du har distribuerat.</li><li>VMName: namnet på din virtuella dator. Detta fylls bara i för skaluppsättning virtuella datorer. Om du behöver vm-namnet för skalningsuppsättning virtuella datorer kan du hitta det i instans-ID-strängen ovan.</li><li>UsageType: Detta anger vilken typ av användning som detta representerar.<ul><li>ComputeHR är beräkningstimanvändningen för den underliggande virtuella datorn, till exempel Standard_D1_v2.</li><li>ComputeHR_SW är premiumprogramavgiften om den virtuella datorn använder premiumprogramvara, som Microsoft R Server.</li></ul></li></ul>    | Virtuella datorer {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Skaluppsättningar för virtuell dator {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Premiumprogramvara {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Avbildningstyp
För vissa avbildningar i Azure-galleriet fylls avbildningstypen i fältet Ytterligare information. Detta gör det möjligt för användare att förstå och spåra vad de har distribuerat på sin virtuella dator. De värden som fylls i i det här fältet baserat på den avbildning som du har distribuerat är följande:
  - BitRock () 
  - Canonical 
  - FreeBSD 
  - Öppna logik 
  - Oracle 
  - SLES för SAP 
  - Förhandsversionen av SQL Server 14 på förhandsversionen av Windows Server 2012 R2 
  - SUSE
  - SUSE-premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat för SAP-affärsprogram     
  - Röd hatt för SAP HANA 
  - Windows-klient BYOL 
  - Windows Server BYOL 
  - Förhandsversionen av Windows Server 

## <a name="service-type"></a>Typ av tjänst
Fältet Tjänsttyp i fältet Ytterligare information motsvarar den exakta vm-storleken som du har distribuerat. Virtuella datorer med premiumlagring (SSD-baserade) och virtuella datorer med icke-premiumlagring (HDD-baserade) är prissatta på samma sätt. Om du distribuerar en SSD-baserad storlek, till exempel Standard\_DS2\_v2,\_visas\_icke-SSD-storleken ("Standard D2 v2 VM")\_i\_kolumnen Meter-underkategori och SSD-storlek ("Standard DS2 v2") i fältet Ytterligare information.

## <a name="region-names"></a>Namn på region
Regionnamnet som fylls i fältet Resursplats i användningsinformationen varierar från regionnamnet som används i Azure Resource Manager. Här är en mappning mellan regionens värden:

|    **Namn på resurshanterarens region**       |    **Resursplats i användningsinformation**    |
|--------------------------|------------------------------------------|
|    australieneast         |    Australien, östra                               |
|    Australien, sydöstra    |    Australien, sydöstra                          |
|    Brasilien, södra           |    Brasilien, södra                              |
|    CanadaCentral         |    Kanada, centrala                            |
|    KanadaEast            |    Kanada, östra                               |
|    CentralIndia (centralindia)          |    Indien, centrala                            |
|    USA, centrala             |    USA, centrala                            |
|    Kinaeast (kinesiska)             |    Kina, östra                            |
|    kinanort            |    Kina, norra                           |
|    eastasia              |    Asien, östra                             |
|    eastus                |    USA, östra                               |
|    eastus2 (östra)               |    USA, östra 2                             |
|    GermanyCentral        |    DE Centrala                            |
|    TysklandNordenast      |    DE Nordost                          |
|    Japan, östra             |    Japan, östra                               |
|    japanwest (japanwest)             |    Japan, västra                               |
|    KoreaCentral          |    Sydkorea, centrala                            |
|    KoreaSouth (            |    Sydkorea, södra                              |
|    norra centralen        |    USA, norra centrala                      |
|    northeurope           |    Europa, norra                          |
|    southcentralus        |    USA, södra centrala                      |
|    Asien, sydöstra         |    Sydostasien                        |
|    SouthIndia (på andra sätt)            |    Indien, södra                              |
|    UkNorth               |    Norra USA                              |
|    uksouth (svenska)               |    Storbritannien, södra                              |
|    UKSouth2 (Svenska)              |    Storbritannien, södra 2                            |
|    ukwest (svenska)                |    Storbritannien, västra                               |
|    USDoDCentral          |    USA DoD, centrala                        |
|    USADodeast             |    USA DoD, östra                           |
|    USGovArizona (andra)          |    USGov Arizona                         |
|    usgoviowa (pågående)             |    USGov Iowa                            |
|    USGovTexas (andra)            |    USGov Texas                           |
|    usgovvirginia         |    USGov Virginia                        |
|    USA, västra centrala         |    USA, västra centrala                       |
|    Europa, västra            |    Europa, västra                           |
|    WestIndia (på andra)             |    Indien, västra                               |
|    westus                |    USA, västra                               |
|    westus2               |    USA, västra 2                             |


## <a name="virtual-machine-usage-faq"></a>Vanliga frågor om användning av virtuella datorer
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Vilka resurser debiteras vid distribution av en virtuell dator?    
Virtuella datorer förvärvar kostnader för själva den virtuella datorn, alla premiumprogram som körs på den virtuella datorn, lagringskontot\hanterad disk som är associerad med den virtuella datorn och bandbreddsöverföringar för nätverk från den virtuella datorn.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hur vet jag om en virtuell dator använder Azure Hybrid Benefit i GSV för användning?
Om du distribuerar med [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/)debiteras du den icke-Windows VM-kursen eftersom du tar med din egen licens till molnet. I fakturan kan du skilja vilka virtuella resurser som kör Azure\_Hybrid Benefit eftersom de\_har antingen "Windows Server BYOL" eller "Windows Client BYOL" i kolumnen ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hur är basic vs. Standard VM-typer differentierade i användningen CSV?
Virtuella datorer i grund- och standard A-serien erbjuds. Om du distribuerar en grundläggande virtuell dator, i mätarunderkategorin, har den strängen "Basic". Om du distribuerar en virtuell standard-A-serie visas vm-storleken som "A1 VM" eftersom Standard är standard. Mer information om skillnaderna mellan Basic och Standard finns på [prissidan](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Vad är extralit-, små-, medelstora, stora och extrastora storlekar?
ExtraSmall - ExtraLarge är äldre\_namn för\_Standard A0 - Standard A4. I klassiska vm-användningsposter kan du se den här konventionen som används om du har distribuerat dessa storlekar.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Vad är skillnaden mellan mätarregionen och resursplats?
Mätarregionen är associerad med mätaren. För vissa Azure-tjänster som använder ett pris för alla regioner kan fältet Mätarregion vara tomt. Men eftersom virtuella datorer har dedikerade priser per region för virtuella datorer, fylls det här fältet i. På samma sätt är resursplatsen för virtuella datorer den plats där den virtuella datorn distribueras. Azure-regionen i båda fälten är desamma, även om de kan ha en annan strängkonvention för regionens namn.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Varför är ImageType-värdet tomt i fältet Ytterligare information?
Fältet ImageType fylls bara i för en delmängd av bilder. Om du inte distribuerade någon av bilderna ovan är ImageType tom.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Varför är VMName tomt i ytterligare information?
VMName fylls bara i fältet Ytterligare information för virtuella datorer i en skalningsuppsättning. Fältet InstanceID innehåller VM-namnet för virtuella datorer som inte har skalningsuppsättning.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Vad betyder ComputeHR i fältet UsageType i ytterligare information?
ComputeHR står för Compute Hour som representerar användningshändelsen för den underliggande infrastrukturkostnaden. Om UsageType är\_ComputeHR SW representerar användningshändelsen premiumprogramvaran för den virtuella datorn.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hur vet jag om jag debiteras för premiumprogramvara?
När du utforskar vilken VM-avbildning som bäst passar dina behov, se till att kolla in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Avbildningen har programvaran plan hastighet. Om du ser "Gratis" för priset, det finns ingen extra kostnad för programvaran. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Vad är skillnaden mellan Microsoft.ClassicCompute och Microsoft.Compute i tjänsten Förbrukad?
Microsoft.ClassicCompute representerar klassiska resurser som distribueras via Azure Service Manager. Om du distribuerar via Resource Manager fylls Microsoft.Compute i den förbrukade tjänsten. Läs mer om [Azure Deployment-modellerna](../../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Varför är instanceID-fältet tomt för min användning av virtuella datorer?
Om du distribuerar via den klassiska distributionsmodellen är InstanceID-strängen inte tillgänglig.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Varför flödar inte taggarna för mina virtuella datorer till användningsinformationen?
Taggar flödar bara till dig användning CSV för Resource Manager virtuella datorer bara. Klassiska resurstaggar är inte tillgängliga i användningsinformationen.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hur kan den förbrukade kvantiteten vara mer än 24 timmar en dag?
I modellen Klassisk aggregeras fakturering för resurser på molntjänstnivå. Om du har mer än en virtuell dator i en molntjänst som använder samma faktureringsmätare sammanställs din användning tillsammans. Virtuella datorer som distribueras via Resource Manager faktureras på VM-nivå, så den här aggregeringen gäller inte.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Varför är priser inte tillgängliga för DS/FS/GS/LS-storlekar på prissidan?
Virtuella datorer med premiumlagringsberättigade faktureras i samma takt som virtuella datorer som inte är premiumlagringskompatibla. Endast dina lagringskostnader skiljer sig åt. Besök [prissidan](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) för lagring för mer information.

## <a name="next-steps"></a>Nästa steg
Mer information om dina användningsinformationer finns i [Förstå din faktura för Microsoft Azure.](../../cost-management-billing/understand/review-individual-bill.md)

