---
title: Repeterbar kopia i Azure Data Factory
description: Lär dig hur du undviker dubbletter även om ett segment som kopierar data körs mer än en gång.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7188cb5774699fc6e31fc3b8c78068bb33c6f552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281150"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Repeterbar kopia i Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor
När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs.  
 
> [!NOTE]
> Följande exempel är för Azure SQL men gäller för alla datalager som stöder rektangulära datauppsättningar. Du kan behöva justera **typen** av källa och frågeegenskapen (till exempel frågan i stället för sqlReaderQuery) för datalagret. **query**   

När du läser från relationsarkiv vill du vanligtvis bara läsa de data som motsvarar segmentet. Ett sätt att göra det skulle vara att använda systemvariablerna WindowStart och WindowEnd som är tillgängliga i Azure Data Factory. Läs om variablerna och funktionerna i Azure Data Factory här i artikeln [Azure Data Factory - Functions and System Variables.](data-factory-functions-variables.md) Exempel: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Den här frågan läser data som ligger i segmentets varaktighetsområde (WindowStart -> WindowEnd) från tabellen MyTable. Om du kör det här segmentet igen skulle du alltid se till att samma data läss. 

I andra fall kanske du vill läsa hela tabellen och kan definiera sqlReaderQuery enligt följande:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Repeterbar skriva till SqlSink
När du kopierar data till **Azure SQL/SQL Server** från andra datalager måste du tänka på repeterbarheten för att undvika oavsiktliga resultat. 

När data kopieras till Azure SQL/SQL Server Database läggs data till sink-tabellen som standard. Du kopierar data från en CSV-fil (kommaavgränsade värden) som innehåller två poster till följande tabell i en Azure SQL/SQL Server-databas. När ett segment körs kopieras de två posterna till SQL-tabellen. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Anta att du hittade fel i källfilen och uppdaterade mängden Down Tube från 2 till 4. Om du kör datasegmentet igen för den perioden manuellt hittar du två nya poster som läggs till i Azure SQL/SQL Server Database. Det här exemplet förutsätter att ingen av kolumnerna i tabellen har primärnyckelbegränsningen.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

För att undvika detta måste du ange UPSERT-semantik med hjälp av någon av följande två mekanismer:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mekanism 1: använda sqlWriterCleanupScript
Du kan använda egenskapen **sqlWriterCleanupScript** för att rensa data från sink-tabellen innan du infogar data när ett segment körs. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

När ett segment körs körs rensningsskriptet först för att ta bort data som motsvarar segmentet från SQL-tabellen. Kopieringsaktiviteten infogar sedan data i SQL-tabellen. Om segmentet körs igen uppdateras kvantiteten efter behov.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Anta att flat brickan posten tas bort från den ursprungliga csv. Sedan köra segmentet skulle ge följande resultat: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Kopieringsaktiviteten körde rensningsskriptet för att ta bort motsvarande data för det segmentet. Sedan läste indata från csv (som sedan innehöll endast en post) och infogade den i tabellen. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mekanism 2: använda sliceIdentifierColumnName
> [!IMPORTANT]
> SegmentIdentifierColumnName stöds för närvarande inte för Azure SQL Data Warehouse. 

Den andra mekanismen för att uppnå repeterbarhet är genom att ha en dedikerad kolumn (sliceIdentifierColumnName) i måltabellen. Den här kolumnen används av Azure Data Factory för att säkerställa att källan och målet förblir synkroniserade. Den här metoden fungerar när det finns flexibilitet i att ändra eller definiera mål-SQL Table-schemat. 

Den här kolumnen används av Azure Data Factory för repeterbarhet och i processen Azure Data Factory inte göra några schemaändringar i tabellen. Sätt att använda denna metod:

1. Definiera en kolumn av typen **binär (32)** i målet SQL Table. Det bör inte finnas några begränsningar för den här kolumnen. Låt oss namnge den här kolumnen som AdfSliceIdentifier för det här exemplet.


    Källtabell:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Måltabell: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Använd den i kopieringsaktiviteten på följande sätt:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory fyller i den här kolumnen enligt dess behov av att säkerställa att källan och målet förblir synkroniserade. Värdena i den här kolumnen bör inte användas utanför den här kontexten. 

I likhet med mekanism 1 rensar kopiera aktivitet automatiskt upp data för det angivna segmentet från målet SQL Table. Data infogas sedan från källan till måltabellen. 

## <a name="next-steps"></a>Nästa steg
Läs följande kopplingsartiklar som för fullständiga JSON-exempel: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL-datalager](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
