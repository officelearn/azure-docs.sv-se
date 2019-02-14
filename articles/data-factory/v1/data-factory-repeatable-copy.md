---
title: Upprepningsbara kopian i Azure Data Factory | Microsoft Docs
description: Lär dig att undvika dubbletter även om en sektor som kopierar data körs mer än en gång.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fa08e6da6b87de5878f099709563fb90e8669e77
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237611"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Upprepningsbara kopian i Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Kom ihåg att undvika oväntade resultat repeterbarhet när kopiera data från relationsdata lagras. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt på något sätt, måste du se till att samma data läses oavsett hur många gånger som en sektor körs.  
 
> [!NOTE]
> Följande exempel är avsedda för Azure SQL, men kan användas för alla datalager som har stöd för rektangulära datauppsättningar. Du kan behöva justera den **typ** för källan och **fråga** egenskapen (till exempel: fråga i stället för sqlReaderQuery) för data som lagrar.   

Vanligtvis vid läsning från relationella datalager, vill du läsa endast de data som motsvarar den sektorn. Ett sätt att göra det är med hjälp av de WindowStart och WindowEnd systemvariablerna som är tillgängliga i Azure Data Factory. Läs mer om variabler och funktioner i Azure Data Factory här i den [Azure Data Factory - funktioner och systemvariabler](data-factory-functions-variables.md) artikeln. Exempel: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Den här frågan läser data som ligger i sektorn varaktighet intervallet (WindowStart -> WindowEnd) från tabellen MyTable. Kör igen för den här sektor skulle också se alltid till att samma data läses. 

I andra fall måste du kanske vill läsa hela tabellen och kan definiera sqlReaderQuery enligt följande:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Upprepningsbara som skrivs till SqlSink
När du kopierar data till **Azure SQL/SQL Server** från andra datalager, som du behöver repeterbarhet att tänka på att undvika oväntade resultat. 

När du kopierar data till Azure SQL/SQL Server-databasen, läggs kopieringsaktiviteten data till tabellen mottagare som standard. Anta att du kopierar data från en CSV (kommaavgränsad)-fil som innehåller två poster i följande tabell i en Azure SQL/SQL Server-databas. När körs en sektor kopieras två poster till SQL-tabellen. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Anta att du fel har påträffats på källfilen och uppdaterade antalet rör ned från 2 till 4. Om du kör om datasektorn under den perioden manuellt, hittar du två nya poster läggs till Azure SQL/SQL Server-databas. Det här exemplet förutsätts att ingen av kolumnerna i tabellen har primärnyckelbegränsningen.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Du måste ange UPSERT semantik genom att använda någon av följande två metoder för att undvika det här beteendet kan:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Metod 1: använda sqlWriterCleanupScript
Du kan använda den **sqlWriterCleanupScript** egenskapen att rensa data från tabellen mottagare innan du infogar data när du kör ett segment. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

När en sektor körs, kör skriptet för rensning först om du vill ta bort data som motsvarar sektorn från SQL-tabell. Kopieringsaktiviteten sedan infogar data i SQL-tabell. Om sektorn körs om antalet som uppdateras enligt önskemål.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Anta att fast bricka-posten tas bort från den ursprungliga csv. Köra sektorn resulterar i följande resultat: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Kopieringsaktivitet kördes raderingsskript för att ta bort motsvarande data för den sektorn. Sedan den läsa indata från CSV-filen (som sedan finns bara en post) och infogas i tabellen. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Metod 2: använda sliceIdentifierColumnName
> [!IMPORTANT]
> SliceIdentifierColumnName stöds för närvarande inte för Azure SQL Data Warehouse. 

Den andra metoden för att uppnå repeterbarhet är att låta en dedikerad kolumn (sliceIdentifierColumnName) i mål-tabellen. Den här kolumnen används av Azure Data Factory så att källan och målet hålla synkroniserade. Den här metoden fungerar när det finns möjligheter att ändra eller definiera målschema för SQL-tabell. 

Den här kolumnen används av Azure Data Factory för repeterbarhet och i processen för Azure Data Factory gör inte alla schemaändringar för tabellen. Sätt att använda den här metoden:

1. Definiera en kolumn av typen **binary (32)** i målet SQL-tabell. Det bör finnas utan begränsningar på den här kolumnen. Nu ska vi ge ett namn i den här kolumnen som AdfSliceIdentifier i det här exemplet.


Källtabellen:

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

2. Använd det i kopieringsaktiviteten på följande sätt:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory fyller den här kolumnen enligt dess behovet av att källan och målet vara synkroniserat. Värdena i den här kolumnen ska inte användas utanför den här kontexten. 

Liknar mekanismen 1, Kopieringsaktivitet rensas automatiskt data för givna sektorn från målet SQL-tabell. Därefter infogas data från källa i till måltabellen. 

## <a name="next-steps"></a>Nästa steg
Granska följande kopplingen artiklar som för fullständiga JSON-exempel: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
