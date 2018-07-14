<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Hantera tabellentiteter

Nu när du har en tabell kan vi titta på hur du hanterar enheter, eller rader i tabellen. 

En entitet kan ha upp till 255 egenskaper, inklusive 3 Systemegenskaper: **PartitionKey**, **RowKey**, och **tidsstämpel**. Du ansvarar för infogar och uppdaterar värdena för **PartitionKey** och **RowKey**. Servern hanterar värde för **tidsstämpel**, vilket kan inte ändras. Tillsammans i **PartitionKey** och **RowKey** identifiera varje entitet i en tabell.

* **PartitionKey**: Anger den partition som entiteten lagras i.
* **RowKey**: identifierar enheten inom partitionen.

Du kan definiera upp till 252 anpassade egenskaper för en entitet. 

### <a name="add-table-entities"></a>Lägg till tabellentiteter

Lägg till entiteter i en tabell med **Lägg till StorageTableRow**. De här exemplen använder partitionsnycklar med värden ”partition1” och ”partition2” och radnycklar lika delstater. Egenskaperna i varje entitet kan användarnamn och användar-ID. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Fråga tabellentiteter

Det finns flera olika sätt att fråga entiteter i en tabell.

#### <a name="retrieve-all-entities"></a>Hämta alla entiteter

Använd för att hämta alla entiteter **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Det här kommandot ger resultat som liknar följande tabell:

| användar-ID | användarnamn | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partition1 | CA |
| 3 | Christina | Partition1 | WA |
| 2 | Jessie | Partition2 | NM |
| 4 | Steven | Partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Hämta entiteter för en specifik partition

Använd för att hämta alla entiteter i en specifik partition **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Resultatet liknar följande tabell:

| användar-ID | användarnamn | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partition1 | CA |
| 3 | Christina | Partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Hämta entiteter för ett specifikt värde i en viss kolumn

Använd för att hämta entiteter där värdet i en viss kolumn är lika med ett visst värde, **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Den här frågan returnerar en post.

|Fältet|värde|
|----|----|
| användar-ID | 1 |
| användarnamn | Chris |
| PartitionKey | Partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Hämta entiteter med hjälp av ett anpassat filter 

Använd för att hämta entiteter med hjälp av ett anpassat filter **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

Den här frågan returnerar en post.

|Fältet|värde|
|----|----|
| användar-ID | 1 |
| användarnamn | Chris |
| PartitionKey | Partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Uppdaterar entiteter 

Det finns tre steg för att uppdatera entiteter. Först måste du hämta entiteten att ändra. Därefter kontrollera ändringen. Det tredje genomför ändringen med hjälp av **uppdatering AzureStorageTableRow**.

Uppdatera entiteten med användarnamn = ”Jessie” om du vill ha användarnamn = 'Jessie2'. Det här exemplet visar också ett annat sätt att skapa ett anpassat filter med hjälp av .NET-typerna. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

Jessie2 posten visas i resultaten.

|Fältet|värde|
|----|----|
| användar-ID | 2 |
| användarnamn | Jessie2 |
| PartitionKey | Partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Ta bort tabellenheter

Du kan ta bort en entitet eller alla entiteter i tabellen.

#### <a name="deleting-one-entity"></a>Tar bort en entitet

Om du vill ta bort en enda enhet, hämta en referens till entiteten och skicka det till **Remove-AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Ta bort alla entiteter i tabellen 

Om du vill ta bort alla entiteter i tabellen, hämta dem och skicka resultaten till remove-cmdlet. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```
