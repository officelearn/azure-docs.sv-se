---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572768"
---
## <a name="repeatability-during-copy"></a>Repeterbarhet vid kopiering
När kopiering av data till Azure SQL/SQL Server från andra data lagras måste en repeterbarhet att tänka på att undvika oväntade resultat. 

När du kopierar data till Azure SQL/SQL Server-databas, kommer kopieringsaktiviteten som standard Lägg till datauppsättningen till tabellen mottagare som standard. När du kopierar data från en CSV (fil med kommaavgränsade värden) filen datakälla som innehåller två poster till Azure SQL/SQL Server-databas, är det till exempel tabellen ser det ut:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Anta att du fel har påträffats på källfilen och uppdaterade antalet rör ned från 2 till 4 i källfilen. Om du kör datasektorn igen under den perioden, hittar du två nya poster läggs till Azure SQL/SQL Server-databas. Den nedan förutsätter att ingen av kolumnerna i tabellen har primärnyckelbegränsningen.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Om du vill undvika detta behöver du ange UPSERT semantik genom att använda en av de nedan 2 mekanismer som anges nedan.

> [!NOTE]
> Ett segment kan igen köras automatiskt i Azure Data Factory enligt återförsökspolicyn som anges.
> 
> 

### <a name="mechanism-1"></a>Metod 1
Du kan utnyttja **sqlWriterCleanupScript** egenskapen att först utföra rensning åtgärden när du kör ett segment. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Rensa-skriptet ska köras första vid kopiering för en viss sektor som skulle ta bort data från SQL-tabell som motsvarar den sektorn. Aktiviteten kommer därefter att infoga data i SQL-tabell. 

Om sektorn har nu kör igen och du hittar antalet som uppdateras som önskas.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Anta att fast bricka-posten tas bort från den ursprungliga csv. Sedan köra sektorn resulterar i följande resultat: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Inget nytt hade utförts. Kopieringsaktivitet kördes raderingsskript för att ta bort motsvarande data för den sektorn. Sedan den läsa indata från CSV-filen (som sedan finns bara 1 post) och infogas i tabellen. 

### <a name="mechanism-2"></a>Metod 2
> [!IMPORTANT]
> sliceIdentifierColumnName stöds inte för Azure SQL Data Warehouse just nu. 

En annan mekanism för att uppnå repeterbarhet är att låta en dedikerad kolumn (**sliceIdentifierColumnName**) i mål-tabellen. Den här kolumnen används av Azure Data Factory så att källan och målet hålla synkroniserade. Den här metoden fungerar när det finns möjligheter att ändra eller definiera målschema för SQL-tabell. 

Den här kolumnen används av Azure Data Factory för repeterbarhet och i processen för Azure Data Factory gör inte alla schemaändringar för tabellen. Sätt att använda den här metoden:

1. Definiera en kolumn av typen binary (32) i målet SQL-tabell. Det bör finnas utan begränsningar på den här kolumnen. Namnge vi den här kolumnen som 'ColumnForADFuseOnly ”i det här exemplet.
2. Använd det i kopieringsaktiviteten på följande sätt:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory fylls den här kolumnen enligt dess behovet av att källan och målet vara synkroniserat. Värdena i den här kolumnen bör inte användas utanför den här kontexten av användaren. 

Liknar mekanismen 1, Kopieringsaktivitet kommer automatiskt först rensa data för givna sektorn från målet SQL-tabell och kör sedan kopieringsaktiviteten normalt för att infoga data från källan till målet för den sektorn. 

