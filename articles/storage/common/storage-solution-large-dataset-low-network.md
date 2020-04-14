---
title: Azure-dataöverföringsalternativ för stora datauppsättningar med låg eller ingen nätverksbandbredd| Microsoft-dokument
description: Lär dig hur du väljer en Azure-lösning för dataöverföring när du har begränsat till ingen nätverksbandbredd i din miljö och du planerar att överföra stora datauppsättningar.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: be1f74dcccc654dbdd0a743d1da2da89071045f1
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253143"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Dataöverföring för stora datamängder med låg eller ingen nätverksbandbredd
 
Den här artikeln innehåller en översikt över dataöverföringslösningarna när du har begränsat till ingen nätverksbandbredd i din miljö och du planerar att överföra stora datauppsättningar. Artikeln beskriver också de rekommenderade dataöverföringsalternativen och respektive nyckelfunktionsmatris för det här scenariot.

Om du vill förstå en översikt över alla tillgängliga dataöverföringsalternativ går du till [Välj en Azure-dataöverföringslösning](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Offlineöverföring eller nätverksöverföring

Stora datamängder innebär att du har få TBs till några PBs av data. Du har begränsat till ingen nätverksbandbredd, nätverket är långsamt eller otillförlitligt. Dessutom:

- Du begränsas av kostnader för nätverksöverföring från internetleverantörerna.
- Säkerhets- eller organisationsprinciper tillåter inte utgående anslutningar när det handlar om känsliga data.

I alla ovanstående fall använder du en fysisk enhet för att göra en engångsöverföring av massdata. Välj mellan Data Box Disk, Data Box, Data Box Tunga enheter som levereras av Microsoft, eller Importera / Exportera med dina egna diskar.

Om du vill bekräfta om en fysisk enhet är rätt alternativ använder du följande tabell. Det visar den beräknade tiden för nätverksdataöverföring, för olika tillgängliga bandbredder (förutsatt 90% utnyttjande). Om nätverksöverföringen beräknas vara för långsam bör du använda en fysisk enhet.  

![Nätverksöverföring eller offlineöverföring](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Rekommenderade alternativ

De alternativ som är tillgängliga i det här scenariot är enheter för offlineöverföring i Azure Data Box eller Azure Import/Export.

- **Azure Data Box-familjen för offlineöverföringar** – Använd enheter från Microsoft-levererade Data Box-enheter för att flytta stora mängder data till Azure när du är begränsad av tid, nätverkstillgänglighet eller kostnader. Kopiera lokala data med hjälp av verktyg som Robocopy. Beroende på vilken datastorlek som är avsedd för överföring kan du välja mellan Data Box Disk, Data Box eller Data Box Heavy.
- **Azure Import/Export** – Använd Azure Import/Export-tjänsten genom att skicka egna hårddiskar för att på ett säkert sätt importera stora mängder data till Azure Blob storage och Azure Files. Den här tjänsten kan också användas för att överföra data från Azure Blob-lagring till hårddiskar och leverera till dina lokala platser.

## <a name="comparison-of-key-capabilities"></a>Jämförelse av nyckelfunktioner

I följande tabell sammanfattas skillnaderna i nyckelfunktioner.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box – tung              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Datastorlek                        |    Upp till 35 TBs                 |    Upp till 80 TBs per enhet                       |    Upp till 800 TB per enhet               |    Variabel                            |
|    Datatyp                        |    Azure-blobar                  |    Azure-blobar<br>Azure Files                    |    Azure-blobar<br>Azure Files            |    Azure-blobar<br>Azure Files          |
|    Formfaktor                      |    5 SSD per beställning             |    1 X 50 lbs. enhet i skrivbordsstorlek per beställning    |    1 X ~500 lbs. stor enhet per beställning    |    Upp till 10 hårddiskar/SSD per beställning        |
|    Inledande inställningstid               |    Låg <br>(15 minuter)            |    Låg till måttlig <br> (<30 minuter)               |    Moderera<br>(1-2 timmar)               |    Måttlig till svår<br>(variabel) |
|    Skicka data till Azure               |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    Exportera data från Azure           |    Inga                           |    Inga                                            |    Inga                                    |    Ja                                 |
|    Kryptering                       |    AES 128-bitars                  |    AES 256-bitars                                   |    AES 256-bitars                           |    AES 128-bitars                         |
|    Maskinvara                         |     Microsoft levereras          |    Microsoft levereras                            |    Microsoft levereras                    |    Kunden levereras                   |
|    Nätverksgränssnitt                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Partnerintegration              |    Vissa                         |    [Hög](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Hög](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Vissa                                |
|    Frakt                         |    Microsoft hanterade            |    Microsoft hanterade                             |    Microsoft hanterade                     |    Hanterade kunder                    |
| Använda när data flyttas         |Inom en handelsgräns|Inom en handelsgräns|Inom en handelsgräns|Över geografiska gränser, t.ex.|
|    Prissättning                          |    [Prissättning](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prissättning](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Nästa steg

- Förstå hur man

    - [Överför data med Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Överför data med dataruta](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Överför data med Import/Export](/azure/storage/common/storage-import-export-data-to-blobs).
