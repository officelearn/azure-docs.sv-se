---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541391"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Hantera tabellentiteter

Nu när du har en tabell kan vi titta på hur du hanterar enheter, eller rader i tabellen. 

Entiteter kan ha upp till 255 egenskaper, inklusive tre Systemegenskaper: **PartitionKey**, **RowKey**, och **tidsstämpel**. Du är ansvarig för infogar och uppdaterar värdena för **PartitionKey** och **RowKey**. Servern hanterar värde för **tidsstämpel**, vilket kan inte ändras. Tillsammans i **PartitionKey** och **RowKey** identifiera varje entitet i en tabell.

* **PartitionKey**: Anger den partition som entiteten lagras i.
* **RowKey**: Identifierar enheten inom partitionen.

Du kan definiera upp till 252 anpassade egenskaper för en entitet. 

### <a name="add-table-entities"></a>Lägg till tabellentiteter

Lägg till entiteter i en tabell med **Lägg till AzTableRow**. De här exemplen använder partitionsnycklar med värden `partition1` och `partition2`, och radnycklar lika med delstater. Egenskaperna i varje entitet är `username` och `userid`. 

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

### <a name="query-the-table-entities"></a>Fråga tabellentiteter

Du kan fråga entiteter i en tabell med hjälp av den **Get-AzTableRow** kommando.

> [!NOTE]
> Cmdletarna **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName**, och  **Get-AzureStorageTableRowByCustomFilter** är föråldrade och tas bort i en framtida version-uppdatering.

#### <a name="retrieve-all-entities"></a>Hämta alla entiteter

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Det här kommandot ger resultat som liknar följande tabell:

| userid | användarnamn | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partition1 | CA |
| 3 | Christine | Partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Hämta entiteter för en specifik partition

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Resultatet liknar följande tabell:

| userid | användarnamn | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partition1 | CA |
| 3 | Christine | Partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Hämta entiteter för ett specifikt värde i en viss kolumn

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Den här frågan returnerar en post.

|Fältet|värde|
|----|----|
| userid | 1 |
| användarnamn | Chris |
| PartitionKey | Partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Hämta entiteter med hjälp av ett anpassat filter 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Den här frågan returnerar en post.

|Fältet|värde|
|----|----|
| userid | 1 |
| användarnamn | Chris |
| PartitionKey | Partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Uppdaterar entiteter 

Det finns tre steg för att uppdatera entiteter. Först måste du hämta entiteten att ändra. Därefter kontrollera ändringen. Det tredje genomför ändringen med hjälp av **uppdatering AzTableRow**.

Uppdatera entiteten med användarnamn = ”Jessie” om du vill ha användarnamn = 'Jessie2'. Det här exemplet visar också ett annat sätt att skapa ett anpassat filter med hjälp av .NET-typerna.

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

Jessie2 posten visas i resultaten.

|Fältet|värde|
|----|----|
| userid | 2 |
| användarnamn | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Ta bort tabellenheter

Du kan ta bort en entitet eller alla entiteter i tabellen.

#### <a name="deleting-one-entity"></a>Tar bort en entitet

Om du vill ta bort en enda enhet, hämta en referens till entiteten och skicka det till **Remove-AzTableRow**.

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

Om du vill ta bort alla entiteter i tabellen, hämta dem och skicka resultaten till remove-cmdlet. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
