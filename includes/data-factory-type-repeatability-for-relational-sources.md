## <a name="repeatability-during-copy"></a>Repeterbarhet vid kopiering
När du kopierar data från och till relationella butiker, som du behöver repeterbarhet Tänk på att undvika oväntade resultat. 

En sektor kan köras automatiskt i Azure Data Factory enligt återförsökspolicyn som anges. Vi rekommenderar att du ställer in en återförsöksprincip som skyddar mot tillfälligt fel. Därför är repeterbarhet det viktigt att ta hand om under dataflyttning. 

**Som en källa:**

> [!NOTE]
> Följande exempel gäller för alla datalager som har stöd för rektangulär datauppsättningar är för SQL Azure. Du kan behöva justera den **typen** för källa och **frågan** egenskapen (till exempel: frågan i stället sqlReaderQuery) data lagras.   
> 
> 

Vanligtvis vid läsning från relationella lagrar kan du läsa den data som motsvarar den sektorn. Ett sätt att göra detta är med hjälp av variablerna WindowStart och WindowEnd tillgängliga i Azure Data Factory. Läs mer om variabler och funktioner i Azure Data Factory här i den [schemaläggning och körning](../articles/data-factory/v1/data-factory-scheduling-and-execution.md) artikel. Exempel: 

```json
"source": {
"type": "SqlSource",
"sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Den här frågan läser data från ”mytable prefix, som ligger inom intervallet sektorn varaktighet. Kör i det här segmentet skulle alltid kontrollera detta beteende. 

I annat fall kan du läsa hela tabellen (anta för en gång flytta bara) och du kan definiera sqlReaderQuery enligt följande:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```
