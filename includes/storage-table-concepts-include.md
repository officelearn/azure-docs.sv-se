## <a name="what-is-table-storage"></a>Vad är Table Storage
Azure Table Storage lagrar stora mängder strukturerade data. Tjänsten är ett NoSQL-datalager som tar emot autentiserade anrop inuti och utanför Azure-molnet. Azure-tabeller passar utmärkt för att lagra strukturerade, icke-relationella data. Vanliga användningsområden för Table Storage är:

* Lagring av flera TB med strukturerade data som kan serva webbaserade skalningsbara program
* Lagring av datauppsättningar som inte kräver komplexa kopplingar, sekundärnycklar eller lagrade procedurer som kan avnormaliseras för snabb åtkomst
* Ställa snabba datafrågor med hjälp av ett klustrat index
* Komma åt data med OData-protokollet och LINQ-frågor med WCF Data Service .NET-bibliotek

Du kan använda Table Storage för att lagra och fråga stora mängder strukturerad, icke-relationell data och dina tabeller skalar upp efter behov.

## <a name="table-storage-concepts"></a>Koncept för Table Storage
Table Storage består av följande komponenter:

![Komponentdiagram för Table Storage][Table1]

* **URL-format:** Azure Table Storage-konton använder det här formatet:`http://<storage account>.table.core.windows.net/<table>`

  Azure Cosmos DB tabell API konton använder du formatet:`http://<storage account>.table.cosmosdb.azure.com/<table>`  

  Du kan adressera Azure-tabeller direkt med den här adressen med OData-protokollet. Mer information finns på [OData.org][OData.org].
* **Konton:** All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål för Azure Storage](../articles/storage/common/storage-scalability-targets.md) för information om kapacitet för lagringskonton. 

    All åtkomst till Azure Cosmos DB görs via ett tabell-API-konto. Se [skapa ett tabell-API-konto](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account) information som skapar en tabell API-konto.
* **Tabell**: en tabell är en samling entiteter. Tabeller framtvingar inte något schema på entiteter, vilket innebär att en enda tabell kan innehålla entiteter med olika egenskapsuppsättningar.  
* **Entitet**: en entitet är en uppsättning egenskaper, liknande en databasrad. En entitet i Azure Storage kan vara upp till 1MB i storlek. En entitet i Azure Cosmos-databasen kan vara upp till 2MB i storlek.
* **Egenskaper**: en egenskap är ett namn-värde-par. Varje entitet kan innehålla upp till 252 egenskaper för att lagra data. Varje entitet har också tre systemegenskaper som anger en partitionsnyckel, en radnyckel och en tidsstämpel. Entiteter med samma partitionsnyckel kan frågas snabbare och kan infogas/uppdateras i atomiska åtgärder. En entitets radnyckel är dess unika identifierare inom en partition.

Mer information om namngivning av tabeller och egenskaper finns i [Förstå Tabelltjänst-datamodellen](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
