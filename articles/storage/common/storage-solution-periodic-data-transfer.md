---
title: Välj en Azure-lösning för periodisk dataöverföring| Microsoft-dokument
description: Lär dig hur du väljer en Azure-lösning för dataöverföring när du överför data med jämna mellanrum.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: fb49802adf6242f445b700d06622d7e6aa336b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67357044"
---
# <a name="solutions-for-periodic-data-transfer"></a>Lösningar för periodisk dataöverföring
 
Den här artikeln innehåller en översikt över dataöverföringslösningarna när du överför data med jämna mellanrum. Periodisk dataöverföring över nätverket kan kategoriseras som återkommande med jämna mellanrum eller kontinuerlig dataförflyttning. Artikeln beskriver också de rekommenderade dataöverföringsalternativen och respektive nyckelfunktionsmatris för det här scenariot.

Om du vill förstå en översikt över alla tillgängliga dataöverföringsalternativ går du till [Välj en Azure-dataöverföringslösning](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Rekommenderade alternativ

De rekommenderade alternativen för periodisk dataöverföring kan delas in i två kategorier beroende på om överföringen är återkommande eller kontinuerlig.

- **Skript/programmatiska verktyg** – För dataöverföring som sker med jämna mellanrum använder du skript- och programmatiska verktyg som AzCopy- och Azure Storage REST API:er. Dessa verktyg är inriktade på IT-proffs och utvecklare.

    - **AzCopy** - Använd det här kommandoradsverktyget för att enkelt kopiera data till och från Azure Blobbar, Filer och Tabelllagring med optimal prestanda. AzCopy stöder samtidighet och parallellism, och möjligheten att återuppta kopieringsåtgärder när de avbryts.
    - **Azure Storage REST API:er/SDK: er** – När du skapar ett program kan du utveckla programmet mot Azure Storage REST-API:er och använda Azure SDK:er som erbjuds på flera språk. REST-API:erna kan också utnyttja Azure Storage Data Movement Library som utformats speciellt för högpresterande kopiering av data till och från Azure.

- **Kontinuerliga datainmatningsverktyg** – För kontinuerligt, pågående datainmatning kan du välja en av Data Box onlineöverföringsenhet eller Azure Data Factory. Dessa verktyg är konfigurerade av IT-proffs och kan öppet automatisera dataöverföring.

    - **Azure Data Factory** – Data Factory bör användas för att skala ut en överföringsåtgärd och om det finns ett behov av funktioner för orchestration och övervakning av företagsresultat. Använd Azure Data Factory för att konfigurera en molnpipeline som regelbundet överför filer mellan flera Azure-tjänster, lokala eller en kombination av de två. Med Azure Data Factory kan du dirigera datadrivna arbetsflöden som matar in data från olika datalager och automatiserar dataförflyttning och dataomvandling.
    - **Azure Data Box-familjen för onlineöverföringar** – Data Box Edge och Data Box Gateway är onlinenätverksenheter som kan flytta data till och från Azure. Data Box Edge använder artificiell intelligens (AI)-aktiverad Edge-beräkning för att förbehandla data före överföring. Data Box Gateway är en virtuell version av enheten med samma dataöverföringsfunktioner.


## <a name="comparison-of-key-capabilities"></a>Jämförelse av nyckelfunktioner

I följande tabell sammanfattas skillnaderna i nyckelfunktioner.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Skript/Programmatisk nätverksdataöverföring

| Funktion                  | AzCopy                                 | AZURE Storage REST API:er       |
|-----------------------------|----------------------------------------|-------------------------------|
| Formfaktor                 | Kommandoradsverktyg från Microsoft       | Kunderna utvecklas mot lagring <br> REST-API:er med Azure-klientbibliotek |
| Inledande engångsinställning     | Minimal                                | Måttlig, varierande utvecklingsinsats    |
| Dataformat                 | Azure Blobbar, Azure-filer, Azure-tabeller | Azure Blobbar, Azure-filer, Azure-tabeller   |
| Prestanda                 | Redan optimerad                      | Optimera när du utvecklar                  |
| Prissättning                     | Gratis avgifter för utgående data tillkommer      | Gratis avgifter för utgående data tillkommer        |

### <a name="continuous-data-ingestion-over-network"></a>Kontinuerligt datainmatning via nätverk

| Funktion                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Formfaktor                                   | Virtuell enhet             | Fysisk enhet          | Tjänst i Azure portal, agent lokalt                                                            |
| Maskinvara                                      | Din hypervisor            | Levereras av Microsoft    | Ej tillämpligt                                                            |
| Inledande inställningsinsats                          | Låg (<30 minuter.)            | Måttlig (~par timmar) | Stor (~dagar)                                                 |
| Dataformat                                   | Azure Blobbar, Azure-filer   | Azure Blobbar, Azure-filer | [Stöder 70+ dataanslutningar för datalager och format](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Förbehandling av data                           | Inga                         | Ja, via Edge-beräkning    | Ja                                                           |
| Lokal cache<br>(för att lagra lokala data)    | Ja                        | Ja                      | Inga                                                            |
| Överföring från andra moln                    | Inga                         | Inga                       | Ja                                                           |
| Prissättning                                       | [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Prissättning](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Nästa steg

- [Överför data med AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Mer information om dataöverföring med STORAGE REST API:er](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).
- Förstå hur du:
    - [Överför data med Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Omvandla data med Data Box Edge innan du skickar till Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Lär dig hur du överför data med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
