---
title: Förstå användningen av Azure-dator | Microsoft Docs
description: Förstå användningsinformation för virtuella datorer
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: d3e6d3c534a9295b76043dc8a6e01b9c1bb1496e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743215"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Förstå användningen av Azure-dator
Kraftfulla förbrukning insikter som kan uppnås genom att analysera dina data i Azure-användning – insikter som kan aktivera kostnad bättre hantering och tilldelning i hela organisationen. Det här dokumentet innehåller en djupdykning i din information på Azure Compute-förbrukning. För ytterligare information om allmänna Azure-användning, går du till [förstå din faktura](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

## <a name="download-your-usage-details"></a>Ladda ned användningsinformation
Börja [ladda ned användningsinformation](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). Tabellen nedan innehåller definitioner och exempel värdena för användning för virtuella datorer som distribueras via Azure Resource Manager. Det här dokumentet innehåller inte detaljerad information om virtuella datorer som distribueras via vår klassiska modellen.


| Fält             | Betydelse                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Exempelvärden                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Datum för användning         | Det datum när resursen har använts.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| Mätar-ID           | Identifierar den huvudtjänst som användningen tillhör.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | “Virtual Machines”                                                                                                                                                                                                                                                                                                                                               |
| Mätarens underkategori | Identifierare för faktureringsmätaren. <ul><li>För användning av Beräkningstimmar finns en mätare för varje VM-storlek + OS (Windows, icke-Windows) + Region.</li><li>Det finns en mätare för varje typ av programvara för användning av Premium-programvara. De flesta premium-Programvaruavbildningar har olika värden för varje kärna storlek. Mer information finns i [Prissättningssidan för Compute.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | “2005544f-659d-49c9-9094-8e0aea1be3a5”                                                                                                                                                                                                                                                                                                                           |
| Mätarnamn         | Det här är specifika för varje tjänst i Azure. För beräkning är det alltid ”Beräkningstimmar”.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | ”Beräkningstimmar”                                                                                                                                                                                                                                                                                                                                                  |
| Mätarregion       | Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret ligger.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  ”Japan, östra”                                                                                                                                                                                                                                                                                                                                                       |
| Enhet               | Identifierar i vilken enhet tjänsten debiteras. Beräkningsresurser faktureras per timme.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | ”Timmar”                                                                                                                                                                                                                                                                                                                                                          |
| Förbrukad           | Mängden den resurs som förbrukats under den dagen. För beräkning fakturerar vi för varje minut som den virtuella datorn kördes i en viss timme (upp till 6 decimaler av precision).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Resursplats  | Identifierar det datacenter där resursen körs.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | ”Japan, östra”                                                                                                                                                                                                                                                                                                                                                        |
| Förbrukad tjänst   | Tjänsten Azure-plattformen som du använde.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Resursgrupp     | Resursgruppen där den distribuerade resursen körs. Mer information finns i [översikt över Azure Resource Manager.](https://docs.microsoft.com/azure/virtual-machines/linux/vm-usage)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| Instans-ID        | Identifieraren för resursen. Identifieraren innehåller namnet du angav för resursen när den skapades. Instans-ID innehåller de SubscriptionId, ResourceGroupName och VMName för virtuella datorer (eller skalningsuppsättning namn för skala användning).                                                                                                                                                                                                                                                                                                                                                                                                                    | ”/ prenumerationer/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1”<br><br>eller<br><br>”/ prenumerationer/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1”                                                                                           |
| Taggar               | Tagg som du tilldelar till resursen. Använd taggar om du vill gruppera faktureringsposter. Lär dig hur du [tagga dina virtuella datorer.](tag.md) Detta är tillgänglig för Resource Manager virtuella datorer bara.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Ytterligare info    | Tjänstspecifik metadata. För virtuella datorer fyller vi följande data i fältet ytterligare information: <ul><li>Bild typspecifika avbildning som du körde. Hitta en fullständig lista över strängar som stöds under under bildtyper.</li><li>Service Type: storleken som du har distribuerat.</li><li>VMName: namnet på den virtuella datorn. Det här fältet fylls bara för skalningsuppsättningar i virtuella datorer. Om du behöver ditt VM-namn för VM-skaluppsättning, du kan hitta det i instans-ID-strängen ovan.</li><li>UsageType: Detta anger vilken typ av användning som representerar.<ul><li>ComputeHR är den Beräkningstimmar användningen av den underliggande virtuella datorn som Standard_D1_v2.</li><li>ComputeHR_SW är premium programvara dig om Virtuellt datorn använder premium-programmet, t.ex. Microsoft R Server.</li></ul></li></ul>    | Virtual Machines {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Virtual Machine Scale Sets {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Premium Software {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Avbildningstyp
För vissa bilder i Azure-galleriet fylls bildtypen i fältet ytterligare information. Detta gör det möjligt för användarna att förstå och spåra vad de har distribuerats på sina virtuella datorn. Följande värden som fylls i automatiskt baserat på avbildningen som du har distribuerat:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Öppna 
  - Oracle 
  - SLES för SAP 
  - SQL Server 14 Preview på Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat for SAP Business Applications     
  - Red Hat för SAP HANA 
  - Windows-klienten BYOL 
  - Windows Server BYOL 
  - Windows Server-förhandsversion 

## <a name="service-type"></a>Typ av tjänst
Fältet Typ i fältet ytterligare information som motsvarar den exakta VM-storlek som du har distribuerat. Virtuella datorer (SSD-baserad) i Premium storage och virtuella datorer (HDD-baserad) för icke-premium storage är samma pris. Om du distribuerar en SSD-baserad storlek, som Standard\_DS2\_v2, visas inte är SSD-storlek ('Standard\_D2\_virtuell v2 ”) i kolumnen underkategori för mätning och SSD-storlek (” Standard\_DS2\_ v2 ”) i fältet ytterligare information.

## <a name="region-names"></a>Regionnamn
Regionsnamn fylls i fältet resursplats i användningsinformation varierar från Regionsnamn som används i Azure Resource Manager. Här är en mappning mellan värdet region:

|    **Resource Manager Regionsnamn**       |    **Resursplats i användningsinformation**    |
|--------------------------|------------------------------------------|
|    australienöstra         |    Australien, Öst                               |
|    australiensydöstra    |    Australien, sydöstra                          |
|    brasiliensödra           |    Brasilien, Syd                              |
|    CanadaCentral         |    Kanada, centrala                            |
|    Kanadaöstra            |    Kanada, östra                               |
|    Indiencentrala          |    Indien, centrala                            |
|    usacentrala             |    Centrala USA                            |
|    chinaeast             |    Östra Kina                            |
|    chinanorth            |    Norra Kina                           |
|    eastasia              |    Östasien                             |
|    usaöstra                |    Östra USA                               |
|    usaöstra2               |    USA, östra 2                             |
|    GermanyCentral        |    Tyskland, centrala                            |
|    GermanyNortheast      |    Tyskland, nordöstra                          |
|    japanöstra             |    Japan, östra                               |
|    japanvästra             |    Japan, västra                               |
|    KoreaCentral          |    Sydkorea, centrala                            |
|    KoreaSouth            |    Sydkorea, södra                              |
|    usanorracentrala        |    Norra centrala USA                      |
|    northeurope           |    Norra Europa                          |
|    southcentralus        |    Södra centrala USA                      |
|    sydostasien         |    Sydostasien                        |
|    Indiensödra            |    Indien, södra                              |
|    UKNorth               |    USA, Norra                              |
|    södrastorbritannien               |    Storbritannien, södra                              |
|    UKSouth2              |    Storbritannien, södra 2                            |
|    västrastorbritannien                |    Storbritannien, västra                               |
|    USDoDCentral          |    US DoD, centrala                        |
|    USDoDEast             |    US DoD, östra                           |
|    USGovArizona          |    Arizona (USA-förvaltad region)                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    Texas (USA-förvaltad region)                           |
|    usgovvirginia         |    USGov Virginia                        |
|    Västra centrala USA         |    USA, västra centrala                       |
|    europavästra            |    Västra Europa                           |
|    Indienvästra             |    Indien, västra                               |
|    westus                |    Västra USA                               |
|    westus2               |    USA, västra 2                             |


## <a name="virtual-machine-usage-faq"></a>Vanliga frågor och svar för användning av virtuella datorer
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Vilka resurser debiteras när du distribuerar en virtuell dator?    
Virtuella datorer får kostnaderna för Virtuellt datorn, premium programvara som körs på den virtuella datorn, account\managed Lagringsdisken som är associerade med den virtuella datorn, och nätverkets bandbredd som överförs från den virtuella datorn.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hur vet jag om en virtuell dator använder Azure Hybrid-förmånen i CSV-filen för användning?
Om du distribuerar med hjälp av den [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/), debiteras du icke-Windows-VM-pris eftersom du tar med din egen licens till molnet. På fakturan kan du skilja som Resource Manager-VM med Azure Hybrid-förmånen eftersom de har antingen ”Windows\_Server BYOL” eller ”Windows\_klienten BYOL” i kolumnen ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hur är grundläggande vs. Standard VM-typer differentierade i CSV-filen för användning?
Både Basic och Standard virtuella datorer i A-serien erbjuder. Om du distribuerar en grundläggande virtuell dator i underkategori mätaren har strängen ”Basic”. Om du distribuerar en Standard virtuell A-serien, sedan visas virtuella datorstorlek som ”A1 VM” eftersom Standard är standard. Mer information om skillnaderna mellan Basic och Standard finns de [prissättning för](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Vad är ExtraSmall, Small, Medium, Large och ExtraLarge storlekar?
ExtraSmall – Extrastora är de tidigare namnen för Standard\_A0 – Standard\_A4. Du kan se den här konventionen används om du har distribuerat dessa storlekar i klassiska VM användningsposter.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Vad är skillnaden mellan Mätningsregion och resursplats?
Region för mätning är associerad med mätaren. För vissa Azure-tjänster som använder ett pris i alla regioner vara Mätningsregion fältet tomt. Eftersom virtuella datorer ha dedikerade priser per region för virtuella datorer, fylls det här fältet. Resursplats för virtuella datorer är på samma sätt kan den plats där den virtuella datorn har distribuerats. Azure-regioner i bägge fälten är samma, men de kan ha en annan sträng konvention för områdesnamnet.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Varför är ImageType värdet tomt i fältet ytterligare information?
ImageType fältet fylls bara för en delmängd av bilder. Om du inte har distribuerat en avbildning ovan, är ImageType tomt.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Varför är VMName tom i ytterligare information?
VMName fylls bara i fältet ytterligare information för virtuella datorer i en skalningsuppsättning. Fältet InstanceID innehåller VM-namnet för icke-skalningsuppsättningar i virtuella datorer.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Vad betyder ComputeHR i fältet UsageType i ytterligare information?
ComputeHR står för Beräkningstimme som representerar användningshändelse för kostnaden för underliggande infrastruktur. Om UsageType är ComputeHR\_SW, användningshändelse representerar en avgift för premium-programvara för den virtuella datorn.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hur vet jag om jag får betala för premium-programmet?
När utforska vilka VM-avbildning som bäst passar dina behov, måste du ta en titt på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Avbildningen har den programvara plan hastigheten. Om du ser ”kostnadsfri” för priset finns utan extra kostnad för programvaran. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Vad är skillnaden mellan Microsoft.ClassicCompute och Microsoft.Compute i tjänsten förbrukade?
Microsoft.ClassicCompute representerar klassiska resurser som har distribuerats via Azure Service Manager. Om du distribuerar via Resource Manager fylls Microsoft.Compute i förbrukad tjänst. Läs mer om den [Azure distributionsmodeller](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Varför är InstanceID fältet tomt för min användning av virtuella datorer?
InstanceID strängen är inte tillgängligt om du distribuerar via den klassiska distributionsmodellen.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Varför är taggarna för Mina virtuella datorer inte flödar till användningsinformation?
Taggar endast flödar till du användning CSV: N för Resource Manager virtuella datorer bara. Klassiska resurstaggar är inte tillgängliga i användningsinformation.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hur kan Använd kvantitet vara mer än 24 timmar en dag?
I den klassiska modellen sammanställs faktureringen för resurser på nivån tjänst i molnet. Om du har mer än en virtuell dator i en molntjänst som använder samma faktureringsmätaren sammanställs din användning tillsammans. Virtuella datorer som distribueras via Resource Manager faktureras på VM-nivå, så att den här aggregeringen inte gäller.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Varför är priser inte tillgängliga för DS/FS/GS/LS-storlekar på sidan med priser?
Premium-lagring kan virtuella datorer faktureras enligt samma taxa som icke-premium-lagring kan virtuella datorer. Endast dina lagringskostnader skiljer sig åt. Gå till den [storage-prissidan](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) för mer information.

## <a name="next-steps"></a>Nästa steg
Läs mer om användningsinformation i [förstå fakturan för Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill
)

