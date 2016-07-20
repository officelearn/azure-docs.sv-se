## Vad är Tabelltjänsten

Azure Table Storage-tjänsten lagrar stora mängder strukturerade data. Tjänsten är ett NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data. Vanliga användningsområden för Tabelltjänsten är:

-   Lagring av flera TB med strukturerade data som kan serva webbaserade skalningsbara program
-   Lagring av datauppsättningar som inte kräver komplexa kopplingar, sekundärnycklar eller lagrade procedurer som kan avnormaliseras för snabb åtkomst
-   Ställa snabba datafrågor med hjälp av ett klustrat index
-   Komma åt data med OData-protokollet och LINQ-frågor med WCF Data Service .NET-bibliotek

Du kan använda Tabelltjänsten för att lagra och fråga stora mängder strukturerad, icke-relationell data och dina tabeller skalar upp efter behov.

## Tabelltjänst-koncept

Tabelltjänsten består av följande komponenter:

![Tabell1][Table1]

-   **URL-format:** kod adresserar tabeller i ett konto med det här adressformatet:   
    http://`<storage account>`.table.core.windows.net/`<table>`  
      
    Du kan adressera Azure-tabeller direkt med den här adressen med OData-protokollet. Mer information finns på [OData.org][]

-   **Lagringskonto:** All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål för Azure Storage](storage-scalability-targets.md) för information om kapacitet för lagringskonton.

-   **Tabell**: en tabell är en samling entiteter. Tabeller framtvingar inte något schema på entiteter, vilket innebär att en enda tabell kan innehålla entiteter med olika egenskapsuppsättningar. Antalet tabeller som ett lagringskonto kan innehålla begränsas bara av lagringskontots kapacitet.

-   **Entitet**: en entitet är en uppsättning egenskaper, liknande en databasrad. En entitet kan vara upp till 1MB.

-   **Egenskaper**: en egenskap är ett namn-värde-par. Varje entitet kan innehålla upp till 252 egenskaper för att lagra data. Varje entitet har också 3 systemegenskaper som anger en partitionsnyckel, en radnyckel och en tidsstämpel. Entiteter med samma partitionsnyckel kan frågas snabbare och kan infogas/uppdateras i atomiska åtgärder. En entitets radnyckel är dess unika identifierare inom en partition.

Mer information om namngivning av tabeller och egenskaper finns i [Förstå Tabelltjänst-datamodellen](https://msdn.microsoft.com/library/azure/dd179338.aspx).
  
  [Tabell1]: ./media/storage-table-concepts-include/table1.png
  [OData.org]: http://www.odata.org/



<!--HONumber=Jun16_HO2-->


