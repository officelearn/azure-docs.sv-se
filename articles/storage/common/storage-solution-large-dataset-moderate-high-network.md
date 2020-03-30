---
title: Azure-dataöverföringsalternativ för stora datauppsättningar, måttlig till hög nätverksbandbredd| Microsoft-dokument
description: Lär dig hur du väljer en Azure-lösning för dataöverföring när du har måttlig till hög nätverksbandbredd i din miljö och du planerar att överföra stora datauppsättningar.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: f7177a95bdd585ff2822c9ac8c94a85d12f9259b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900372"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Dataöverföring för stora datamängder med måttlig eller hög nätverksbandbredd
 
Den här artikeln innehåller en översikt över dataöverföringslösningarna när du har måttlig till hög nätverksbandbredd i din miljö och du planerar att överföra stora datauppsättningar. Artikeln beskriver också de rekommenderade dataöverföringsalternativen och respektive nyckelfunktionsmatris för det här scenariot.

Om du vill förstå en översikt över alla tillgängliga dataöverföringsalternativ går du till [Välj en Azure-dataöverföringslösning](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Scenariobeskrivning

Med stora datamängder avses datastorlekar i storleksordningen TBs till PBs. Måttlig till hög nätverksbandbredd refererar till 100 Mbit/s till 10 Gbit/s.

## <a name="recommended-options"></a>Rekommenderade alternativ

Vilka alternativ som rekommenderas i det här scenariot beror på om du har måttlig nätverksbandbredd eller hög nätverksbandbredd.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Måttlig nätverksbandbredd (100 Mbit/s – 1 Gbit/s)

Med måttlig nätverksbandbredd måste du projicera tiden för dataöverföring över nätverket.

Använd följande tabell för att uppskatta tiden och baserat på den, välj mellan en offlineöverföring eller över nätverksöverföringen. Tabellen visar den beräknade tiden för nätverksdataöverföring, för olika tillgängliga nätverksbandbredder (förutsatt att 90 % används).  

![Nätverksöverföring eller offlineöverföring](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Om nätverksöverföringen beräknas vara för långsam bör du använda en fysisk enhet. De rekommenderade alternativen i det här fallet är offlineöverföringsenheter från Azure Data Box-familjen eller Azure Import/Export med dina egna diskar.

    - **Azure Data Box-familjen för offlineöverföringar** – Använd enheter från Microsoft-levererade Data Box-enheter för att flytta stora mängder data till Azure när du är begränsad av tid, nätverkstillgänglighet eller kostnader. Kopiera lokala data med hjälp av verktyg som Robocopy. Beroende på vilken datastorlek som är avsedd för överföring kan du välja mellan Data Box Disk, Data Box eller Data Box Heavy.
    - **Azure Import/Export** – Använd Azure Import/Export-tjänsten genom att skicka egna hårddiskar för att på ett säkert sätt importera stora mängder data till Azure Blob storage och Azure Files. Den här tjänsten kan också användas för att överföra data från Azure Blob-lagring till hårddiskar och leverera till dina lokala platser.

- Om nätverksöverföringen beräknas vara rimlig kan du använda något av följande verktyg som beskrivs i [Hög nätverksbandbredd](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Hög nätverksbandbredd (1 Gbit/s – 100 Gbit/s)

Om den tillgängliga nätverksbandbredden är hög använder du något av följande verktyg.

- **AzCopy** - Använd det här kommandoradsverktyget för att enkelt kopiera data till och från Azure Blobbar, Filer och Tabelllagring med optimal prestanda. AzCopy stöder samtidighet och parallellism, och möjligheten att återuppta kopieringsåtgärder när de avbryts.
- **Azure Storage REST API:er/SDK: er** – När du skapar ett program kan du utveckla programmet mot Azure Storage REST-API:er och använda Azure SDK:er som erbjuds på flera språk.
- **Azure Data Box-familjen för onlineöverföringar** – Data Box Edge och Data Box Gateway är onlinenätverksenheter som kan flytta data till och från Azure. Använd den fysiska enheten för Data Box Edge när det finns ett samtidigt behov av kontinuerligt intag och förbehandling av data före uppladdningen. Data Box Gateway är en virtuell version av enheten med samma dataöverföringsfunktioner. I varje enskilt fall hanteras dataöverföringen av enheten.
- **Azure Data Factory** – Data Factory bör användas för att skala ut en överföringsåtgärd och om det finns ett behov av funktioner för orchestration och övervakning av företagsresultat. Använd Data Factory för att regelbundet överföra filer mellan flera Azure-tjänster, lokalt eller en kombination av de två. med Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (så kallade pipelines) som matar in data från olika datalager och automatiserar dataförflyttning och dataomvandling.

## <a name="comparison-of-key-capabilities"></a>Jämförelse av nyckelfunktioner

I följande tabeller sammanfattas skillnaderna i nyckelfunktioner för de rekommenderade alternativen.

### <a name="moderate-network-bandwidth"></a>Måttlig nätverksbandbredd

Om du använder offlinedataöverföring använder du följande tabell för att förstå skillnaderna i nyckelfunktioner.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box – tung            |    Import/Export                       |
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
|    Partnerintegration              |    Vissa                         |    [Hög](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Hög](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Vissa                                |
|    Frakt                         |    Microsoft hanterade            |    Microsoft hanterade                             |    Microsoft hanterade                     |    Hanterade kunder                    |
| Använda när data flyttas         |Inom en handelsgräns|Inom en handelsgräns|Inom en handelsgräns|Över geografiska gränser, t.ex.|
|    Prissättning                          |    [Prissättning](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prissättning](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Om du använder dataöverföring online använder du tabellen i följande avsnitt för hög nätverksbandbredd.

### <a name="high-network-bandwidth"></a>Hög nätverksbandbredd

|                                     |    Verktyg AzCopy, <br>Azure PowerShell, <br>Azure CLI             |    Azure Storage REST API:er, SDK:er                   |    Data Box Gateway eller Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Datatyp                  |    Azure Blobbar, Azure-filer, Azure-tabeller    |    Azure Blobbar, Azure-filer, Azure-tabeller    |    Azure Blobbar, Azure-filer                           |   Stöder 70+ dataanslutningar för datalager och format    |
|    Formfaktor                |    Kommandoradsverktyg                        |    Programmatiskt gränssnitt                    |    Microsoft tillhandahåller en virtuell <br>eller fysisk enhet     |    Tjänst i Azure-portalen                                            |
|    Inledande engångsinställning     |    Lätt               |    Moderera                       |    Lätt (<30 minuter) till måttlig (1-2 timmar)            |    Omfattande                                                          |
|    Förbehandling av data              |    Inga                                        |    Inga                                        |    Ja (med edge-beräkning)                               |    Ja                                                                |
|    Överföring från andra moln       |    Inga                                        |    Inga                                        |    Inga                                                    |    Ja                                                                |
|    Användartyp                        |    IT Pro eller dev                                       |    Dev                                       |    IT-proffs                                                |    IT-proffs                                                             |
|    Prissättning                          |    Gratis avgifter för utgående data tillkommer         |    Gratis avgifter för utgående data tillkommer         |    [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Prissättning](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du överför data med Import/Export](/azure/storage/common/storage-import-export-data-to-blobs).
- Förstå hur man

    - [Överför data med Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Överför data med dataruta](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Överför data med AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Förstå hur du:
    - [Överför data med Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Omvandla data med Data Box Edge innan du skickar till Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Lär dig hur du överför data med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Använda REST-API:erna för att överföra data

    - [I .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [I Java](https://docs.microsoft.com/java/api/overview/azure/storage)
