---
title: Alternativ för Azure data överföring för små data uppsättningar med låg till måttlig nätverks bandbredd | Microsoft Docs
description: Lär dig hur du väljer en Azure-lösning för data överföring när du har låg till måttlig nätverks bandbredd i din miljö och du planerar att överföra små data uppsättningar.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "60397285"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Dataöverföring för små datamängder med låg eller måttlig nätverksbandbredd
 
Den här artikeln innehåller en översikt över data överförings lösningarna när du har låg till måttlig nätverks bandbredd i din miljö och du planerar att överföra små data uppsättningar. Artikeln beskriver också rekommenderade alternativ för data överföring och respektive nyckel funktions mat ris för det här scenariot.

Om du vill veta en översikt över alla tillgängliga alternativ för data överföring går du till [Välj en Azure Data Transfer-lösning](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Scenariobeskrivning

Små data uppsättningar syftar på data storlekar i GB-ordningen till några TBs. Låg till måttlig nätverks bandbredd innebär 45 Mbit/s (T3-anslutning i Data Center) till 1 Gbit/s.

- Om du bara överför en fåtal av filer och du inte behöver automatisera data överföringen, bör du överväga verktygen med ett grafiskt gränssnitt.
- Om du är van vid system administration bör du överväga kommando rads-eller programmerings-/skript verktyg.

## <a name="recommended-options"></a>Rekommenderade alternativ

De alternativ som rekommenderas i det här scenariot är:

- **Grafiska gränssnitts verktyg** som Azure Storage Explorer och Azure Storage i Azure Portal. Detta ger ett enkelt sätt att visa dina data och snabbt överföra några få filer.

    - **Azure Storage Explorer** – med det här plattforms oberoende verktyget kan du hantera innehållet i dina Azure Storage-konton. Du kan ladda upp, ladda ned och hantera blobbar, filer, köer, tabeller och Azure Cosmos DB entiteter. Använd den med Blob Storage för att hantera blobbar och mappar, samt för att ladda upp och ladda ned blobbar mellan ditt lokala fil system och Blob Storage, eller mellan lagrings konton.
    - **Azure Portal** -Azure Storage i Azure Portal ger ett webbaserat gränssnitt för att utforska filer och överföra nya filer en i taget. Det här är ett bra alternativ om du inte vill installera några verktyg eller utfärda kommandon för att snabbt utforska dina filer, eller för att helt enkelt ladda upp en fåtal med nya.

- **Skript/programmerings verktyg** som AzCopy/PowerShell/Azure CLI och Azure Storage REST-API: er.

    - **AzCopy** – Använd det här kommando rads verktyget för att enkelt kopiera data till och från Azure-blobbar, filer och tabell lagring med optimala prestanda. AzCopy stöder samtidighet och parallellitet och möjligheten att återuppta kopierings åtgärder när de avbryts.
    - **Azure PowerShell** – för användare som är van vid system administration använder du modulen Azure Storage i Azure PowerShell för att överföra data.
    - **Azure CLI** – Använd det här plattforms oberoende verktyget för att hantera Azure-tjänster och ladda upp data till Azure Storage.
    - **Azure Storage REST-API: er/SDK: er** – när du skapar ett program kan du utveckla programmet mot Azure Storage REST-API: er/SDK: er och använda Azures klient bibliotek som erbjuds på flera olika språk.


## <a name="comparison-of-key-capabilities"></a>Jämförelse av viktiga funktioner

I följande tabell sammanfattas skillnaderna mellan viktiga funktioner.

| Funktion | Azure Lagringsutforskaren | Azure Portal | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure Storage REST-API: er eller SDK: er |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Tillgänglighet | Hämta och installera <br>Fristående verktyg | Webbaserade utforskar verktyg i Azure Portal | Kommando rads verktyg |Programmerbara gränssnitt i .NET, Java, python, Java Script, C++, go, ruby och PHP |
| Grafiskt gränssnitt | Ja | Ja | Inga | Inga |
| Plattformar som stöds | Windows, Mac, Linux | Webbaserad |Windows, Mac, Linux |Alla plattformar |
| Tillåtna Blob Storage-åtgärder<br>för blobbar och mappar | Ladda upp<br>Ladda ned<br>Hantera | Ladda upp<br>Ladda ned<br>Hantera |Ladda upp<br>Ladda ned<br>Hantera | Ja, anpassningsbar |
| Tillåten Data Lake gen1-lagring<br>åtgärder för filer och mappar | Ladda upp<br>Ladda ned<br>Hantera | Inga |Ladda upp<br>Ladda ned<br>Hantera                   | Inga |
| Tillåtna åtgärder för fil lagring<br>för filer och kataloger | Ladda upp<br>Ladda ned<br>Hantera | Ladda upp<br>Ladda ned<br>Hantera   |Ladda upp<br>Ladda ned<br>Hantera | Ja, anpassningsbar |
| Tillåtna åtgärder för tabell lagring<br>för tabeller |Hantera | Inga |Tabell stöd i AzCopy-v7 |Ja, anpassningsbar|
| Tillåten Queue Storage | Hantera | Inga  |Inga | Ja, är anpassningsbar|


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [överför data med Azure Storage Explorer](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Överföra data med AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

