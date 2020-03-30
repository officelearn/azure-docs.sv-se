---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187810"
---
## <a name="repeatability-during-copy"></a>Repeterbarhet under kopiering
När du kopierar data till Azure SQL/SQL Server från andra datalager måste man ha repeterbarhet i åtanke för att undvika oavsiktliga resultat. 

När du kopierar data till Azure SQL/SQL Server Database kommer kopieringsaktivitet som standard ATT DATAUPPSÄTTNINGen till sink-tabellen som standard. När du till exempel kopierar data från en CSV-filkälla (kommaavgränsade värden) som innehåller två poster till Azure SQL/SQL Server Database, ser det så här tabellen ser ut:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Anta att du hittade fel i källfilen och uppdaterade antalet Down Tube från 2 till 4 i källfilen. Om du kör datasegmentet igen för den perioden hittar du två nya poster som läggs till i Azure SQL/SQL Server Database. Nedanstående förutsätter att ingen av kolumnerna i tabellen har den primära nyckelbegränsningen.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

För att undvika detta måste du ange UPSERT-semantik genom att utnyttja en av nedanstående 2 mekanismer som anges nedan.

> [!NOTE]
> Ett segment kan köras automatiskt i Azure Data Factory enligt den angivna principen för återförsök.
> 
> 

### <a name="mechanism-1"></a>Mekanism 1
Du kan använda **sqlWriterCleanupScript-egenskapen** för att först utföra rensningsåtgärder när ett segment körs. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Rensningsskriptet skulle köras först under kopian för ett visst segment som skulle ta bort data från SQL-tabellen som motsvarar det segmentet. Aktiviteten infogar sedan data i SQL-tabellen. 

Om segmentet nu körs igen, då hittar du kvantiteten uppdateras efter behov.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Anta att flat brickan posten tas bort från den ursprungliga csv. Sedan köra segmentet igen skulle ge följande resultat: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Inget nytt behövde göras. Kopieringsaktiviteten körde rensningsskriptet för att ta bort motsvarande data för det segmentet. Sedan läste indata från csv (som sedan innehöll endast 1 post) och infogade den i tabellen. 

### <a name="mechanism-2"></a>Mekanism 2
> [!IMPORTANT]
> sliceIdentifierColumnName stöds inte för Azure SQL Data Warehouse just nu. 

En annan mekanism för att uppnå repeterbarhet är genom att ha en dedikerad kolumn **(sliceIdentifierColumnName)** i måltabellen. Den här kolumnen används av Azure Data Factory för att säkerställa att källan och målet förblir synkroniserade. Den här metoden fungerar när det finns flexibilitet i att ändra eller definiera mål-SQL Table-schemat. 

Den här kolumnen skulle användas av Azure Data Factory för repeterbarhet och i processen Azure Data Factory kommer inte att göra några schemaändringar i tabellen. Sätt att använda denna metod:

1. Definiera en kolumn av typen binär (32) i målet SQL Table. Det bör inte finnas några begränsningar för den här kolumnen. Låt oss namnge den här kolumnen som "ColumnForADFuseOnly" för det här exemplet.
2. Använd den i kopieringsaktiviteten på följande sätt:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory fyller i den här kolumnen enligt dess behov av att säkerställa att källan och målet förblir synkroniserade. Värdena i den här kolumnen bör inte användas utanför den här kontexten av användaren. 

I likhet med mekanism 1 rensar kopiera aktivitet automatiskt först data för det angivna segmentet från mål-SQL-tabellen och kör sedan kopieringsaktiviteten normalt för att infoga data från källa till mål för det segmentet. 

