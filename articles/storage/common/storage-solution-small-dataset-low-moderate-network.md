---
title: Azure-dataöverföringsalternativ för små datauppsättningar med låg till måttlig nätverksbandbredd| Microsoft-dokument
description: Lär dig hur du väljer en Azure-lösning för dataöverföring när du har låg till måttlig nätverksbandbredd i din miljö och du planerar att överföra små datauppsättningar.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60397285"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Dataöverföring för små datamängder med låg eller måttlig nätverksbandbredd
 
Den här artikeln innehåller en översikt över dataöverföringslösningarna när du har låg till måttlig nätverksbandbredd i din miljö och du planerar att överföra små datauppsättningar. Artikeln beskriver också de rekommenderade dataöverföringsalternativen och respektive nyckelfunktionsmatris för det här scenariot.

Om du vill förstå en översikt över alla tillgängliga dataöverföringsalternativ går du till [Välj en Azure-dataöverföringslösning](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Scenariobeskrivning

Små datamängder avser datastorlekar i storleksordningen gb till några TBs. Låg till måttlig nätverksbandbredd innebär 45 Mbit/s (T3-anslutning i datacenter) till 1 Gbit/s.

- Om du bara överför en handfull filer och du inte behöver automatisera dataöverföringen bör du överväga verktygen med ett grafiskt gränssnitt.
- Om du är bekväm med systemadministration, överväga kommandorad eller programmatiska / skriptverktyg.

## <a name="recommended-options"></a>Rekommenderade alternativ

De alternativ som rekommenderas i det här scenariot är:

- **Grafiska gränssnittsverktyg** som Azure Storage Explorer och Azure Storage i Azure-portalen. Dessa ger ett enkelt sätt att visa dina data och snabbt överföra några filer.

    - **Azure Storage Explorer** - Med det här plattformsoberoende verktyget kan du hantera innehållet i dina Azure-lagringskonton. Det låter dig ladda upp, ladda ned och hantera blobbar, filer, köer, tabeller och Azure Cosmos DB-entiteter. Använd den med Blob-lagring för att hantera blobbar och mappar, samt ladda upp och hämta blobbar mellan det lokala filsystemet och Blob-lagringen eller mellan lagringskonton.
    - **Azure portal** - Azure Storage i Azure Portal tillhandahåller ett webbaserat gränssnitt för att utforska filer och ladda upp nya filer en i taget. Detta är ett bra alternativ om du inte vill installera några verktyg eller kommandon fråga för att snabbt utforska dina filer, eller att helt enkelt ladda upp en handfull nya.

- **Skript/programmatiska verktyg** som AzCopy/PowerShell/Azure CLI och Azure Storage REST API:er.

    - **AzCopy** - Använd det här kommandoradsverktyget för att enkelt kopiera data till och från Azure Blobbar, Filer och Tabelllagring med optimal prestanda. AzCopy stöder samtidighet och parallellism, och möjligheten att återuppta kopieringsåtgärder när de avbryts.
    - **Azure PowerShell** – För användare som är bekväma med systemadministration använder du Azure Storage-modulen i Azure PowerShell för att överföra data.
    - **Azure CLI** - Använd det här plattformsoberoende verktyget för att hantera Azure-tjänster och ladda upp data till Azure Storage.
    - **Azure Storage REST API:er/SDK: er** – När du skapar ett program kan du utveckla programmet mot Azure Storage REST API:er/SDK:er och använda Azure-klientbibliotek som erbjuds på flera språk.


## <a name="comparison-of-key-capabilities"></a>Jämförelse av nyckelfunktioner

I följande tabell sammanfattas skillnaderna i nyckelfunktioner.

| Funktion | Azure Lagringsutforskaren | Azure Portal | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure Storage REST API:er eller SDK:er |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Tillgänglighet | Hämta och installera <br>Fristående verktyg | Webbaserade utforskningsverktyg i Azure portal | Kommandoradsverktyget |Programmerbara gränssnitt i .NET, Java, Python, JavaScript, C++, Go, Ruby och PHP |
| Grafiskt gränssnitt | Ja | Ja | Inga | Inga |
| Plattformar som stöds | Windows, Mac, Linux | Webbaserade |Windows, Mac, Linux |Alla plattformar |
| Tillåtna Blob-lagringsåtgärder<br>för blobbar och mappar | Ladda upp<br>Ladda ned<br>Hantera | Ladda upp<br>Ladda ned<br>Hantera |Ladda upp<br>Ladda ned<br>Hantera | Ja, anpassningsbar |
| Tillåten Lagring av Data Lake Gen1<br>åtgärder för filer och mappar | Ladda upp<br>Ladda ned<br>Hantera | Inga |Ladda upp<br>Ladda ned<br>Hantera                   | Inga |
| Tillåtna fillagringsåtgärder<br>för filer och kataloger | Ladda upp<br>Ladda ned<br>Hantera | Ladda upp<br>Ladda ned<br>Hantera   |Ladda upp<br>Ladda ned<br>Hantera | Ja, anpassningsbar |
| Tillåtna tabelllagringsåtgärder<br>för tabeller |Hantera | Inga |Tabellstöd i AzCopy v7 |Ja, anpassningsbar|
| Tillåten kölagring | Hantera | Inga  |Inga | Ja, är anpassningsbar|


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [överför data med Azure Storage Explorer](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Överföra data med AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

