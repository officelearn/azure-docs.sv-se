---
title: Förstå användningen av virtuella Azure-datorer | Microsoft Docs
description: Förstå användnings information om virtuella datorer
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
ms.openlocfilehash: 303bf41d943267c42c1311d185a4ff65baf5178f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834600"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Förstå användningen av virtuella Azure-datorer
Genom att analysera Azures användnings data kan kraftfulla förbruknings insikter uppnås – insikter som kan möjliggöra bättre kostnads hantering och allokering i hela organisationen. Det här dokumentet ger en detaljerad genom gång av information om din Azure Compute-förbrukning. Om du vill ha mer information om allmän Azure-användning går du till att [förstå din faktura](../../billing/billing-understand-your-bill.md).

## <a name="download-your-usage-details"></a>Hämta din användnings information
Börja med att [Hämta din användnings information](../../billing/billing-download-azure-invoice-daily-usage-date.md). Tabellen nedan innehåller definitionen och exempel på användnings värden för Virtual Machines som distribueras via Azure Resource Manager. Det här dokumentet innehåller ingen detaljerad information för virtuella datorer som distribueras via vår klassiska modell.


| Fält             | Betydelse                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Exempel värden                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Användningsdatum         | Det datum då resursen användes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| Mätare-ID           | Identifierar den översta tjänst som denna användning tillhör.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | “Virtual Machines”                                                                                                                                                                                                                                                                                                                                               |
| Mätarens underkategori | Identifierare för faktureringsmätaren. <ul><li>För beräkning av användnings tid finns det en mätning för varje VM-storlek + OS (Windows, icke-Windows) + region.</li><li>För förstklassig program varu användning finns det en mätare för varje program varu typ. De flesta Premium program avbildningar har olika mätare för varje kärn storlek. Mer information finns på sidan för [beräknings priser.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | “2005544f-659d-49c9-9094-8e0aea1be3a5”                                                                                                                                                                                                                                                                                                                           |
| Mätarnamn         | Detta är särskilt för varje tjänst i Azure. För Compute är det alltid "Beräkningstimmar".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Beräkningstimmar"                                                                                                                                                                                                                                                                                                                                                  |
| Region för mätning       | Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret ligger.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "Japan, öst"                                                                                                                                                                                                                                                                                                                                                       |
| Enhet               | Identifierar enheten som tjänsten debiteras i. Beräknings resurser faktureras per timme.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Timmarna                                                                                                                                                                                                                                                                                                                                                          |
| Förbrukad           | Mängden av den resurs som har för bruk ATS under den dagen. För Compute debiterar vi varje minut som den virtuella datorn kördes för en given timme (upp till 6 decimaler av noggrannhet).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Resursplats  | Identifierar det datacenter där resursen körs.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "Japan, öst"                                                                                                                                                                                                                                                                                                                                                        |
| Förbrukad tjänst   | Den Azure-plattform som du använde.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Resursgrupp     | Resursgruppen där den distribuerade resursen körs. Mer information finns i [Azure Resource Manager översikt.](../../azure-resource-manager/resource-group-overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    MyRG                                                                                                                                                                                                                                                                                                                                                        |
| Instans-ID        | Identifieraren för resursen. Identifieraren innehåller namnet du angav för resursen när den skapades. För virtuella datorer kommer instans-ID: t innehålla SubscriptionId, ResourceGroupName och VMName (eller skalnings uppsättningens namn för användning av skalnings uppsättningar).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/Subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/MyRG/providers/Microsoft. Compute/virtualMachines/MyVM1"<br><br>eller<br><br>"/Subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/MyRG/providers/Microsoft. Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Tags               | Tagg som du tilldelar till resursen. Använd taggar för att gruppera fakturerings poster. Lär dig att [tagga din Virtual Machines.](tag.md) Detta är endast tillgängligt för virtuella Resource Manager-datorer.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Ytterligare information    | Tjänstspecifika metadata. För virtuella datorer fyller vi i följande i fältet ytterligare information: <ul><li>Bildtyp-bestämd bild som du körde. Hitta den fullständiga listan över strängar som stöds under avbildnings typer.</li><li>Tjänst typ: den storlek som du har distribuerat.</li><li>VMName: namnet på den virtuella datorn. Detta är endast ifyllt för virtuella datorer med skalnings uppsättningar. Om du behöver ditt VM-namn för virtuella datorer med skalnings uppsättningar kan du se det i instans-ID-strängen ovan.</li><li>UsageType Anger den typ av användning som detta representerar.<ul><li>ComputeHR är beräknings timmen för den underliggande virtuella datorn, t. ex. Standard_D1_v2.</li><li>ComputeHR_SW är den förstklassiga program varu avgiften om den virtuella datorn använder Premium program vara, t. ex. Microsoft R Server.</li></ul></li></ul>    | Virtual Machines {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Virtual Machine Scale Sets {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Premium Software {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Avbildningstyp
För vissa bilder i Azure-galleriet fylls avbildnings typen i fältet ytterligare information. Detta gör det möjligt för användarna att förstå och spåra vad de har distribuerat på den virtuella datorn. De värden som är ifyllda i det här fältet baserat på den avbildning som du har distribuerat är följande:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Öppna logik 
  - Oracle 
  - SLES för SAP 
  - SQL Server 14 för hands version av Windows Server 2012 R2 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat för SAP Business Applications     
  - Red Hat för SAP HANA 
  - BYOL för Windows-klient 
  - Windows Server-BYOL 
  - Windows Server Preview 

## <a name="service-type"></a>Typ av tjänst
Fältet typ av tjänst i fältet ytterligare information motsvarar den exakta VM-storlek som du har distribuerat. Virtuella datorer i Premium Storage (SSD-baserade) och icke-Premium-virtuella datorer (HDD-baserade) är i samma pris. Om du distribuerar en SSD-baserad storlek, som standard\_DS2\_v2, kan du se storleken på den icke-SSD (\_"\_standard D2 v2 VM") i kolumnen mätar under kategori och SSD-storlek (standard DS2\_ \_ v2) i fältet ytterligare information.

## <a name="region-names"></a>Region namn
Region namnet som fyllts i i fältet resurs plats i användnings informationen varierar från det region namn som används i Azure Resource Manager. Här är en mappning mellan värdena för regionen:

|    **Namn på Resource Manager-region**       |    **Resurs plats i användnings information**    |
|--------------------------|------------------------------------------|
|    australienöstra         |    Australien, östra                               |
|    australiensydöstra    |    Australien, sydöstra                          |
|    brasiliensödra           |    Brasilien, södra                              |
|    Indiensödra         |    Kanada, centrala                            |
|    Indien            |    Kanada, östra                               |
|    Kanada          |    Indien, centrala                            |
|    usacentrala             |    Centrala USA                            |
|    chinaeast             |    Kina, östra                            |
|    chinanorth            |    Kina, norra                           |
|    asienöstra              |    Östasien                             |
|    usaöstra                |    East US                               |
|    eastus2               |    USA, östra 2                             |
|    GermanyCentral        |    Tyskland, centrala                            |
|    GermanyNortheast      |    Tyskland, nordöstra                          |
|    japanöstra             |    Japan, östra                               |
|    japanvästra             |    Japan, västra                               |
|    Centrala          |    Sydkorea, centrala                            |
|    KoreaSouth            |    Sydkorea, södra                              |
|    usanorracentrala        |    Norra centrala USA                      |
|    europanorra           |    Norra Europa                          |
|    usasödracentrala        |    Södra centrala USA                      |
|    sydostasien         |    Sydostasien                        |
|    Australienöstra            |    Indien, södra                              |
|    UKNorth               |    Nord USA                              |
|    södrastorbritannien               |    Storbritannien, södra                              |
|    UKSouth2              |    Södra Storbritannien 2                            |
|    västrastorbritannien                |    Storbritannien, västra                               |
|    USDoDCentral          |    US DoD, centrala                        |
|    USDoDEast             |    US DoD, östra                           |
|    USGovArizona          |    Arizona (USA-förvaltad region)                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    Texas (USA-förvaltad region)                           |
|    usgovvirginia         |    USGov Virginia                        |
|    westcentralus         |    USA, västra centrala                       |
|    europavästra            |    Västra Europa                           |
|    Usavästracentrala             |    Indien, västra                               |
|    usavästra                |    Västra USA                               |
|    westus2               |    USA, västra 2                             |


## <a name="virtual-machine-usage-faq"></a>Vanliga frågor om användning av virtuell dator
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Vilka resurser debiteras vid distribution av en virtuell dator?    
Virtuella datorer får kostnader för själva den virtuella datorn, all Premium-programvara som körs på den virtuella datorn, den lagrings account\managed disk som är kopplad till den virtuella datorn och nätverks bandbredds överföringarna från den virtuella datorn.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hur kan jag se om en virtuell dator använder Azure Hybrid-förmån i CSV-filen för användning?
Om du distribuerar med hjälp av [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/)debiteras du den VM-taxa som inte är Windows-taxa eftersom du tar din egen licens till molnet. På din faktura kan du skilja på vilka virtuella datorer i Resource Manager som körs Azure Hybrid-förmån eftersom de har antingen\_"Windows Server-BYOL"\_eller "Windows-klientens BYOL" i kolumnen ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hur är Basic vs. Är standard typerna av virtuella datorer differentierade i användningen av CSV?
Både grundläggande och standard A-seriens virtuella datorer erbjuds. Om du distribuerar en virtuell dator i mätar under kategorin har den strängen "Basic". Om du distribuerar en standard virtuell dator med A-serien visas VM-storleken som "a1 VM" eftersom standard är standard. Mer information om skillnaderna mellan Basic och standard finns på [sidan med priser](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Vad är ExtraSmall, små, medel stora och ExtraLarge storlekar?
ExtraSmall-ExtraLarge är äldre namn för standard\_a0 – standard\_A4. I klassiska poster för VM-användning kan du se den här konventionen om du har distribuerat dessa storlekar.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Vad är skillnaden mellan mätar området och resurs platsen?
Mätar regionen är associerad med mätaren. För vissa Azure-tjänster som använder ett pris för alla regioner kan fältet mätar region vara tomt. Eftersom de virtuella datorerna har dedikerade priser per region för Virtual Machines fylls det här fältet i. På samma sätt är resurs platsen för Virtual Machines den plats där den virtuella datorn distribueras. Azure-regionen i båda fälten är samma, men de kan ha en annan sträng konvention för region namnet.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Varför är ImageType-värdet tomt i fältet ytterligare information?
Fältet ImageType är bara ifyllt för en delmängd av avbildningarna. Om du inte distribuerade någon av avbildningarna ovan är ImageType tomt.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Varför är VMName tomt i ytterligare information?
VMName fylls bara i ytterligare informations fält för virtuella datorer i en skalnings uppsättning. InstanceID-fältet innehåller det virtuella dator namnet för virtuella datorer som inte är skalade.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Vad betyder ComputeHR i fältet UsageType i ytterligare information?
ComputeHR står för beräknings timme som representerar användnings händelsen för den underliggande infrastruktur kostnaden. Om UsageType är ComputeHR\_SW representerar användnings händelsen Premium Software-avgiften för den virtuella datorn.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hur gör jag för att veta om jag debiteras för Premium-program?
När du utforskar vilken VM-avbildning som passar bäst för dina behov bör du ta en titt på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Avbildningen har program Plans priset. Om du ser "kostnads fri" för priset finns det ingen extra kostnad för program varan. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Vad är skillnaden mellan Microsoft. ClassicCompute och Microsoft. Compute i den förbrukade tjänsten?
Microsoft. ClassicCompute representerar klassiska resurser som distribueras via Azure-Service Manager. Om du distribuerar via Resource Manager fylls Microsoft. Compute i den förbrukade tjänsten. Läs mer om [Azures distributions modeller](../../azure-resource-manager/resource-manager-deployment-model.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Varför är InstanceID-fältet tomt för min virtuella dator användning?
Om du distribuerar via den klassiska distributions modellen är InstanceID-strängen inte tillgänglig.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Varför är taggarna för mina virtuella datorer inte flödar till användnings informationen?
Taggar endast flödar till dig för användning av CSV för endast virtuella Resource Manager-datorer. Klassiska resurs taggar är inte tillgängliga i användnings informationen.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hur kan den förbrukade kvantiteten vara mer än 24 timmar en dag?
I den klassiska modellen sammanställs faktureringen av resurser på moln tjänst nivån. Om du har fler än en virtuell dator i en moln tjänst som använder samma fakturerings mätare sammanställs användningen tillsammans. Virtuella datorer som distribueras via Resource Manager debiteras på VM-nivå, så denna agg regering gäller inte.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Varför är prissättning inte tillgängligt för DS/FS/GS/LS-storlekar på prissättnings Sidan?
Virtuella datorer med Premium Storage-kapacitet debiteras enligt samma taxa som icke-Premium Storage-kompatibla virtuella datorer. Endast dina lagrings kostnader skiljer sig åt. Besök [sidan med pris för lagring](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) för mer information.

## <a name="next-steps"></a>Nästa steg
Mer information om din användnings information finns i [förstå din faktura för Microsoft Azure.](../../billing/billing-understand-your-bill.md)

