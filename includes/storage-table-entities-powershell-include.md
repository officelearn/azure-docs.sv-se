---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187018"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Hantera tabellentiteter

Nu när du har en tabell ska vi titta på hur du hanterar entiteter eller rader i tabellen. 

Entiteter kan ha upp till 255 egenskaper, inklusive tre systemegenskaper: **PartitionKey**, **RowKey**och **Timestamp**. Du ansvarar för att infoga och uppdatera värdena för **PartitionKey** och **RowKey**. Servern hanterar värdet för **Tidsstämpel**, som inte kan ändras. Tillsammans identifierar **PartitionKey** och **RowKey** unikt varje entitet i en tabell.

* **PartitionKey**: Bestämmer den partition som entiteten lagras i.
* **RowKey**: Identifierar unikt entiteten i partitionen.

Du kan definiera upp till 252 anpassade egenskaper för en entitet. 

### <a name="add-table-entities"></a>Lägga till tabellentiteter

Lägg till entiteter i en tabell med **Add-AzTableRow**. I de här exemplen används partitionsnycklar med värden `partition1` och `partition2`, och radtangenter som är lika med tillståndsförkortningar. Egenskaperna i varje `username` entitet är och `userid`. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Fråga tabellenheter

Du kan fråga entiteterna i en tabell med kommandot **Get-AzTableRow.**

> [!NOTE]
> Cmdlets **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName**och **Get-AzureStorageTableRowByCustomFilter** är inaktuella och tas bort i en framtida versionsuppdatering.

#### <a name="retrieve-all-entities"></a>Hämta alla entiteter

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Det här kommandot ger resultat som liknar följande tabell:

| userid | användarnamn | Partition | radnyckel |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Hämta entiteter för en viss partition

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Resultaten liknar följande tabell:

| userid | användarnamn | Partition | radnyckel |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Hämta entiteter för ett visst värde i en viss kolumn

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Den här frågan hämtar en post.

|fält|värde|
|----|----|
| userid | 1 |
| användarnamn | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Hämta entiteter med ett anpassat filter 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Den här frågan hämtar en post.

|fält|värde|
|----|----|
| userid | 1 |
| användarnamn | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Uppdatera entiteter 

Det finns tre steg för att uppdatera entiteter. Hämta först entiteten för att ändra. För det andra, gör ändringen. För det tredje, begå ändringen med **Update-AzTableRow**.

Uppdatera entiteten med användarnamn = 'Jessie' för att ha användarnamn = 'Jessie2'. I det här exemplet visas också ett annat sätt att skapa ett anpassat filter med .NET-typer.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

Resultaten visar Jessie2-posten.

|fält|värde|
|----|----|
| userid | 2 |
| användarnamn | Jessie2 (på andra) |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Ta bort tabellentiteter

Du kan ta bort en entitet eller alla entiteter i tabellen.

#### <a name="deleting-one-entity"></a>Ta bort en entitet

Om du vill ta bort en enskild entitet hämtar du en referens till den entiteten och rör den till **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Ta bort alla entiteter i tabellen

Om du vill ta bort alla entiteter i tabellen hämtar du dem och piper resultaten till den bort ta bort cmdleten. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
