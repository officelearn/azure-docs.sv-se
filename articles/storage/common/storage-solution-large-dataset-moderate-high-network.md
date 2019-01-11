---
title: Alternativ för stora datauppsättningar, begränsad till hög bandbredd för överföring av Azure data | Microsoft Docs
description: Lär dig hur du väljer en Azure-lösning för att överföra data när du har begränsad till hög nätverksbandbredd i din miljö och du planerar att överföra stora datamängder.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: bc5668d826395fb71ee70907f095303a43f1ec7f
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214325"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Dataöverföringen för stora datauppsättningar med måttlig till hög nätverksbandbredd
 
Den här artikeln innehåller en översikt över dataöverföringen lösningar när du har begränsad till hög nätverksbandbredd i din miljö och du planerar att överföra stora datamängder. Artikeln beskriver också de rekommenderade alternativ för överföring och viktig funktion för respektive matrisen för det här scenariot.

Om du vill förstå en översikt över alla tillgängliga data transfer alternativ, går till [Välj en lösning med Azure data transfer](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Scenariobeskrivning

Stora datamängder referera till datamängder i den ordning som TBs till PBs. Måttlig till hög nätverksbandbredd refererar till 100 Mbit/s till 10 Gbit/s.

## <a name="recommended-options"></a>Rekommenderade alternativ

Vilka alternativ som rekommenderas i det här scenariot beror på om du har begränsad bandbredd eller hög nätverksbandbredd.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Måttlig nätverksbandbredd (100 Mbit/s - 1 Gbit/s)

Med begränsad nätverksbandbredd måste du projektet tiden för att överföra data över nätverket.

Använd följande tabell för att beräkna tid och baserat på att välja mellan en offline överföring eller över nätverksöverföringen. Tabellen visar den beräknade tiden för överföring av data för olika tillgängliga nätverksbandbredd (förutsatt att 90% utnyttjande).  

![Överföring eller offline överföring](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Om nätverksöverföringen förväntas för långsamma, bör du använda en fysisk enhet. Alternativ som rekommenderas är i det här fallet offline överföring enheter från Azure Data Box-serien eller Azure Import/Export med dina egna diskar.

    - **Azure Data Box-familjen för överföring av offline** – använda enheter från från Microsoft Data Box-enheter för att flytta stora mängder data till Azure när du är begränsad av tid eller Nätverkstillgänglighet kostnader. Kopiera lokala data med hjälp av verktyg, till exempel Robocopy. Du kan välja från Data Box-Disk, Data Box eller Data Box tung beroende på Datastorleken är avsedd för överföring.
    - **Azure Import/Export** – använda Azure Import/Export-tjänsten genom att skicka dina egna enheter på ett säkert sätt importera stora mängder data till Azure Blob storage och Azure Files. Den här tjänsten kan också användas för att överföra data från Azure Blob storage till diskenheter och leverera till dina lokala platser.

- Om nätverksöverföringen förväntas vara rimligt så du kan använda någon av följande verktyg som beskrivs i [hög nätverksbandbredd](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Hög nätverksbandbredd (1 Gbit/s - 100 Gbit/s)

Om den tillgängliga nätverksbandbredden är hög, använder du något av följande verktyg.

- **AzCopy** – Använd det här kommandoradsverktyget enkelt kopiera data till och från Azure-Blobar, filer, och Table storage med optimala prestanda. AzCopy stöder samtidighet och parallellitet och möjligheten att återuppta kopieringsåtgärder avbruten.
- **Azure Storage REST API: er/SDK: er** – när du skapar ett program kan du utveckla program mot Azure Storage REST API: er och använda Azure SDK: er som erbjuds på flera språk.
- **Azure Data Box-familjen för överföring av online** – Data Box Edge och rutan Datagateway är online nätverksenheter som kan skicka data till och från Azure. Använd Data Box Edge fysisk enhet när det finns en samtidig behöver för kontinuerlig datainmatning och förbearbetning av data före att ladda upp. Data Box-Gateway är en virtuell version av enheten med samma funktioner för överföring av data. I båda fallen ska hanteras dataöverföringen av enheten.
- **Azure Data Factory** – Data Factory ska användas för att skala ut en överföringen och om det är nödvändigt för orchestration- och enterprise graderingsberedskap övervakningsfunktionerna. Använd Data Factory för att regelbundet överför filer mellan flera Azure-tjänster, lokala eller en kombination av båda. med Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som matar in data från olika datalager och automatisera dataförflyttning och Dataomvandling.

## <a name="comparison-of-key-capabilities"></a>Jämförelse av de viktigaste funktionerna

Följande tabeller sammanfattar skillnaderna i viktiga funktioner för alternativ som rekommenderas.

### <a name="moderate-network-bandwidth"></a>Måttlig nätverksbandbredd

Om du använder offline dataöverföring, information om skillnaderna i viktiga funktioner med hjälp av följande tabell.

|                                     |    Data Box-Disk (förhandsversion)    |    Data Box                                      |    Data Box tung (förhandsversion)              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Datastorlek                        |    Upp till 35 TB                 |    Upp till 80 TB per enhet                       |    Upp till 800 TB per enhet               |    Variabel                            |
|    Datatyp                        |    Azure-Blobar                  |    Azure-Blobar<br>Azure Files                    |    Azure-Blobar<br>Azure Files            |    Azure-Blobar<br>Azure Files          |
|    Formfaktor                      |    5 SSD per beställning             |    1 x 50-lbs. Desktop medelstora enhet per beställning    |    Lbs-1 X ~ 500. stora enheter per beställning    |    Upp till 10 hårddiskar/SSD per beställning        |
|    Inledande konfigurationstid               |    Låg <br>(15 minuter)            |    Låg till måttlig <br> (< 30 minuter)               |    Mellan<br>(1 – 2 timmar)               |    Begränsad till svårt<br>(variabel) |
|    Skicka data till Azure               |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    Exportera data från Azure           |    Nej                           |    Nej                                            |    Nej                                    |    Ja                                 |
|    Kryptering                       |    AES 128-bitars                  |    AES 256-bitars                                   |    AES 256-bitars                           |    AES 128-bitars                         |
|    Maskinvara                         |     Från Microsoft          |    Från Microsoft                            |    Från Microsoft                    |    Kunder som har angetts                   |
|    Nätverksgränssnitt                |    USB-3.1/SATA                 |    RJ-45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    Partnerintegration              |    Vissa                         |    [Hög](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Hög](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Vissa                                |
|    Fraktas                         |    Hanteras av Microsoft            |    Hanteras av Microsoft                             |    Hanteras av Microsoft                     |    Hanteras av kunden                    |
| Använd när data flyttas         |Inom ett handel|Inom ett handel|Inom ett handel|Över geografiska gränser, t.ex. Vi kan EU|
|    Prissättning                          |    [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/disk/)                    |   [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prissättning](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Om du använder online dataöverföring, Använd tabell i följande avsnitt för hög nätverksbandbredd.

### <a name="high-network-bandwidth"></a>Hög nätverksbandbredd

|                                     |    Verktyg för AzCopy <br>Azure PowerShell <br>Azure CLI             |    Azure Storage REST API: er, SDK: er                   |    Data Box-Gateway eller Data Box Edge (förhandsversion)           |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Datatyp                  |    Azure-Blobs, Azure Files, Azure-tabeller    |    Azure-Blobs, Azure Files, Azure-tabeller    |    Azure-Blobs, Azure Files                           |   Stöder 70 + dataanslutningar för datalager och format    |
|    Formfaktor                |    Kommandoradsverktyg                        |    Programmeringsgränssnitt                    |    Microsoft tillhandahåller en virtuell <br>eller fysisk enhet     |    Tjänsten i Azure portal                                            |
|    Enstaka installationen     |    Enkelt               |    Mellan                       |    Enkelt (< 30 minuter) till måttlig (1 – 2 timmar)            |    Omfattande                                                          |
|    Förbearbetning av data              |    Nej                                        |    Nej                                        |    Ja (med Edge-beräkning)                               |    Ja                                                                |
|    Överföra från andra moln       |    Nej                                        |    Nej                                        |    Nej                                                    |    Ja                                                                |
|    Användartyp                        |    IT Pro eller utveckling                                       |    Dev                                       |    IT-proffs                                                |    IT-proffs                                                             |
|    Prissättning                          |    Kostnadsfri, kostnaderna för utgående datatrafik gäller         |    Kostnadsfri, kostnaderna för utgående datatrafik gäller         |    [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Prissättning](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du överför data med Import/Export](/azure/storage/common/storage-import-export-data-to-blobs).
- Förstå hur du

    - [Överföra data med Data Box-Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Överföra data med Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Överföra data med AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Förstå hur du:
    - [Överföra data med Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformera data med Data Box Edge innan du skickar till Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Lär dig att överföra data med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Använda REST-API: er för att överföra data

    - [I .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [I Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)