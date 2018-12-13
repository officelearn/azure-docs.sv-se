---
title: Azure-data överför alternativ för små datauppsättningar med låg till måttlig nätverksbandbredd | Microsoft Docs
description: Lär dig hur du väljer en Azure-lösning för att överföra data när du har låg till måttlig nätverksbandbredd i din miljö och du planerar att överföra små datauppsättningar.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: b9c19c4cd85b1ab2bc3ea010d029361957de3943
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53264114"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Dataöverföringen för små datauppsättningar med låg till måttlig nätverksbandbredd
 
Den här artikeln innehåller en översikt över dataöverföringen lösningar när du har låg till måttlig nätverksbandbredd i din miljö och du planerar att överföra små datauppsättningar. Artikeln beskriver också de rekommenderade alternativ för överföring och viktig funktion för respektive matrisen för det här scenariot.

Om du vill förstå en översikt över alla tillgängliga data transfer alternativ, går till [Välj en lösning med Azure data transfer](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Scenariobeskrivning

Små datauppsättningar avser datamängder i den ordning som GB till några TB. Låg till måttlig nätverksbandbredd innebär 45 Mbit/s (T3 anslutning i datacenter) till 1 Gbit/s.

- Om du överför endast en handfull filer och du behöver inte automatisera dataöverföring, Överväg verktygen med ett grafiskt gränssnitt.
- Om du är nöjd med systemadministration du kommandorad eller programmässiga/skriptverktyg.

## <a name="recommended-options"></a>Rekommenderade alternativ

De alternativ som rekommenderas i det här scenariot är:

- **Grafiska gränssnittsverktyg** , till exempel Azure Storage Explorer och Azure Storage i Azure-portalen. Detta ger ett enkelt sätt att visa dina data och snabbt överföra några filer.

    - **Azure Storage Explorer** -plattformsoberoende verktyget kan du hantera innehållet i dina Azure storage-konton. Det kan du ladda upp, ladda ned och hantera blobbar, filer, köer, tabeller och Azure Cosmos DB-entiteter. Använda den med Blob storage för att hantera blobbar och mappar, samt ladda upp och ladda ned blobar mellan ditt lokala filsystem och Blob storage, eller mellan lagringskonton.
    - **Azure-portalen** – Azure Storage i Azure-portalen tillhandahåller en webbaserat gränssnitt för att utforska filer och överföra nya filer i taget. Det här är ett bra alternativ om du inte vill installera verktyg från någon eller utfärda kommandon för att snabbt Utforska dina filer eller helt enkelt överföra en handfull nya.

- **Skript/programmatiska verktyg** , till exempel AzCopy/PowerShell/Azure CLI och Azure Storage REST API: er.

    - **AzCopy** – Använd det här kommandoradsverktyget enkelt kopiera data till och från Azure-Blobar, filer, och Table storage med optimala prestanda. AzCopy stöder samtidighet och parallellitet och möjligheten att återuppta kopieringsåtgärder avbruten.
    - **Azure PowerShell** – för användare som föredrar att systemadministration, använda Azure Storage-modulen i Azure PowerShell för att överföra data.
    - **Azure CLI** -använda plattformsoberoende verktyget för att hantera Azure-tjänster och ladda upp data till Azure Storage.
    - **Azure Storage REST API: er/SDK: er** – när du skapar ett program, kan du utveckla program mot Azure Storage REST API: er/SDK: er och använda Azure-klientbiblioteken erbjuds på flera språk.


## <a name="comparison-of-key-capabilities"></a>Jämförelse av de viktigaste funktionerna

I följande tabell sammanfattas skillnaderna mellan de viktigaste funktionerna.

| Funktion                                                            | Azure Lagringsutforskaren                    | Azure Portal  | AzCopy<br>Azure PowerShell<br>Azure CLI            | Azure Storage REST API: er eller SDK: er |
|----------------------------------------------|-------------------------------------------|--------------------------------------|-------------------|---------------------------------------|
| Tillgänglighet                                                       | Ladda ned och installera <br>Fristående verktyg | Verktyg för Webbaserad utforskning i Azure-portalen           | Kommandoradsverktyg |Programmerbara gränssnitt i .NET, Java, Python, JavaScript, C++, Go, Ruby och PHP                                                         |
| Grafiskt gränssnitt                                              | Ja                                       | Ja                                                     | Nej                | Nej                                                      |
| Plattformar som stöds                                              | Windows, Mac, Linux                       | Webbaserad     |Windows, Mac, Linux     |Alla plattformar                                                         |
| Tillåtna åtgärder för Blob storage<br>för blobbar och mappar            | Ladda upp<br>Ladda ned<br>Hantera    | Ladda upp<br>Ladda ned<br>Hantera  |Ladda upp<br>Ladda ned<br>Hantera             | Ja, anpassningsbara                                                        |
| Tillåtna Gen1 för Data Lake-lagring<br>åtgärder för filer och mappar  | Ladda upp<br>Ladda ned<br>Hantera                | Nej |Ladda upp<br>Ladda ned<br>Hantera                   |      Nej                                                   |
| Tillåtna filåtgärder för lagring<br>för filer och kataloger        | Ladda upp<br>Ladda ned<br>Hantera       | Ladda upp<br>Ladda ned<br>Hantera   |Ladda upp<br>Ladda ned<br>Hantera |                                                         |Ja, anpassningsbara
| Tillåtna åtgärder för tabeller med lagring<br>för tabeller                      |Hantera                            | Nej            |Stöd för tabellen i AzCopy v7 |Ja, anpassningsbara|
| Tillåtna kölagring                                              | Hantera                                    | Nej  |Nej | Ja, kan anpassas|


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [överföra data med Azure Storage Explorer](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Överföra data med AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

