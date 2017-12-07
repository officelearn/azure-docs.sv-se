---
title: "Användning av Azure virtuella datorer att förstå | Microsoft Docs"
description: "Förstå användningsinformation för virtuell dator"
services: virtual-machines
documentationcenter: 
author: mmccrory
manager: jeconnoc
editor: 
tags: azure-virtual-machine
ms.assetid: 
ms.service: 
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: c87c4256aa193a4971b75c3230d1996c2efdc352
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="understanding-azure-virtual-machine-usage"></a>Förstå användning av Azure virtuella datorer
Förbrukning av kraftfulla insikter kan uppnås genom att analysera dina Azure användningsdata – insikter som kan aktivera kostnad bättre hantering och tilldelning i hela organisationen. Det här dokumentet ger en djupdykning i din Azure Compute förbrukning information. För mer information om allmänna Azure användning, gå till [förstå fakturan](/billing/billing-understand-your-bill.md).

## <a name="download-your-usage-details"></a>Hämta din användningsinformation
Ska börja, [hämta din användningsinformation](/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv.md). Tabellen nedan innehåller definitioner och exempel värdena för användning för virtuella datorer distribueras via Azure Resource Manager. Det här dokumentet innehåller inte detaljerad information om virtuella datorer som distribueras via vår klassiska modellen.


| Fält             | Betydelse                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Exempelvärden                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Datum för användning         | Datum när resursen användes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| Mätar-ID           | Identifierar tjänsten på den översta nivån som denna användning tillhör.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | ”Virtuella datorer”                                                                                                                                                                                                                                                                                                                                               |
| Mätarens underkategori | Identifierare för faktureringsmätaren. <ul><li>För användning med Compute timme, är en mätare för varje VM-storlek + OS (Windows, icke-Windows) + Region.</li><li>Det finns en mätare för varje typ av programvara för Premium programvaruanvändning. De flesta premium programvara bilder har olika mätare för varje core storlek. Mer information finns i [beräkna priser för sidan.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | ”2005544f-659d-49c9-9094-8e0aea1be3a5”                                                                                                                                                                                                                                                                                                                           |
| Mätarnamn         | Detta är specifika för varje tjänst i Azure. För beräkning är det alltid ”Compute timmar”.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | ”Beräkningstimmar”                                                                                                                                                                                                                                                                                                                                                  |
| Mätarregion       | Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret ligger.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  ”Japan, Öst”                                                                                                                                                                                                                                                                                                                                                       |
| Enhet               | Identifierar den enhet som tjänsten debiteras i. Beräkningsresurser debiteras per timme.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | ”Timmar”                                                                                                                                                                                                                                                                                                                                                          |
| Förbrukad           | Storleken på den resurs som har förbrukats för den dagen. För beräkning debiterar för varje minut som den virtuella datorn har körts för en given timme (upp till 6 decimaler noggrannhet).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Resursplats  | Identifierar det datacenter där resursen körs.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | ”Japan, Öst”                                                                                                                                                                                                                                                                                                                                                        |
| Förbrukad tjänst   | Tjänsten Azure-plattformen som du använde.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | ”Microsoft.Compute”                                                                                                                                                                                                                                                                                                                                              |
| Resursgrupp     | Resursgruppen där den distribuerade resursen körs. Mer information finns i [översikt över Azure Resource Manager.](/azure-resource-manager/resource-group-overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    ”MyRG”                                                                                                                                                                                                                                                                                                                                                        |
| Instans-ID        | Identifierare för resursen. Identifieraren innehåller namnet du angav för resursen när den skapades. Instans-ID innehåller prenumerations-ID, ResourceGroupName och VMName för virtuella datorer, (eller skala ange namnet för användning skala).                                                                                                                                                                                                                                                                                                                                                                                                                    | ”/ prenumerationer/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1”<br><br>eller<br><br>”/ prenumerationer/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1”                                                                                           |
| Taggar               | Taggen som du tilldelar till resursen. Använd taggar fakturering posterna. Lär dig hur du [tagga dina virtuella datorer.](tag.md) Detta är tillgänglig för hanteraren för virtuella datorer bara.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | ”{” myDepartment ”:” RD ”,” myUser ”:” mittnamn ”}”                                                                                                                                                                                                                                                                                                                        |
| Ytterligare info    | Tjänstspecifika metadata. För virtuella datorer fyller vi följande i fältet ytterligare information: <ul><li>Bild typspecifika bilden som du körde. Hitta en fullständig lista med strängar som stöds under under bildtyper.</li><li>Service Type: den storlek som du har distribuerat.</li><li>VMName: namnet på den virtuella datorn. Detta fylls endast för skaluppsättning för virtuella datorer. Om du behöver ditt VM-namn för att skala ange virtuella datorer, kan du hitta som i instans-ID-strängen ovan.</li><li>UsageType: Anger vilken typ av användning som representerar.<ul><li>ComputeHR är den Compute timme användningen av den underliggande virtuella datorn som Standard_D1_v2.</li><li>ComputeHR_SW är premium programvara avgift om den virtuella datorn använder premium program som Microsoft R Server.</li></ul></li></ul>    | Virtuella datorer {”ImageType”: ”kanoniska”, ”ServiceType”: ”Standard_DS1_v2”, ”VMName” ”:”, ”UsageType” ”: ComputeHR”}<br><br>Skaluppsättningar för den virtuella datorn {”ImageType”: ”kanoniska”, ”ServiceType”: ”Standard_DS1_v2”, ”VMName”: ”myVM1”, ”UsageType”: ”ComputeHR”}<br><br>Premium-programvara {”ImageType”: ”” ”, ServiceType”: ”Standard_DS1_v2”, ”VMName” ”:”, ”UsageType” ”: ComputeHR_SW”} |

## <a name="image-type"></a>Avbildningstyp
För vissa avbildningar i Azure-galleriet fylls bildtypen i fältet ytterligare information. Detta gör det möjligt för användarna att förstå och spåra de har distribuerats på den virtuella datorn. De värden som fylls i automatiskt baserat på bilden som du har distribuerat är följande:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Öppna logik 
  - Oracle 
  - SLES för SAP 
  - SQL Server 14 förhandsgranskning i Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat för SAP Business-program     
  - Red Hat för SAP HANA 
  - Windows-klient BYOL 
  - Windows Server BYOL 
  - Förhandsvisning av Windows Server 

## <a name="service-type"></a>Typ av tjänst
Service type-fältet i ytterligare information om fältet motsvarar den exakta VM-storlek som du har distribuerat. Premium-lagring virtuella datorer (SSD-baserad) och icke-premium storage virtuella datorer (HDD-baserat) är prissatta samma. Om du distribuerar en SSD-baserad storlek som Standard\_DS2\_v2, visas inte SSD-storlek ('Standard\_D2\_v2 VM ”) i kolumnen mätaren underkategorin och SSD-storlek ('Standard\_DS2\_ v2-) i fältet ytterligare information.

## <a name="region-names"></a>Namn på områden
Regionsnamnet i fältet resursplats i användningsinformation skiljer sig från regionsnamnet som används i Azure Resource Manager. Här är en mappning mellan värdena som region:

|    **Hanteraren för filserverresurser regionnamn**       |    **Resursplats i användningsinformation**    |
|--------------------------|------------------------------------------|
|    australienöstra         |    Australien Öst                               |
|    australiensydöstra    |    Australien, sydost                          |
|    brasiliensödra           |    Brasilien, Syd                              |
|    CanadaCentral         |    Kanada, Central                            |
|    CanadaEast            |    Kanada, Öst                               |
|    CentralIndia          |    Indien, Central                            |
|    usacentrala             |    Centrala USA                            |
|    chinaeast             |    Östra Kina                            |
|    chinanorth            |    Norra Kina                           |
|    asienöstra              |    Östasien                             |
|    usaöstra                |    Östra USA                               |
|    usaöstra2               |    Östra USA 2                             |
|    GermanyCentral        |    Tyskland, centrala                            |
|    GermanyNortheast      |    Tyskland, nordöstra                          |
|    japanöstra             |    Japan, Öst                               |
|    japanvästra             |    Japan, Väst                               |
|    KoreaCentral          |    KR Central                            |
|    KoreaSouth            |    KR Syd                              |
|    usanorracentrala        |    Norra centrala USA                      |
|    europanorra           |    Norra Europa                          |
|    usasödracentrala        |    Södra centrala USA                      |
|    sydostasien         |    Sydostasien                        |
|    SouthIndia            |    Indien, Syd                              |
|    UKNorth               |    USA, Nord                              |
|    södrastorbritannien               |    Storbritannien, södra                              |
|    UKSouth2              |    Storbritannien, södra 2                            |
|    västrastorbritannien                |    Storbritannien, västra                               |
|    USDoDCentral          |    US DoD, centrala                        |
|    USDoDEast             |    US DoD, östra                           |
|    USGovArizona          |    USA: s regering Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    USA: s regering Texas                           |
|    usgovvirginia         |    USGov Virginia                        |
|    westcentralus         |    USA, västra centrala                       |
|    europavästra            |    Västra Europa                           |
|    WestIndia             |    Indien, Väst                               |
|    usavästra                |    Västra USA                               |
|    westus2               |    USA, Väst 2                             |


## <a name="virtual-machine-usage-faq"></a>Vanliga frågor om användning av virtuella datorer
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Vilka resurser debiteras när du distribuerar en virtuell dator?    
Virtuella datorer få kostnader för Virtuellt datorn, en premium-program som körs på den virtuella datorn account\managed Lagringsdisken som är kopplade till den virtuella datorn och nätverk bandbredd överförs från den virtuella datorn.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hur vet jag om en virtuell dator använder Azure Hybrid förmån i användning CSV?
Om du distribuerar med den [Azure Hybrid förmån](https://azure.microsoft.com/pricing/hybrid-benefit/), debiteras du den icke-Windows VM-hastigheten eftersom du använder din egen licens till molnet. I fakturan, kan du skilja som hanteraren för virtuella datorer som körs Hybrid-förmån för Azure eftersom de har antingen ”Windows\_Server BYOL” eller ”Windows\_klienten BYOL” i kolumnen ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hur är grundläggande vs. Standard VM-typer differentierade i användning CSV?
Både Basic och Standard A-Series VMs erbjuds. Om du distribuerar en enkel virtuell dator i kategorin mätaren Sub har strängen ”Basic”. Om du distribuerar en Standard A-Series VM visas VM-storlek som ”A1 VM” eftersom Standard är standard. Mer information om skillnaderna mellan Basic och Standard finns de [prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Vad är ExtraSmall, Small, Medium, Large och ExtraLarge storlekar?
ExtraSmall - Extrastora är äldre namnen för Standard\_A0 – Standard\_A4. Du kan se denna konvention används om du har distribuerat dessa storlekar klassiska Virtuella användningsposter.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Vad är skillnaden mellan mätaren Region och Resource-plats?
Området mätaren är associerad med mätaren. För vissa Azure-tjänster som använder ett pris i alla regioner, vara mätaren Region fältet tomt. Eftersom virtuella datorer har särskilda priser per region för virtuella datorer, fylls det här fältet. På liknande sätt är resursplats för virtuella datorer den plats där den virtuella datorn distribueras. Azure-region i båda fälten är desamma, även om de kan ha en annan sträng konvention för regionsnamnet på.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Varför är värdet ImageType tom i fältet ytterligare information?
Fältet ImageType fylls endast för en delmängd av avbildningar. Om du inte har distribuerat en av avbildningarna ovan, är ImageType tomt.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Varför är VMName tom ytterligare Info?
VMName fylls endast i fältet ytterligare information för virtuella datorer i en skaluppsättning. InstanceID-fältet innehåller namnet på virtuella datorn för icke-skaluppsättning för virtuella datorer.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Vad innebär ComputeHR i fältet UsageType i ytterligare information?
ComputeHR står för Compute timme som representerar användningshändelse för underliggande infrasturcture kostnaden. Om UsageType är ComputeHR\_SW händelsen användning representerar kostnad för premium-programvara för den virtuella datorn.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hur vet jag om jag vill debiteras för premium programvara?
När utforska vilken VM-avbildning som bäst passar dina behov, måste du checka ut den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Avbildningen har programvara plan hastighet. Om du ser ”lediga” hastigheten finns utan extra kostnad för programvaran. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Vad är skillnaden mellan Microsoft.ClassicCompute och Microsoft.Compute i tjänsten förbrukad?
Microsoft.ClassicCompute representerar klassiska resurser har distribuerats via Azure Service Manager. Om du distribuerar via Resource Manager fylls Microsoft.Compute i tjänsten förbrukade. Lär dig mer om den [Azure distributionsmodeller](/azure-resource-manager/resource-manager-deployment-model.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Varför är InstanceID fältet tomt för min användning av virtuella datorer?
InstanceID-strängen är inte tillgängligt om du distribuerar via den klassiska distributionsmodellen.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Varför är taggar för Mina virtuella datorer inte flödar till användningsinformation?
Taggar endast flödas till du användning CSV: N för hanteraren för virtuella datorer bara. Klassisk resurstaggar är inte tillgängliga i användningsinformation.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hur kan Förbrukat antal vara mer än 24 timmar en dag?
I den klassiska modellen sammanställs fakturering för resurser på nivån tjänst i molnet. Om du har mer än en virtuell dator i en molnbaserad tjänst som använder samma fakturering mätaren sammanställs din användning tillsammans. Virtuella datorer som distribueras via Hanteraren för filserverresurser debiteras på VM-nivå, så den här aggregeringen inte tillämpas.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Varför är priser inte tillgängliga för DS/FS/GS/LS storlekar på prissättningssidan?
Premium-lagring kan virtuella datorer debiteras med samma hastighet som icke-premium-lagring kan virtuella datorer. Din lagringskostnader skiljer sig åt. Besök den [lagring sida med priser](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) för mer information.

## <a name="next-steps"></a>Nästa steg
Läs mer om din användningsinformation i [förstå fakturan för Microsoft Azure.](/billing/billing-understand-your-bill.md)

