---
title: Parameterisera länkade tjänster i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att Parameterisera länkade tjänster i Azure Data Factory och skicka dynamiska värden vid körning.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: douglasl
ms.openlocfilehash: af06be520b10184c1e96bdb27d0b8b648cde84a2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018461"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parameterisera länkade tjänster i Azure Data Factory

Du kan nu Parameterisera en länkad tjänst och skicka dynamiska värden vid körning. Om du vill ansluta till olika databaser på samma Azure SQL Database-server kan du nu Parameterisera namnet på databasen i länkade tjänstedefinition. Detta gör att du slipper skapa en länkad tjänst för varje databas i Azure SQL database-server. Du kan Parameterisera andra egenskaper i den länkade tjänstdefinitionen samt – till exempel *användarnamn.*

Du kan använda Data Factory-Användargränssnittet på Azure-portalen eller ett programmeringsgränssnitt för att Parameterisera länkade tjänster.

> [!TIP]
> Vi rekommenderar inte för att Parameterisera lösenord eller hemligheter. I stället Store alla anslutningssträngar i Azure Key Vault och Parameterisera den *hemligt namn*.

Titta på följande videoklipp för en sju minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Lagrar data som stöds

För närvarande stöds länkade tjänsten parameterisering i Användargränssnittet för Data Factory i Azure portal för följande datalager. För alla andra datalager du Parameterisera den länkade tjänsten genom att välja den **kod** -ikonen i den **anslutningar** fliken och använder JSON-redigerare.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

![Lägg till dynamiskt innehåll till länkade tjänstens definition](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Skapa en ny parameter](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
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
