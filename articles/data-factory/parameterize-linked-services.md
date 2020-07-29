---
title: Parameterisera länkade tjänster i Azure Data Factory
description: Lär dig hur du Parameterisera länkade tjänster i Azure Data Factory och skickar dynamiska värden vid körning.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 85689661e7f0d170cd88edde8985f46285e679c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987782"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parameterisera länkade tjänster i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nu kan du Parameterisera en länkad tjänst och skicka dynamiska värden vid körning. Om du till exempel vill ansluta till olika databaser på samma logiska SQL Server kan du nu Parameterisera databas namnet i den länkade tjänst definitionen. Detta gör att du inte behöver skapa en länkad tjänst för varje databas på den logiska SQL-servern. Du kan också Parameterisera andra egenskaper i den länkade tjänst definitionen, till exempel *användar namn.*

Du kan använda Data Factory gränssnittet i Azure Portal eller ett programmerings gränssnitt för att Parameterisera länkade tjänster.

> [!TIP]
> Vi rekommenderar att du inte Parameterisera lösen ord eller hemligheter. Lagra alla anslutnings strängar i Azure Key Vault i stället och Parameterisera det *hemliga namnet*.

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Datalager som stöds

För tillfället stöds länkade tjänst Parameterisering i Data Factory användar gränssnitt för följande data lager. För alla andra data lager kan du Parameterisera den länkade tjänsten genom att välja **kod** ikonen på fliken **anslutningar** och använda JSON-redigeraren.

- Amazon Redshift
- Azure Cosmos DB (SQL API)
- Azure Database for MySQL
- Azure SQL Database
- Azure Synapse Analytics (tidigare SQL DW)
- MySQL
- Oracle
- SQL Server

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
