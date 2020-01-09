---
title: Parameterisera länkade tjänster i Azure Data Factory
description: Lär dig hur du Parameterisera länkade tjänster i Azure Data Factory och skickar dynamiska värden vid körning.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440100"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parameterisera länkade tjänster i Azure Data Factory

Nu kan du Parameterisera en länkad tjänst och skicka dynamiska värden vid körning. Om du till exempel vill ansluta till olika databaser på samma Azure SQL Database-Server kan du nu Parameterisera databas namnet i den länkade tjänst definitionen. Detta gör att du inte behöver skapa en länkad tjänst för varje databas på Azure SQL Database-servern. Du kan också Parameterisera andra egenskaper i den länkade tjänst definitionen, till exempel *användar namn.*

Du kan använda Data Factory gränssnittet i Azure Portal eller ett programmerings gränssnitt för att Parameterisera länkade tjänster.

> [!TIP]
> Vi rekommenderar att du inte Parameterisera lösen ord eller hemligheter. Lagra alla anslutnings strängar i Azure Key Vault i stället och Parameterisera det *hemliga namnet*.

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Datalager som stöds

För närvarande stöds länkade tjänst Parameterisering i användar gränssnittet för Data Factory i Azure Portal för följande data lager. För alla andra data lager kan du Parameterisera den länkade tjänsten genom att välja **kod** ikonen på fliken **anslutningar** och använda JSON-redigeraren.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure-databas för MySQL

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

![Lägg till dynamiskt innehåll i den länkade tjänst definitionen](media/parameterize-linked-services/parameterize-linked-services-image1.png)

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
