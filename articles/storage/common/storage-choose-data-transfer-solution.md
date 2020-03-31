---
title: Välj en Azure-lösning för dataöverföring| Microsoft-dokument
description: Lär dig hur du väljer en Azure-lösning för dataöverföring baserat på datastorlekar och tillgänglig nätverksbandbredd i din miljö
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: ada96d0d4b167e30cb6e271aa02d0d399a9ae7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303094"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Välja en Azure-lösning för dataöverföring

Den här artikeln innehåller en översikt över några av de vanliga Azure-dataöverföringslösningarna. Artikeln länkar också ut till rekommenderade alternativ beroende på nätverksbandbredden i din miljö och storleken på de data som du tänker överföra.

## <a name="types-of-data-movement"></a>Typer av datarörelser

Dataöverföringen kan vara offline eller via nätverksanslutningen. Välj din lösning beroende på din:

- **Datastorlek** - Storleken på de data som är avsedda för överföring.
- **Överföringsfrekvens** - Intag av data med en eller längre tid och
- **Nätverk** – Bandbredd tillgänglig för dataöverföring i din miljö.

Datarörelsen kan vara av följande slag:

- **Offlineöverföring med hjälp av leveransbara enheter** – Använd fysiska leveransbara enheter när du vill göra massöverföring av engångsinformation. Microsoft skickar en disk eller en säker specialiserad enhet. Alternativt kan du köpa och skicka dina egna diskar. Du kopierar data till enheten och skickar den sedan till Azure där data överförs.  De tillgängliga alternativen för det här fallet är Data Box Disk, Data Box, Data Box Heavy och Import/Export (använd dina egna diskar).

- **Nätverksöverföring** - Du överför dina data till Azure via nätverksanslutningen. Detta kan göras på många sätt.

    - **Grafiskt gränssnitt** - Om du ibland överför bara några få filer och inte behöver automatisera dataöverföringen kan du välja ett grafiskt gränssnittsverktyg som Azure Storage Explorer eller ett webbaserat utforskningsverktyg i Azure-portalen.
    - **Skriptad eller programmatisk överföring** - Du kan använda optimerade programvaruverktyg som vi tillhandahåller eller kallar våra REST-API:er/SDK:er direkt. De skriptbara verktygen är AzCopy, Azure PowerShell och Azure CLI. För programmatiskt gränssnitt använder du en av SDK:erna för .NET, Java, Python, Node/JS, C++, Go, PHP eller Ruby.
    - **Lokala enheter** – Vi tillhandahåller en fysisk eller virtuell enhet som finns i ditt datacenter och optimerar dataöverföring över nätverket. Dessa enheter tillhandahåller också en lokal cache med filer som används ofta. Den fysiska enheten är Data Box Edge och den virtuella enheten är Data Box Gateway. Båda körs permanent i dina lokaler och ansluter till Azure via nätverket.
    - **Hanterad datapipeline** – Du kan ställa in en molnpipeline för att regelbundet överföra filer mellan flera Azure-tjänster, lokalt eller en kombination av två. Använd Azure Data Factory för att konfigurera och hantera datapipeldrar och flytta och omvandla data för analys.

Följande visuella illustrerar riktlinjerna för att välja de olika Azure-dataöverföringsverktygen beroende på vilken nätverksbandbredd som är tillgänglig för överföring, datastorlek avsedd för överföring och frekvensen av överföringen.

![Verktyg för dataöverföring i Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**De övre gränserna för offlineöverföringsenheterna - Data Box Disk, Data Box och Data Box Heavy kan utökas genom att placera flera beställningar av en enhetstyp.*

## <a name="selecting-a-data-transfer-solution"></a>Välja en dataöverföringslösning

Svara på följande frågor för att välja en dataöverföringslösning:

- Är din tillgängliga nätverksbandbredd begränsad eller obefintlig och vill du överföra stora datauppsättningar?
  
    Om ja, se: [Scenario 1: Överför stora datauppsättningar utan eller låg nätverksbandbredd](storage-solution-large-dataset-low-network.md).
- Vill du överföra stora datauppsättningar via nätverk och du har en måttlig till hög nätverksbandbredd?

    Om ja, se: [Scenario 2: Överför stora datauppsättningar med måttlig till hög nätverksbandbredd](storage-solution-large-dataset-moderate-high-network.md).
- Vill du ibland överföra bara några filer över nätverket?

    Om ja, se [scenario 3: Överför små datauppsättningar med begränsad till måttlig nätverksbandbredd](storage-solution-small-dataset-low-moderate-network.md).
- Letar du efter dataöverföring med jämna mellanrum?

    Om ja, använd de skript/programmatiska alternativ som beskrivs i [scenario 4: Periodiska dataöverföringar](storage-solution-periodic-data-transfer.md).
- Letar du efter pågående, kontinuerlig dataöverföring?

    Om ja använder du alternativen i [scenario 4: Periodiska dataöverföringar](storage-solution-periodic-data-transfer.md).

## <a name="data-transfer-feature-in-azure-portal"></a>Dataöverföringsfunktionen i Azure-portalen

Du kan också gå till ditt Azure Storage-konto i Azure-portalen och välja **dataöverföringsfunktionen.** Ange nätverksbandbredden i din miljö, storleken på de data du vill överföra och dataöverföringsfrekvensen. Du kommer att se de optimala dataöverföringslösningarna som motsvarar den information som du har angett. 

## <a name="next-steps"></a>Nästa steg

- [Få en introduktion till Azure Storage Explorer](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Läs en översikt över AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Använda Azure PowerShell med Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Snabbstart: Skapa, hämta och lista blobbar med Azure CLI](../blobs/storage-quickstart-blobs-cli.md)
- Läs om:

    - [Azure Data Box, Azure Data Box Disk och Azure Data Box Heavy för offlineöverföringar](https://docs.microsoft.com/azure/databox/).
    - [Azure Data Box Gateway och Azure Data Box Edge för onlineöverföringar](https://docs.microsoft.com/azure/databox-online/).
- [Lär dig vad som är Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Använda REST-API:erna för att överföra data

    - [I .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [I Java](https://docs.microsoft.com/java/api/overview/azure/storage)
