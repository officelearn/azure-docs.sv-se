---
title: Alternativ för Azure data överföring för stora data uppsättningar, måttlig till hög nätverks bandbredd | Microsoft Docs
description: Lär dig hur du väljer en Azure-lösning för data överföring när du har måttlig till hög nätverks bandbredd i din miljö och du planerar att överföra stora data mängder.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: c88da1c7fba5d7cb51863a580ae420eb6b3298a2
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491282"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Dataöverföring för stora datamängder med måttlig eller hög nätverksbandbredd
 
Den här artikeln innehåller en översikt över data överförings lösningarna när du har måttlig till hög nätverks bandbredd i din miljö och du planerar att överföra stora data mängder. Artikeln beskriver också rekommenderade alternativ för data överföring och respektive nyckel funktions mat ris för det här scenariot.

Om du vill veta en översikt över alla tillgängliga alternativ för data överföring går du till [Välj en Azure Data Transfer-lösning](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Scenariobeskrivning

Stora data uppsättningar syftar på data storlekar i ordningen TBs till PBs. Måttlig till hög nätverks bandbredd innebär 100 Mbit/s till 10 Gbit/s.

## <a name="recommended-options"></a>Rekommenderade alternativ

Vilka alternativ som rekommenderas i det här scenariot beror på om du har måttlig nätverks bandbredd eller hög nätverks bandbredd.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Måttlig nätverks bandbredd (100 Mbit/s – 1 Gbit/s)

Med måttlig nätverks bandbredd måste du projicera tiden för data överföring över nätverket.

Använd följande tabell för att beräkna tiden och utifrån det, Välj mellan en offline-överföring eller över nätverks överföringen. Tabellen visar den planerade tiden för nätverks data överföring för olika tillgängliga nätverks bandbredder (förutsatt 90% användning).  

![Nätverks överföring eller offline-överföring](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Om nätverks överföringen projiceras för att bli långsam bör du använda en fysisk enhet. De rekommenderade alternativen i det här fallet är frånkopplade enheter från Azure Data Box-serien eller Azure import/export med hjälp av dina egna diskar.

    - **Azure Data boxs familj för offline-överföringar** – Använd enheter från Microsoft-data Box-enhet enheter för att flytta stora mängder data till Azure när du är begränsad till tid, nätverks tillgänglighet eller kostnader. Kopiera lokala data med hjälp av verktyg som Robocopy. Beroende på data storleken som ska överföras kan du välja mellan Data Box Disk, Data Box-enhet eller Data Box Heavy.
    - **Azure import/export** – Använd Azures import-/export tjänst genom att leverera egna disk enheter för att på ett säkert sätt importera stora mängder data till Azure Blob storage och Azure Files. Den här tjänsten kan också användas för att överföra data från Azure Blob Storage till disk enheter och leverera till dina lokala platser.

- Om nätverks överföringen projiceras så att den är rimlig kan du använda något av följande verktyg som beskrivs i [hög nätverks bandbredd](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Hög nätverks bandbredd (1 Gbit/s – 100 Gbit/s)

Om den tillgängliga nätverks bandbredden är hög använder du något av följande verktyg.

- **AzCopy** – Använd det här kommando rads verktyget för att enkelt kopiera data till och från Azure-blobbar, filer och tabell lagring med optimala prestanda. AzCopy stöder samtidighet och parallellitet och möjligheten att återuppta kopierings åtgärder när de avbryts.
- **Azure Storage REST-API: er/SDK:** er – när du skapar ett program kan du utveckla programmet mot Azure Storage REST API: er och använda de Azure-SDK: er som erbjuds på flera språk.
- **Azure Data boxs familj för online-överföringar** – Data Box Edge och data Box Gateway är online nätverks enheter som kan flytta data till och från Azure. Använd Data Box Edge fysiska enheten när det finns ett samtidig behov av kontinuerlig inmatning och för bearbetning av data före uppladdning. Data Box Gateway är en virtuell version av enheten med samma funktioner för data överföring. I varje fall hanteras data överföringen av enheten.
- **Azure Data Factory** – Data Factory ska användas för att skala ut en överförings åtgärd och om det behövs för att dirigera och övervaka funktioner i företags klass. Använd Data Factory för att regelbundet överföra filer mellan flera Azure-tjänster, lokalt eller en kombination av de två. med Data Factory kan du skapa och schemalägga data drivna arbets flöden (kallas pipelines) som matar in data från olika data lager och automatiserar data förflyttning och data omvandling.

## <a name="comparison-of-key-capabilities"></a>Jämförelse av viktiga funktioner

I följande tabeller sammanfattas skillnaderna mellan viktiga funktioner för de rekommenderade alternativen.

### <a name="moderate-network-bandwidth"></a>Måttlig nätverks bandbredd

Om du använder offline-dataöverföring kan du använda följande tabell för att förstå skillnaderna mellan viktiga funktioner.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box – tung            |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Data storlek**                    |    Upp till 35 TBs                 |    Upp till 80 TBs per enhet                       |    Upp till 800 TB per enhet               |    Variabel                            |
|    **Datatyp**                    |    Azure-blobar                  |    Azure-blobar<br>Azure Files                    |    Azure-blobar<br>Azure Files            |    Azure-blobar<br>Azure Files          |
|    **Form faktor**                  |    5 SSD per order             |    1 X 50 – kg. enhet för Skriv bords storlek per beställning    |    1 X ~ 500 – kg. stor enhet per beställning    |    Upp till 10 hård diskar/SSD per beställning        |
|    **Första installations tid**               |    Låg <br>(15 minuter)            |    Låg till måttlig <br> (<30 minuter)               |    Medel<br>(1-2 timmar)               |    Måttligt till svårt<br>variabel |
|    **Skicka data till Azure**           |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    **Exportera data från Azure**           |    Nej                           |    Nej                                            |    Nej                                    |    Ja                                 |
|    **Kryptering**                   |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    **Maskinvara**                     |     Microsoft tillhandahålls          |    Microsoft tillhandahålls                            |    Microsoft tillhandahålls                    |    Kunden har tillhandahållit                   |
|    **Nätverksgränssnitt**            |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    **Partnerintegration**          |    Vissa                         |    [Hög](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Hög](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Vissa                                |
|    **Frakt**                     |    Microsoft-hanterat            |    Microsoft-hanterat                             |    Microsoft-hanterat                     |    Kund hantering                    |
| **Använd när data flyttas**     |Inom en handels avgränsning|Inom en handels avgränsning|Inom en handels avgränsning|Över geografiska gränser, t. ex. USA till EU|
|    **Prissättning**                          |    [Prissättning](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prissättning](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Om du använder data överföring online använder du tabellen i följande avsnitt för hög nätverks bandbredd.

### <a name="high-network-bandwidth"></a>Hög nätverks bandbredd

|                                     |    Verktyg AzCopy, <br>Azure PowerShell <br>Azure CLI             |    Azure Storage REST API: er, SDK: er                   |    Data Box Gateway eller Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    **Datatyp**              |    Azure-blobbar, Azure Files, Azure-tabeller    |    Azure-blobbar, Azure Files, Azure-tabeller    |    Azure-blobbar, Azure Files                           |   Stöd för 70 + data anslutningar för data lager och format    |
|    **Form faktor**            |    Kommandoradsverktyg                        |    Programmerings gränssnitt                    |    Microsoft tillhandahåller en virtuell <br>eller fysisk enhet     |    Tjänsten i Azure Portal                                            |
|    **Inledande eng ång slöin ställning** |    Överskådlig               |    Medel                       |    Enkelt (<30 minuter) till måttlig (1-2 timmar)            |    Bredd                                                          |
|    **För bearbetning av data**          |    Nej                                        |    Nej                                        |    Ja (med Edge Compute)                               |    Ja                                                                |
|    **Överföring från andra moln**   |    Nej                                        |    Nej                                        |    Nej                                                    |    Ja                                                                |
|    **Användar typ**                    |    IT-proffs eller utveckling                                       |    Dev                                       |    IT-proffs                                                |    IT-proffs                                                             |
|    **Prissättning**                      |    Kostnads fritt gäller avgifterna för utgående data         |    Kostnads fritt gäller avgifterna för utgående data         |    [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Prissättning](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du överför data med import/export](/azure/storage/common/storage-import-export-data-to-blobs).
- Förstå hur du

    - [Överför data med data Box disk](/azure/databox/data-box-disk-quickstart-portal).
    - [Överför data med data Box-enhet](/azure/databox/data-box-quickstart-portal).
- [Överför data med AZCopy](/azure/storage/common/storage-use-azcopy-v10).
- Förstå hur du:
    - [Överför data med data Box Gateway](/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformera data med data Box Edge innan du skickar dem till Azure](/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Lär dig hur du överför data med Azure Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).
- Använd REST-API: er för att överföra data

    - [I .NET](/dotnet/api/overview/azure/storage)
    - [I Java](/java/api/overview/azure/storage)
