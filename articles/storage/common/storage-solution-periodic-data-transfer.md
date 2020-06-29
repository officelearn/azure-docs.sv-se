---
title: Välj en Azure-lösning för regelbunden data överföring | Microsoft Docs
description: Lär dig hur du väljer en Azure-lösning för data överföring när du överför data regelbundet.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 9ffa35e158d34a1fc6945ee2730dcf136d13edb5
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2020
ms.locfileid: "85504346"
---
# <a name="solutions-for-periodic-data-transfer"></a>Lösningar för periodisk dataöverföring
 
Den här artikeln ger en översikt över data överförings lösningarna när du överför data regelbundet. Regelbunden data överföring över nätverket kan kategoriseras som återkommande med jämna mellanrum eller kontinuerlig data förflyttning. Artikeln beskriver också rekommenderade alternativ för data överföring och respektive nyckel funktions mat ris för det här scenariot.

Om du vill veta en översikt över alla tillgängliga alternativ för data överföring går du till [Välj en Azure Data Transfer-lösning](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Rekommenderade alternativ

De rekommenderade alternativen för regelbunden data överföring delas in i två kategorier beroende på om överföringen är återkommande eller kontinuerlig.

- **Skriptbaserade/programbaserade verktyg** – för data överföring som sker med jämna mellanrum använder du skript-och programmerings verktyg som AzCopy och Azure Storage REST-API: er. Dessa verktyg är riktade mot IT-proffs och utvecklare.

    - **AzCopy** – Använd det här kommando rads verktyget för att enkelt kopiera data till och från Azure-blobbar, filer och tabell lagring med optimala prestanda. AzCopy stöder samtidighet och parallellitet och möjligheten att återuppta kopierings åtgärder när de avbryts.
    - **Azure Storage REST-API: er/SDK:** er – när du skapar ett program kan du utveckla programmet mot Azure Storage REST API: er och använda de Azure-SDK: er som erbjuds på flera språk. REST-API: er kan också utnyttja Azure Storage data flytt bibliotek som är särskilt utformade för att kopiera data till och från Azure.

- **Verktyg för kontinuerlig data inmatning** – för kontinuerlig, kontinuerlig data inmatning kan du välja en av data Box-enhet enhet för överföring online eller Azure Data Factory. Dessa verktyg konfigureras av IT-proffs och kan automatiskt automatisera data överföringen.

    - **Azure Data Factory** – Data Factory ska användas för att skala ut en överförings åtgärd och om det behövs för att dirigera och övervaka funktioner i företags klass. Använd Azure Data Factory för att konfigurera en moln pipeline som regelbundet överför filer mellan flera Azure-tjänster, lokalt eller en kombination av de två. Med Azure Data Factory kan du dirigera data drivna arbets flöden som matar in data från olika data lager och automatiserar data förflyttning och data omvandling.
    - **Azure Data Box-serien för online-överföringar** – Data Box Edge och data Box Gateway är online nätverks enheter som kan flytta data till och från Azure. Data Box Edge använder artificiell intelligens (AI)-aktive rad Edge-beräkning för att Förbearbeta data före uppladdning. Data Box Gateway är en virtuell version av enheten med samma funktioner för data överföring.


## <a name="comparison-of-key-capabilities"></a>Jämförelse av viktiga funktioner

I följande tabell sammanfattas skillnaderna mellan viktiga funktioner.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Skriptad/programmatisk nätverks data överföring

| Funktion                  | AzCopy                                 | Azure Storage REST-API: er       |
|-----------------------------|----------------------------------------|-------------------------------|
| Form faktor                 | Kommando rads verktyg från Microsoft       | Kunderna utvecklar mot lagring <br> REST API: er med Azures klient bibliotek |
| Inledande eng ång slöin ställning     | Minimal                                | Måttlig, varierande utvecklings ansträngning    |
| Data format                 | Azure-blobbar, Azure Files, Azure-tabeller | Azure-blobbar, Azure Files, Azure-tabeller   |
| Prestanda                 | Redan optimerad                      | Optimera när du utvecklar                  |
| Prissättning                     | Kostnads fritt gäller avgifterna för utgående data      | Kostnads fritt gäller avgifterna för utgående data        |

### <a name="continuous-data-ingestion-over-network"></a>Kontinuerlig data inmatning över nätverk

| Funktion                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Form faktor                                   | Virtuell enhet             | Fysisk enhet          | Tjänst i Azure Portal, agent lokalt                                                            |
| Maskinvara                                      | Din hypervisor            | Tillhandahålls av Microsoft    | NA                                                            |
| Första installations ansträngning                          | Låg (<30 minuter.)            | Måttlig (~ paret timmar) | Stor (~ dagar)                                                 |
| Data format                                   | Azure-blobbar, Azure Files   | Azure-blobbar, Azure Files | [Stöd för 70 + data anslutningar för data lager och format](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| För bearbetning av data                           | No                         | Ja, via Edge Compute    | Yes                                                           |
| Lokal cache<br>(för att lagra lokala data)    | Ja                        | Ja                      | No                                                            |
| Överföring från andra moln                    | Inga                         | Inga                       | Yes                                                           |
| Prissättning                                       | [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Prissättning](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Nästa steg

- [Överför data med AZCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Mer information om data överföring med Storage REST-API: er](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).
- Förstå hur du:
    - [Överför data med data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformera data med data Box Edge innan du skickar dem till Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Lär dig hur du överför data med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
