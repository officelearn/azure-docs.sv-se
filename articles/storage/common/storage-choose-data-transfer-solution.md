---
title: Välj en Azure-lösning för dataöverföring | Microsoft Docs
description: Lär dig hur du väljer en Azure-lösning för att överföra data baserat på datamängder och tillgänglig nätverksbandbredd i din miljö
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/10/2018
ms.author: alkohli
ms.openlocfilehash: 4e2a182493b1e9de3d2ba9d586a9560e42fe0ecb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484084"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Välj en Azure-lösning för att överföra data

Den här artikeln innehåller en översikt över några av de vanliga Azure-dataöverföringen lösningar. Artikeln innehåller också länkar ut till rekommenderade alternativ beroende på nätverkets bandbredd i din miljö och storleken på data som du vill överföra.

## <a name="types-of-data-movement"></a>Typer av dataförflyttning

Överföra data kan vara offline eller på nätverksanslutningen. Välj din lösning beroende på din:

- **Datastorlek** -storleken på data som är avsedd för överföring
- **Överföra frekvens** -enstaka tillfällen eller regelbundet datainmatning och
- **Nätverk** – tillgänglig bandbredd för data transfer i din miljö.

Dataförflyttning kan vara av följande typer:

- **Offline överföring med hjälp av levererbara enheter** -använda fysiska levererbara enheter när du vill göra offline enstaka bulk dataöverföring. Microsoft skickar du en disk eller en säker särskild enhet. Du kan också köpa och leverera dina egna diskar. Du kopierar data till enheten och sedan skickar den till Azure där data har överförts.  De tillgängliga alternativen för det här fallet är Data Box-Disk, Data Box, Data Box tung och Import/Export (Använd dina egna diskar).

- **Network överföring** -du överför data till Azure över nätverksanslutningen. Detta kan göras på många sätt.

    - **Grafiskt gränssnitt** -om du ibland överföra några filer och behöver inte automatisera dataöverföringen, du kan välja ett grafiskt gränssnitt verktyg, till exempel Azure Storage Explorer eller en webbaserad utforskning verktyg i Azure-portalen.
    - **Skriptbaserade eller programmässiga överföring** – du kan använda optimerad programverktyg vi ge eller anropa vår REST API: er/SDK: er direkt. Tillgängliga skriptbara verktyg är AzCopy, Azure PowerShell och Azure CLI. Programmeringsgränssnitt, använder du någon av de SDK: er för .NET, Java, Python, Node/JS, C++, Go, PHP eller Ruby.
    - **Lokala enheter** – vi står en fysisk eller virtuell enhet som finns i ditt datacenter och optimerar dataöverföring via nätverket. Dessa enheter kan du även ange en lokal cache för filer som används ofta. Den fysiska enheten är Data Box-kant och den virtuella enheten är Data Box-Gateway. Både kör permanent i ditt lokala nätverk och ansluta till Azure via nätverket.
    - **Hanterade datapipeline** – du kan ställa in en cloud-pipeline för att regelbundet överföra filer mellan flera Azure-tjänster, lokala eller en kombination av två. Använd Azure Data Factory för att konfigurera och hantera datapipelines, och flytta och omvandla data för analys.

Följande visuella objekt visar riktlinjer för att välja olika Azure-dataöverföring verktyg beroende på den tillgängliga bandbredden för överföring, datastorlek avsedd för överföring och frekvensen för överföringen.

![Azure tools för dataöverföring.](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Den övre gränsen för offline överföring enheter – Data Box-Disk, Data Box och Data Box tung kan utökas genom att placera flera order av en typ av enhet.*

## <a name="selecting-a-data-transfer-solution"></a>Att välja en lösning för överföring

Besvara följande frågor för att välja en lösning för överföring av data:

- Är din tillgängliga nätverksbandbredd begränsad eller icke-existerande och du vill överföra stora datauppsättningar?
  
    Om Ja, se: [Scenario 1: Överföra stora datamängder utan eller låg nätverksbandbredd](storage-solution-large-dataset-low-network.md).
- Du vill överföra stora datamängder över nätverket och du har en måttlig till hög nätverksbandbredd?

    Om Ja, se: [Scenario 2: Överför stora datamängder med måttlig till hög nätverksbandbredd](storage-solution-large-dataset-moderate-high-network.md).
- Vill du ibland överföra några filer över nätverket?

    Om Ja, gå [Scenario 3: Överföra små datauppsättningar med begränsad till måttlig nätverksbandbredd](storage-solution-small-dataset-low-moderate-network.md).
- Letar du efter point-in-time-dataöverföring med jämna mellanrum?

    Om Ja, Använd skriptade/programmässiga alternativ som beskrivs i [Scenario 4: Periodiska dataöverföringar](storage-solution-periodic-data-transfer.md).
- Letar du efter pågående, kontinuerlig dataöverföring?

    Om Ja, Använd alternativen i [Scenario 4: Periodiska dataöverföringar](storage-solution-periodic-data-transfer.md).

## <a name="next-steps"></a>Nästa steg

- [Få en introduktion till Azure Storage Explorer](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Läs en översikt över AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Använd Azure PowerShell med Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Använda Azure CLI med Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Läs mer om:

    - [Azure Data Box, Azure Data Box-Disk och Azure Data Box tung för överföring av offline](https://docs.microsoft.com/azure/databox/).
    - [Azure Data Box-Gateway och Azure Data Box gräns för överföring av online](https://docs.microsoft.com/azure/databox-online/).
- [Lär dig vad är Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Använda REST-API: er för att överföra data

    - [I .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [I Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
