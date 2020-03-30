---
title: Parameterisera länkade tjänster i Azure Data Factory
description: Lär dig hur du parameteriserar länkade tjänster i Azure Data Factory och skickar dynamiska värden vid körning.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: acc7284eb607d20ca1d62b478d802be56048bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440100"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parameterisera länkade tjänster i Azure Data Factory

Du kan nu parameterisera en länkad tjänst och skicka dynamiska värden vid körning. Om du till exempel vill ansluta till olika databaser på samma Azure SQL Database-server kan du nu parameterisera databasnamnet i den länkade tjänstdefinitionen. Detta förhindrar att du behöver skapa en länkad tjänst för varje databas på Azure SQL-databasservern. Du kan parametriera andra egenskaper i den länkade tjänstdefinitionen också, till exempel *användarnamn.*

Du kan använda datafabriksgränssnittet i Azure-portalen eller ett programmeringsgränssnitt för att parameterisera länkade tjänster.

> [!TIP]
> Vi rekommenderar att du inte parameteriserar lösenord eller hemligheter. Lagra alla anslutningssträngar i Azure Key Vault i stället och parametrin det *hemliga namnet*.

För en sju minuters introduktion och demonstration av den här funktionen, titta på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Datalager som stöds

För närvarande stöds länkad tjänstparametrisering i datafabriksgränssnittet i Azure-portalen för följande datalager. För alla andra datalager kan du parameterisera den länkade tjänsten genom att välja **kodikonen** på fliken **Anslutningar** och använda JSON-redigeraren.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

![Lägga till dynamiskt innehåll i definitionen av länkad tjänst](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Skapa en ny parameter](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
