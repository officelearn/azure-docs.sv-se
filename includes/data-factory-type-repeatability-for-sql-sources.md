## <a name="repeatability-during-copy"></a>Repeterbarhet vid kopiering
När kopiering av data till Azure SQL/SQL Server från andra data lagras måste en repeterbarhet Tänk på att undvika oväntade resultat. 

När du kopierar data till Azure SQL/SQL Server-databas kommer kopieringsaktiviteten som standard APPEND datauppsättningen till tabellen mottagare som standard. När du kopierar data från en CSV (fil med kommaavgränsade värden) filen datakälla som innehåller två poster till Azure SQL/SQL Server-databas, är det till exempel tabellen ser ut:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Du kanske fel har påträffats på källfilen och uppdatera antalet ned röret från 2 till 4 i källfilen. Om du kör nytt datasektorn för denna period, hittar du två nya poster som läggs till Azure SQL/SQL Server-databas. Den nedan förutsätter att ingen av kolumnerna i tabellen har primärnyckelns begränsning.

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
> En sektor går att köra automatiskt i Azure Data Factory enligt återförsökspolicyn som anges.
> 
> 

### <a name="mechanism-1"></a>Mekanism 1
Du kan utnyttja **sqlWriterCleanupScript** egenskapen att utföra rensning av åtgärden först när du kör ett segment. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Skriptet för rensning skulle köras första vid kopiering av för ett visst segment som skulle ta bort data från SQL-tabell som motsvarar den sektorn. Aktiviteten kommer därefter infoga data i SQL-tabellen. 

Om sektorn är nu kör igen och du hittar antalet uppdateras som önskade.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Anta att Flat bricka-posten tas bort från den ursprungliga csv. Köra sektorn igen skulle skapa följande resultat: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Inget nytt var du tvungen att. Kopieringsaktiviteten kördes skriptet för rensning för att ta bort motsvarande data för den sektorn. Sedan den läsa indata från CSV-filen (som sedan finns endast 1 post) och infogas i tabellen. 

### <a name="mechanism-2"></a>Metod 2
> [!IMPORTANT]
> sliceIdentifierColumnName stöds inte för Azure SQL Data Warehouse just nu. 

En annan metod för att uppnå repeterbarhet av är att ha en dedikerad kolumn (**sliceIdentifierColumnName**) i mål-tabellen. Den här kolumnen kan användas av Azure Data Factory så att käll- och hålla synkroniserade. Den här metoden fungerar när det finns flexibilitet för att ändra eller definiera SQL-tabellschemat mål. 

Den här kolumnen som ska användas av Azure Data Factory för repeterbarhet och i processen kommer Azure Data Factory inte göra några schemaändringar i tabellen. Sätt att använda den här metoden:

1. Definiera en kolumn av typen binary (32) i målet SQL-tabell. Det bör finnas några begränsningar på den här kolumnen. Vi namn i den här kolumnen som 'ColumnForADFuseOnly' för det här exemplet.
2. Använd den i kopieringsaktiviteten enligt följande:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory kommer att fylla i den här kolumnen enligt dess behovet av käll- och hålls synkroniserade. Värdena i den här kolumnen får inte användas utanför den här kontexten av användaren. 

Liknande mekanism 1, Kopieringsaktiviteten kommer automatiskt först rensa data för den angivna sektorn från målet SQL-tabellen och kör sedan kopieringsaktiviteten normalt om du vill infoga data från källan till målet för den sektorn. 

