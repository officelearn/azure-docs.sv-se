---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 042aedf1a043cd89d74ff099554642d38a3c7dd3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122997"
---
## <a name="what-is-table-storage"></a>Vad är Table Storage
Azure Table Storage lagrar stora mängder strukturerade data. Tjänsten är ett NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data. Vanliga användningsområden för Table Storage är:

* Lagring av flera TB med strukturerade data som kan serva webbaserade skalningsbara program
* Lagring av datauppsättningar som inte kräver komplexa kopplingar, sekundärnycklar eller lagrade procedurer som kan avnormaliseras för snabb åtkomst
* Ställa snabba datafrågor med hjälp av ett klustrat index
* Komma åt data med OData-protokollet och LINQ-frågor med WCF Data Service .NET-bibliotek

Du kan använda Table Storage för att lagra och fråga stora mängder strukturerad, icke-relationell data och dina tabeller skalar upp efter behov.

## <a name="table-storage-concepts"></a>Koncept för Table Storage
Table Storage består av följande komponenter:

![Komponentdiagram för Table Storage][Table1]

* **URL-format:** Azure Table Storage-konton används det här formatet: `http://<storage account>.table.core.windows.net/<table>`

  Konton för Azure Cosmos DB Tabell-API använder det här formatet: `http://<storage account>.table.cosmosdb.azure.com/<table>`  

  Du kan adressera Azure-tabeller direkt med den här adressen med OData-protokollet. Mer information finns på [OData.org][OData.org].
* **Konton:** All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål för Azure Storage](../articles/storage/common/storage-scalability-targets.md) för information om kapacitet för lagringskonton. 

    All åtkomst till Azure Cosmos DB sker via ett konto för Tabell-API. I [Skapa ett konto för Tabell-API](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account) kan du läsa mer om att skapa ett konto för Tabell-API.
* **tabellen**: En tabell är en samling entiteter. Tabeller framtvingar inte något schema på entiteter, vilket innebär att en enda tabell kan innehålla entiteter med olika egenskapsuppsättningar.  
* **Entiteten**: En entitet är en uppsättning egenskaper, liknande en databasrad. En entitet i Azure Storage kan vara upp till 1 MB. En entitet i Azure Cosmos DB kan vara upp till 2 MB.
* **Egenskaper för**: En egenskap är ett namn / värde-par. Varje entitet kan innehålla upp till 252 egenskaper för att lagra data. Varje entitet har också tre systemegenskaper som anger en partitionsnyckel, en radnyckel och en tidsstämpel. Entiteter med samma partitionsnyckel kan frågas snabbare och kan infogas/uppdateras i atomiska åtgärder. En entitets radnyckel är dess unika identifierare inom en partition.

Mer information om namngivning av tabeller och egenskaper finns i [Förstå Tabelltjänst-datamodellen](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
