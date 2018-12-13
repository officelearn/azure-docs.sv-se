---
title: Välj en Azure-lösning för periodiska dataöverföring | Microsoft Docs
description: Lär dig mer om att välja en Azure-lösning för att överföra data när du överför data med jämna mellanrum.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 61d3bcf8fd046ec1b68637bfdcc9827bb5b5d084
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263827"
---
# <a name="solutions-for-periodic-data-transfer"></a>Lösningar för periodiska dataöverföring
 
Den här artikeln innehåller en översikt över dataöverföringen lösningar när du överför data med jämna mellanrum. Periodiska dataöverföring via nätverket kan kategoriseras som återkommande regelbundet eller kontinuerlig dataförflyttning. Artikeln beskriver också de rekommenderade alternativ för överföring och viktig funktion för respektive matrisen för det här scenariot.

Om du vill förstå en översikt över alla tillgängliga data transfer alternativ, går till [Välj en lösning med Azure data transfer](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Rekommenderade alternativ

Alternativ som rekommenderas för periodiska dataöverföring indelade i två kategorier beroende på om överföringen är återkommande eller kontinuerlig.

- **Skripta/programmatiska verktyg** – för dataöverföring som inträffar med jämna mellanrum, Använd skriptbaserade och programmatiska verktyg, till exempel AzCopy och Azure Storage REST API: er. Dessa verktyg är riktade mot IT-proffs och utvecklare.

    - **AzCopy** – Använd det här kommandoradsverktyget enkelt kopiera data till och från Azure-Blobar, filer, och Table storage med optimala prestanda. AzCopy stöder samtidighet och parallellitet och möjligheten att återuppta kopieringsåtgärder avbruten.
    - **Azure Storage REST API: er/SDK: er** – när du skapar ett program kan du utveckla program mot Azure Storage REST API: er och använda Azure SDK: er som erbjuds på flera språk. REST-API: er kan också utnyttja Azure Storage Data Movement Library är särskilt utformat för högpresterande kopiering av data till och från Azure.

- **Verktyg för kontinuerlig datainhämtning** – för kontinuerlig, pågående datainmatning, kan du välja en av Data Box online överföringsenhet eller Azure Data Factory. Dessa verktyg har ställts in av IT-proffs och transparent kan automatisera dataöverföring.

    - **Azure Data Factory** – Data Factory ska användas för att skala ut en överföringen och om det är nödvändigt för orchestration- och enterprise graderingsberedskap övervakningsfunktionerna. Använda Azure Data Factory för att ställa in en cloud-pipeline som regelbundet överför filer mellan flera Azure-tjänster, lokala eller en kombination av båda. Azure Data Factory kan du dirigera datadrivna arbetsflöden som matar in data från olika datalager och automatisera dataförflyttning och Dataomvandling.
    - **Azure Data Box-familjen för överföring av online** -Data Box Edge och rutan Datagateway är online nätverksenheter som kan skicka data till och från Azure. Data Box Edge använder artificiell intelligens (AI)-aktiverad Edge-beräkning att Förbearbeta data innan du överför. Data Box-Gateway är en virtuell version av enheten med samma funktioner för överföring av data.


## <a name="comparison-of-key-capabilities"></a>Jämförelse av de viktigaste funktionerna

I följande tabell sammanfattas skillnaderna mellan de viktigaste funktionerna.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Skripta/Programmatic network-dataöverföringar

| Funktion                  | AzCopy                                 | Azure Storage REST API: er       |
|-----------------------------|----------------------------------------|-------------------------------|
| Formfaktor                 | Kommandoradsverktyget från Microsoft       | Kunder som utvecklar mot Storage <br> REST API: er med hjälp av Azure-klientbiblioteken |
| Enstaka installationen     | Minimalt                                | Måttlig, variabla utvecklingsarbete    |
| Dataformat                 | Azure-Blobs, Azure Files, Azure-tabeller | Azure-Blobs, Azure Files, Azure-tabeller   |
| Prestanda                 | Redan optimerats                      | Optimera när du utvecklar                  |
| Prissättning                     | Kostnadsfri, kostnaderna för utgående datatrafik gäller      | Kostnadsfri, kostnaderna för utgående datatrafik gäller        |

### <a name="continuous-data-ingestion-over-network"></a>Kontinuerlig datainmatning över nätverk

| Funktion                                       | Data Box-Gateway (förhandsversion) | Data Box Edge (förhandsversion)  | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Formfaktor                                   | Virtuell enhet             | Fysisk enhet          | Tjänsten i Azure-portalen agenten lokalt                                                            |
| Maskinvara                                      | Din hypervisor-programmet            | Tillhandahålls av Microsoft    | Ej tillämpligt                                                            |
| Den ursprungliga installationen arbete                          | Låg (< 30 minuter.)            | Måttlig (~ koppla timmar) | Stor (~ dagar)                                                 |
| Dataformat                                   | Azure-Blobs, Azure Files   | Azure-Blobs, Azure Files | [Stöder 70 + dataanslutningar för datalager och format](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Förbearbetning av data                           | Nej                         | Ja, via Edge-beräkning    | Ja                                                           |
| Lokal cache<br>(för att lagra data på plats)    | Ja                        | Ja                      | Nej                                                            |
| Överföra från andra moln                    | Nej                         | Nej                       | Ja                                                           |
| Prissättning                                       | [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Prissättning](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Prissättning](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Nästa steg

- [Överföra data med AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Mer information om data transfer med Storage REST API: er](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
- Förstå hur du:
    - [Överföra data med Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Transformera data med Data Box Edge innan du skickar till Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Lär dig att överföra data med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
