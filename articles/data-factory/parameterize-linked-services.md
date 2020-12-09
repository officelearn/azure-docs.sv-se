---
title: Parameterisera länkade tjänster i Azure Data Factory
description: Lär dig hur du Parameterisera länkade tjänster i Azure Data Factory och skickar dynamiska värden vid körning.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/09/2020
author: dcstwh
ms.author: weetok
manager: anandsub
ms.openlocfilehash: da38dd99d0f27d83d5810a664d0c05f979f47080
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920065"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parameterisera länkade tjänster i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nu kan du Parameterisera en länkad tjänst och skicka dynamiska värden vid körning. Om du till exempel vill ansluta till olika databaser på samma logiska SQL Server kan du nu Parameterisera databas namnet i den länkade tjänst definitionen. Detta gör att du inte behöver skapa en länkad tjänst för varje databas på den logiska SQL-servern. Du kan också Parameterisera andra egenskaper i den länkade tjänst definitionen, till exempel *användar namn.*

Du kan använda Data Factory gränssnittet i Azure Portal eller ett programmerings gränssnitt för att Parameterisera länkade tjänster.

> [!TIP]
> Vi rekommenderar att du inte Parameterisera lösen ord eller hemligheter. Lagra alla anslutnings strängar i Azure Key Vault i stället och Parameterisera det *hemliga namnet*.

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Länkade tjänst typer som stöds

Du kan Parameterisera alla typer av länkade tjänster.
När du redigerar en länkad tjänst i användar gränssnittet tillhandahåller Data Factory inbyggd Parameterisering-upplevelse för följande typer av länkade tjänster. I bladet skapa/redigera länkade tjänster kan du hitta alternativ för nya parametrar och lägga till dynamiskt innehåll.

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (SQL API)
- Azure Database for MySQL
- Azure Databricks
- Azure SQL Database
- Hanterad Azure SQL-instans
- Azure Synapse Analytics 
- MySQL
- Oracle
- SQL Server
- Generisk HTTP
- REST (allmän)

För andra länkade tjänst typer som inte finns med i listan ovan kan du Parameterisera den länkade tjänsten genom att redigera JSON-gränssnittet:

- I den länkade tjänsten skapa/redigera bladet > expandera "Avancerad" längst ned-> Markera kryss rutan Ange dynamiskt innehåll i JSON-format – > ange den länkade tjänstens JSON-nyttolast. 
- Eller, efter att du har skapat en länkad tjänst utan Parameterisering, i [Management Hub](author-visually.md#management-hub) -> länkade tjänster – > hitta den länkade tjänsten > Klicka på "kod" (knapp {} ) för att redigera JSON. 

Referera till avsnittet [JSON-exempel](#json) att lägga till ` parameters` för att definiera parametrar och referera till parametern med ` @{linkedService().paraName} ` .

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
