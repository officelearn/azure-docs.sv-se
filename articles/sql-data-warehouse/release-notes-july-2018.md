---
title: Azure SQL Data Warehouse viktig juni 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216520"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Vad är nytt i Azure SQL Data Warehouse? Juli 2018
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i juli 2018.


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Mer detaljerad information mellan regioner och server återställningar
Du kan nu återställa i regioner och servrar med hjälp av valfri återställningspunkt istället för att välja geo-redundanta säkerhetskopieringar som vidtas var 24: e timme. Mellan regioner och server återställning har stöd för både en användardefinierad eller automatisk återställningspunkter som aktiverar finare granularitet för ytterligare dataskydd. Med flera återställningspunkter är tillgängliga, du kan vara säker på att ditt informationslager är logiskt konsekvent när du återställer i olika regioner.

![Restore-kommandots – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![alternativ för återställningen – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Mer information finns i den [Accelerated och flexibel återställningspunkter](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogginlägg.

## <a name="20-minute-restorations"></a>20 minuter lång återställningar
SQL Data Warehouse erbjuder nu återställningen av alla informationslager **mindre än 20 minuter** inom samma region oavsett databasens storlek. Tiden för återställning gäller oavsett om återställningen till samma eller en annan logisk server inom samma region. Dessutom har ögonblicksbildprocessen förbättrats för att minska den tid det tar för att skapa en återställningspunkt. I lägre prestandanivåer (lägre DWU-inställningar) förbättring är en *2 x i minskad* för ögonblicksbilder skapas.

Mer information finns i den [Accelerated och flexibel återställningspunkter](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogginlägg.

## <a name="custom-restoration-configurations"></a>Återställning av den anpassade konfigurationer
Du kan nu ändra din prestandanivå (DWU) när du återställer i Azure-portalen. Du kan även återställa till en uppgraderad Gen2 data warehouse. Genom att återställa till en Gen 2-instans, kan du nu utvärdera effekten av Gen2 innan [uppgradera informationslagret Gen1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Anpassad återställning-konfiguration – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>FJÄRRPROCEDURANROPET
Den [Fjärrproceduranropet](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) lagrad procedur används ofta av verktyg för att hämta metadata om parametrar i Transact-SQL-batch. Metoden returnerar en rad för varje parameter i batch med härleda typinformation för parametern. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Resultatuppsättningen innehåller metadata om den `@id` parametern (ofullständiga resultat visas)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```