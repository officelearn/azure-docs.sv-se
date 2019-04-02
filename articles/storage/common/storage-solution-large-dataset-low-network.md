---
title: Alternativ för stora datauppsättningar med låg eller ingen nätverkets bandbredd för överföring av Azure data | Microsoft Docs
description: Lär dig mer om att välja en Azure-lösning för att överföra data när du har begränsad till ingen nätverksbandbredd i din miljö och du planerar att överföra stora datamängder.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 6922d49affdde86728b41ef1d3d0c6a2da5c323e
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805099"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Dataöverföring för stora datauppsättningar med låg eller ingen nätverksbandbredd
 
Den här artikeln innehåller en översikt över dataöverföringen lösningar när du har begränsad till ingen nätverksbandbredd i din miljö och du planerar att överföra stora datamängder. Artikeln beskriver också de rekommenderade alternativ för överföring och viktig funktion för respektive matrisen för det här scenariot.

Om du vill förstå en översikt över alla tillgängliga data transfer alternativ, går till [Välj en lösning med Azure data transfer](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Offline överföring eller nätverk överföring

Stora datamängder innebär att du har några TBs till några PBs av data. Du har begränsad till ingen nätverksbandbredd, nätverket går långsamt eller är instabilt. Dessutom:

- Du begränsas av kostnaderna för överföring från din Internetleverantörer (ISP).
- Säkerhets- eller organisationens principer tillåter inte utgående anslutningar när du hanterar med känsliga data.

Använda en fysisk enhet för att göra en enstaka bulk-dataöverföring i alla ovanstående instanser. Välj bland Data Box-Disk, Data Box Data Box tung enheter som tillhandahålls av Microsoft eller Import/Export med dina egna diskar.

Använd följande tabell för att bekräfta om en fysisk enhet är rätt alternativ. Den visar den beräknade tiden för överföring av data för olika tillgängliga bandbredder (förutsatt att 90% utnyttjande). Om överföring förväntas för långsamma, bör du använda en fysisk enhet.  

![Överföring eller offline överföring](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Rekommenderade alternativ

Alternativen som finns i det här scenariot finns enheter för Azure Data Box offline överföring eller Azure Import/Export.

- **Azure Data Box-familjen för överföring av offline** – använda enheter från från Microsoft Data Box-enheter för att flytta stora mängder data till Azure när du är begränsad av tid eller Nätverkstillgänglighet kostnader. Kopiera lokala data med hjälp av verktyg, till exempel Robocopy. Du kan välja från Data Box-Disk, Data Box eller Data Box tung beroende på Datastorleken är avsedd för överföring.
- **Azure Import/Export** – använda Azure Import/Export-tjänsten genom att skicka dina egna enheter på ett säkert sätt importera stora mängder data till Azure Blob storage och Azure Files. Den här tjänsten kan också användas för att överföra data från Azure Blob storage till diskenheter och leverera till dina lokala platser.

## <a name="comparison-of-key-capabilities"></a>Jämförelse av de viktigaste funktionerna

I följande tabell sammanfattas skillnaderna mellan de viktigaste funktionerna.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box – tung              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Datastorlek                        |    Upp till 35 TB                 |    Upp till 80 TB per enhet                       |    Upp till 800 TB per enhet               |    Variabel                            |
|    Datatyp                        |    Azure-Blobar                  |    Azure-Blobar<br>Azure Files                    |    Azure-Blobar<br>Azure Files            |    Azure-Blobar<br>Azure Files          |
|    Formfaktor                      |    5 SSD per beställning             |    1 x 50-lbs. Desktop medelstora enhet per beställning    |    Lbs-1 X ~ 500. stora enheter per beställning    |    Upp till 10 hårddiskar/SSD per beställning        |
|    Inledande konfigurationstid               |    Låg <br>(15 minuter)            |    Låg till måttlig <br> (< 30 minuter)               |    Mellan<br>(1 – 2 timmar)               |    Begränsad till svårt<br>(variabel) |
|    Skicka data till Azure               |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    Exportera data från Azure           |    Nej                           |    Nej                                            |    Nej                                    |    Ja                                 |
|    Kryptering                       |    AES 128-bitars                  |    AES 256-bitars                                   |    AES 256-bitars                           |    AES 128-bitars                         |
|    Maskinvara                         |     Från Microsoft          |    Från Microsoft                            |    Från Microsoft                    |    Kunder som har angetts                   |
|    Nätverksgränssnitt                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Partnerintegration              |    Vissa                         |    [Hög](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Hög](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Vissa                                |
|    Fraktas                         |    Hanteras av Microsoft            |    Hanteras av Microsoft                             |    Hanteras av Microsoft                     |    Hanteras av kunden                    |
| Använd när data flyttas         |Inom ett handel|Inom ett handel|Inom ett handel|Över geografiska gränser, t.ex. Vi kan EU|
|    Prissättning                          |    [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/disk/)                    |   [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prissättning](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Nästa steg

- Förstå hur du

    - [Överföra data med Data Box-Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Överföra data med Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Överföra data med Import/Export](/azure/storage/common/storage-import-export-data-to-blobs).
