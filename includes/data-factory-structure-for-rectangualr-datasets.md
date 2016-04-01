## Angeben der Strukturdefinition für rechteckige Datasets
Der Abschnitt "Structure" in der JSON von Datasets ist ein **optional** Abschnitt für rechteckige Tabellen (mit Zeilen und Spalten) und enthält eine Auflistung von Spalten für die Tabelle. Sie verwenden den Abschnitt "structure" entweder zum Angeben von Typinformationen für Typumwandlungen oder für Spaltenzuordnungen. In den folgenden Abschnitten werden diese Features ausführlich beschrieben. 

Jede Spalte enthält die folgenden Eigenschaften:

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| name | Name der Spalte. | Ja |
| type | Datentyp der Spalte. Im nachstehenden Abschnitt "Typkonvertierungen" finden Sie Details zur Angabe von Typinformationen. | Nein |
| culture | Zu verwendendes .NET-basiertes Gebietsschema, wenn "type" angegeben ist und den .NET-Typ "Datetime" oder "Datetimeoffset" hat. Die Standardeinstellung ist "en-us".  | Nein |
| format | Zu verwendende Formatzeichenfolge, wenn "type" angegeben ist und den .NET-Typ "Datetime" oder "Datetimeoffset" hat. | Nein |

Das folgende Beispiel zeigt den Abschnitt "structure" der JSON für eine Tabelle mit den drei Spalten "userid", "name" und "lastlogindate".

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Verwenden Sie die folgenden Richtlinien für die "Struktur" Informationen hinzufügen und welchen der **Struktur** Abschnitt.

1.  **Für strukturierte Datenquellen** speichern und geben Informationen zusammen mit den Daten selbst (Datenquellen wie SQL Server, Oracle, Azure-Tabelle usw.), sollten Sie den Abschnitt "Structure" nur, wenn Sie möchten bestimmte Quellspalten bestimmten Spalten der Senke Datenschema- und deren Namen nicht identisch sind (Siehe Details im nachfolgenden Abschnitt zur spaltenzuordnung). 

    Wie bereits erwähnt, ist die Information "type" im Abschnitt "structure" optional. Für strukturierte Datenquellen steht die Information "type" bereits als Teil der Datasetdefinition im Datenspeicher zur Verfügung, weshalb Sie Typinformationen nicht dem Abschnitt "structure" hinzufügen müssen.
2. **Für das Schema von lesedatenquellen (insbesondere Azure-Blob)**  Sie können auswählen, um Daten zu speichern, ohne alle Schema- oder Typinformationen mit den Daten speichern. Bei diesen Typen von Datenquellen sollten Sie den Abschnitt "structure" in den beiden folgenden Fällen hinzufügen:
    1. Sie wünschen eine Spaltenzuordnung.
    2. Wenn das Dataset eine Quelle in einer Kopieraktivität ist, können Sie Typinformationen in "structure" bereitstellen. Data Factory verwendet diese Typinformationen für die Konvertierung in systemeigene Typen für die Senke. Finden Sie unter [Verschieben von Daten zu und von Azure-Blob](../articles/data-factory/data-factory-azure-blob-connector.md) Artikel Weitere Informationen.

### Unterstützte .NET-basierte Typen 
Data Factory unterstützt die folgenden CLS-konformen auf .NET basierenden Typwerte für die Bereitstellung von Typinformationen in "structure" für das Schema von Lesedatenquellen wie Azure-Blob.

- Int16
- Int32 
- Int64
- Single
- Double
- Decimal
- Byte]
- Bool
- String 
- Guid
- Datetime
- Datetimeoffset
- Timespan 

Für "Datetime" und "Datetimeoffset" können Sie optional auch die Zeichenfolgen "culture" und "format" angeben, um das Analysieren Ihrer benutzerdefinierten "Datetime"-Zeichenfolge zu erleichtern. Nachstehend sehen Sie ein Beispiel einer Typumwandlung.



