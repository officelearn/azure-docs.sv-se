---
title: Repeterbara kopian i Azure Data Factory | Microsoft Docs
description: "Lär dig att undvika dubbletter även om en sektor som kopierar data körs mer än en gång."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0519a9c60386f8fea0047a661e48f008d3141c5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Repeterbara kopian i Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Tänk på att undvika oväntade resultat repeterbarhet när kopiering av data från relationella data lagras. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera i principen för en dataset så att ett segment som körs när ett fel uppstår. När ett segment körs på något sätt, måste du kontrollera att samma data läses oavsett hur många gånger ett segment körs.  
 
> [!NOTE]
> Följande exempel gäller för alla datalager som har stöd för rektangulär datauppsättningar är för SQL Azure. Du kan behöva justera den **typen** för källa och **frågan** egenskapen (till exempel: frågan i stället sqlReaderQuery) data lagras.   

Vanligtvis vid läsning från relationella lagrar vill du läsa endast de data som motsvarar den sektorn. Ett sätt att göra det är med hjälp av WindowStart WindowEnd systemvariablerna och tillgängliga i Azure Data Factory. Läs mer om variabler och funktioner i Azure Data Factory här i den [Azure Data Factory - funktioner och systemvariabler](data-factory-functions-variables.md) artikel. Exempel: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Den här frågan läser data som faller inom sektorn varaktighet intervallet (WindowStart -> WindowEnd) från tabellen mytable prefix. Kör i det här segmentet skulle också se alltid till att samma data läses. 

I andra fall måste du kanske vill läsa hela tabellen och kan definiera sqlReaderQuery enligt följande:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Repeterbara skrivning till SqlSink
När du kopierar data till **Azure SQL/SQL Server** från andra dataarkiv som du behöver repeterbarhet Tänk på att undvika oväntade resultat. 

När du kopierar data till Azure SQL/SQL Server-databas, lägger kopieringsaktiviteten data till tabellen sink som standard. Anta att du kopierar data från en CSV-fil (fil med kommaavgränsade värden)-fil som innehåller två poster i följande tabell i Azure SQL/SQL Server-databas. När ett segment körs kopieras två poster till SQL-tabellen. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Du kanske fel har påträffats på källfilen och uppdatera antalet ned röret från 2 till 4. Om du manuellt köra datasektorn för denna period hittar du två nya poster som läggs till Azure SQL/SQL Server-databas. Det här exemplet förutsätter att ingen av kolumnerna i tabellen har primärnyckelns begränsning.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Du måste ange UPSERT semantik genom att använda någon av följande två metoder för att undvika problemet:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Metod 1: använder sqlWriterCleanupScript
Du kan använda den **sqlWriterCleanupScript** egenskapen rensa data från tabellen sink innan du infogar data när du kör ett segment. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

När ett segment körs, kör skriptet för rensning först om du vill ta bort data som motsvarar sektorn från SQL-tabellen. Kopieringsaktiviteten infogar data i SQL-tabellen. Om sektorn körs antalet uppdateras enligt önskemål.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Anta att Flat bricka-posten tas bort från den ursprungliga csv. Köra sektorn skulle skapa följande resultat: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Kopieringsaktiviteten kördes skriptet för rensning för att ta bort motsvarande data för den sektorn. Sedan den läsa indata från CSV-filen (som sedan finns bara en post) och infogas i tabellen. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Metod 2: använda sliceIdentifierColumnName
> [!IMPORTANT]
> SliceIdentifierColumnName stöds för närvarande inte för Azure SQL Data Warehouse. 

Andra mekanism för att uppnå repeterbarhet är genom att ha en dedikerad kolumn (sliceIdentifierColumnName) i mål-tabellen. Den här kolumnen kan användas av Azure Data Factory så att käll- och hålla synkroniserade. Den här metoden fungerar när det finns flexibilitet för att ändra eller definiera SQL-tabellschemat mål. 

Den här kolumnen används av Azure Data Factory för repeterbarhet och i processen Azure Data Factory gör inte några schemaändringar för tabellen. Sätt att använda den här metoden:

1. Definiera en kolumn av typen **binary (32)** i målet SQL-tabell. Det bör finnas några begränsningar på den här kolumnen. Vi namn på den här kolumnen som AdfSliceIdentifier för det här exemplet.


    Källtabellen:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Måltabellen: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

2. Använd den i kopieringsaktiviteten enligt följande:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory fylls den här kolumnen enligt dess behovet av käll- och hålls synkroniserade. Värdena i den här kolumnen ska inte användas utanför den här kontexten. 

Liknande mekanism 1, Kopieringsaktiviteten rensas automatiskt data för den angivna sektorn från målet SQL-tabell. Sedan infogas data från källa i till måltabellen. 

## <a name="next-steps"></a>Nästa steg
Granska följande kopplingen artiklar som slutförts JSON-exempel: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)