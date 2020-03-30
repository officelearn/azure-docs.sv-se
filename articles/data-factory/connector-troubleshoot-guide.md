---
title: Felsöka Azure Data Factory-anslutningsprogram
description: Lär dig hur du felsöker anslutningsproblem i Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778234"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Felsöka Azure Data Factory-anslutningsprogram

I den här artikeln beskrivs vanliga felsökningsmetoder för kopplingar i Azure Data Factory.
  

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Felkod: AzureBlobOperationFailed

- **Meddelande:**`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Orsak**: Blob lagringsåtgärd träff problem.

- **Rekommendation**: Kontrollera felet i detaljer. Se blob-hjälpdokument: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Kontakta lagringsteamet om det behövs hjälp.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Felkod: AzureBlobServiceNotReturnExpectedDataLength

- **Meddelande:**`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Felkod: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Meddelande:**`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Felkod: AzureStorageOperationFailedConcurrentWrite

- **Meddelande:**`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Felmeddelande: Begärandens storlek är för stor

- **Symptom:** Du kopierar data till Azure Cosmos DB med standardstorlek för skrivbatchen och slår fel *" Begäran storlek är för**stor**"*.

- **Orsak**: Cosmos DB begränsar en enda begäran storlek till 2 MB. Formeln är, Begär storlek = Enstaka dokumentstorlek * Skriv batchstorlek. Om dokumentstorleken är stor resulterar standardbeteendet i för stor begäranstorlek. Du kan ställa in skrivbatchens storlek.

- **Upplösning:** I kopiera aktivitetsmottagare, minska värdet "Skriv batchstorlek" (standardvärdet är 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Felmeddelande: Ett unikt fel på indexbegränsningen

- **Symptom:** När du kopierar data till Cosmos DB, du träffar följande fel:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Orsak**: Det finns två möjliga orsaker:

    - Om du använder **Infoga** som skrivbeteende innebär det här felet att du källdata har rader/objekt med samma ID.

    - Om du använder **Upsert** som skrivbeteende och ställer in en annan unik nyckel till behållaren betyder det här felet att du källdata har rader/objekt med olika ID:n men samma värde för den definierade unika nyckeln.

- **Upplösning**: 

    - För orsak1 ställer du in **Upsert** som skrivbeteende.
    - För orsak 2 kontrollerar du att varje dokument har olika värde för definierad unik nyckel.

### <a name="error-message-request-rate-is-large"></a>Felmeddelande: Begäranden är stor

- **Symptom:** När du kopierar data till Cosmos DB, du träffar följande fel:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Orsak**: De begärandeenheter som används är större än den tillgängliga RU som konfigurerats i Cosmos DB. Läs om hur Cosmos DB beräknar RU [härifrån](../cosmos-db/request-units.md#request-unit-considerations).

- **Upplösning**: Här är två lösningar:

    1. **Öka behållaren RU** till större värde i Cosmos DB, vilket kommer att förbättra kopieringsaktivitetens prestanda, men medför mer kostnad i Cosmos DB. 

    2. Minska **writeBatchSize** till mindre värde (till exempel 1000) och ange **parallelCopies** till mindre värde som 1, vilket gör kopieringskörningsprestanda sämre än aktuell men inte medför mer kostnad i Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Kolumn saknas i kolumnmappning

- **Symptom**: När du importerar schema för Cosmos DB för kolumnmappning saknas vissa kolumner. 

- **Orsak**: ADF härleder schemat från de första 10 Cosmos DB-dokumenten. Om vissa kolumner/egenskaper inte har något värde i dessa dokument identifieras de inte av ADF, vilket innebär att de inte visas.

- **Upplösning**: Du kan ställa in frågan enligt nedan för att framtvinga kolumnen för att visa resultatuppsättningen med tomt värde: (anta: "omöjligt" kolumn saknas i de första 10 dokumenten). Du kan också lägga till kolumnen manuellt för mappning.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Felmeddelande: GuidRepresentation för läsaren är CSharpLegacy

- **Symptom:** När du kopierar data från Cosmos DB MongoAPI/MongoDB med UUID-fält, slår du följande fel:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Orsak**: Det finns två sätt att representera UUID i BSON - UuidStardard och UuidLegacy. Som standard används UuidLegacy för att läsa data. Du kommer att slå fel om dina UUID-data i MongoDB är UuidStandard.

- **Upplösning**: I MongoDB-anslutningssträng lägger du till alternativ "**uuidRepresentation=standard**". Mer information finns i [MongoDB-anslutningssträngen](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Felkod: AdlsGen2OperationFailed

- **Meddelande:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Orsak**: ADLS Gen2 genererar felet som anger åtgärden misslyckades.

- **Rekommendation**: Kontrollera det detaljerade felmeddelandet som genereras av ADLS Gen2. Om det orsakas av tillfälliga fel, försök igen. Om du behöver ytterligare hjälp kontaktar du Azure Storage-supporten och anger begärande-ID i felmeddelandet.

- **Orsak**: När felmeddelandet innehåller "Förbjudet" kanske tjänstens huvudnamn eller hanterade identitet som du använder inte har tillräcklig behörighet för att komma åt ADLS Gen2.

- **Rekommendation**: Se hjälpdokumentet: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Orsak**: När felmeddelandet innehåller "InternalServerError" returneras felet av ADLS Gen2.

- **Rekommendation**: Det kan orsakas av tillfälliga fel, försök igen. Om problemet kvarstår kontaktar du Azure Storage-supporten och anger begärande-ID i felmeddelandet.


### <a name="error-code--adlsgen2invalidurl"></a>Felkod: AdlsGen2InvalidUrl

- **Meddelande:**`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Felkod: AdlsGen2InvalidFolderPath

- **Meddelande:**`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Felkod: AdlsGen2OperationFailedConcurrentWrite

- **Meddelande:**`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Felkod: AdlsGen2TimeoutError

- **Meddelande:**`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Felmeddelande: Fjärrservern returnerade ett fel: (403) Förbjudet

- **Symptom**: Kopieringsaktiviteten misslyckas med följande fel: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Orsak**: En möjlig orsak är att tjänstens huvudnamn eller hanterade identitet som du använder inte har behörighet att komma åt en viss mapp/fil.

- **Lösning**: Bevilja motsvarande behörigheter för alla mappar och undermappar som du behöver kopiera. Se [det här dokumentet](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Felmeddelande: Det gick inte att hämta åtkomsttoken med hjälp av tjänstens huvudnamn. ADAL-fel: service_unavailable

- **Symptom**: Kopieringsaktiviteten misslyckas med följande fel:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Orsak**: När servicetokenservern (STS) som ägs av Azure Active Directory inte är tillgänglig, dvs. 

- **Lösning**: Kör kopieringsaktiviteten igen efter flera minuter.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Felkod: SqlFailedToConnect

- **Meddelande:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Orsak**: Om felmeddelandet innehåller "SqlException" genererar SQL Database felet som anger att någon specifik åtgärd misslyckades.

- **Rekommendation**: Sök efter SQL-felkod i det https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorshär referensdokumentet för mer information: . Om du behöver ytterligare hjälp kontaktar du Azure SQL-supporten.

- **Orsak**: Om felmeddelandet innehåller "Klient med IP-adress'...' är inte tillåtet att komma åt servern", och du försöker ansluta till Azure SQL Database, vanligtvis orsakas den av Problemet med Azure SQL Database-brandväggen.

- **Rekommendation:** Aktivera alternativet Tillåt Azure-tjänster och resurser för att komma åt den här servern i Azure SQL Server-brandväggen. Referensdokument: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Felkod: SqlOperationFailed

- **Meddelande:**`A database operation failed. Please search error to get more details.`

- **Orsak**: Om felmeddelandet innehåller "SqlException" genererar SQL Database felet som anger att någon specifik åtgärd misslyckades.

- **Rekommendation**: Om SQL-fel inte är klart, försök att ändra databasen till den senaste kompatibilitetsnivån "150". Det kan kasta senaste versionen SQL-fel. Se detaljdokumentet: https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat.
        Om du vill felsöka SQL-problem söker du efter SQL-felkod i det här referensdokumentet för mer information: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Om du behöver ytterligare hjälp kontaktar du Azure SQL-supporten.

- **Orsak**: Om felmeddelandet innehåller "PdwManagedToNativeInteropException", vanligtvis det orsakas av obalans mellan källa och diskbänk kolumnstorlekar.

- **Rekommendation**: Kontrollera storleken på både käll- och diskkolumner. Om du behöver ytterligare hjälp kontaktar du Azure SQL-supporten.

- **Orsak**: Om felmeddelandet innehåller "InvalidOperationException", orsakas det vanligtvis av ogiltiga indata.

- **Rekommendation:** För att identifiera vilken rad som stöter på problemet, aktivera feltolerans funktion på kopieringsaktivitet, som kan omdirigera problematiska rader till lagring för vidare undersökning. Referensdokument: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Felkod: SqlUnauthorizedAccess

- **Meddelande:**`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Orsak**: Autentiseringsuppgifter är felaktiga eller inloggningskontot kan inte komma åt SQL Database.

- **Rekommendation**: Kontrollera inloggningskontot har tillräckligt med behörighet för att komma åt SQL Database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Felkod: SqlOpenConnectionTimeout

- **Meddelande:**`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Orsak**: Kan vara sql database-transientfel.

- **Rekommendation:** Försök att uppdatera den länkade tjänstanslutningssträngen igen med större tidsgränsen för anslutning.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Felkod: SqlAutoCreateTableTypeFailed

- **Meddelande:**`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Orsak**: Tabellen För automatisk skapande kan inte uppfylla källkravet.

- **Rekommendation**: Uppdatera kolumntypen i "mappningar" eller skapa sink-tabellen manuellt i målservern.


### <a name="error-code--sqldatatypenotsupported"></a>Felkod: SqlDataTypeInte Stöds

- **Meddelande:**`A database operation failed. Check the SQL errors.`

- **Orsak**: Om problemet uppstår på SQL-källa och felet är relaterat till SqlDateTime-spill, är datavärdet över logiktypområdet (1/1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Rekommendation:** Casta typen till sträng i käll-SQL-fråga, eller i kopiera aktivitetskolumnmappning ändra kolumntypen till "String".

- **Orsak**: Om problemet uppstår på SQL-diskbänken och felet är relaterat till SqlDateTime-spill, är datavärdet över det tillåtna intervallet i sink-tabellen.

- **Rekommendation**: Uppdatera motsvarande kolumntyp till typen "datetime2" i sink-tabellen.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Felkod: SqlInvalidDbStoredProcedure

- **Meddelande:**`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Orsak**: Den angivna lagrade proceduren är ogiltig. Det kan bero på att den lagrade proceduren inte returnerar några data.

- **Rekommendation**: Validera den lagrade proceduren med SQL Tools. Kontrollera att den lagrade proceduren kan returnera data.


### <a name="error-code--sqlinvaliddbquerystring"></a>Felkod: SqlInvalidDbQueryString

- **Meddelande:**`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Orsak**: Den angivna SQL-frågan är ogiltig. Det kan bero på att frågan inte returnerar några data

- **Rekommendation**: Validera SQL Query av SQL Tools. Kontrollera att frågan kan returnera data.


### <a name="error-code--sqlinvalidcolumnname"></a>Felkod: SqlInvalidColumnName

- **Meddelande:**`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Orsak**: Det går inte att hitta kolumnen. Möjlig konfiguration fel.

- **Rekommendation**: Verifiera kolumnen i frågan, "struktur" i datauppsättningen och "mappningar" i aktivitet.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Felkod: SqlColumnNameMismatchAvCaseSensitive

- **Meddelande:**`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Felkod: SqlBatchWriteTimeout

- **Meddelande:**`Timeouts in SQL write operation.`

- **Orsak**: Kan vara sql database-transientfel.

- **Rekommendation**: Försök igen. Om problemet förebrår kontaktar du Azure SQL-supporten.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Felkod: SqlBatchWriteTransactionFailerad

- **Meddelande:**`SQL transaction commits failed`

- **Orsak**: Om undantagsinformation ständigt anger tidsgräns för transaktioner är nätverksfördröjningen mellan integrationskörning och databas högre än standardtröskeln som 30 sekunder.

- **Rekommendation:** Uppdatera sql-länkad tjänstanslutningssträng med värdet för anslutningstidsutskrift som är lika med 120 eller högre och kör aktiviteten igen.

- **Orsak**: Om undantagsinformation periodvis berättar sqlconnection bruten, kan det bara vara tillfälliga nätverksfel eller SQL Database-sidoproblem

- **Rekommendation**: Försök aktiviteten igen och granska mätvärden för SQL Database-sidan.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Felkod: SqlBulkCopyInvalidColumnLength

- **Meddelande:**`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Orsak**: SQL Bulk Copy misslyckades på grund av att ta emot en ogiltig kolumnlängd från bcp-klienten.

- **Rekommendation:** För att identifiera vilken rad som stöter på problemet, aktivera feltolerans funktion på kopieringsaktivitet, som kan omdirigera problematiska rader till lagring för vidare undersökning. Referensdokument: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Felkod: SqlConnectionIsClosed

- **Meddelande:**`The connection is closed by SQL Database.`

- **Orsak:** SQL-anslutningen stängs av SQL Database när hög samtidig körning och server avsluta anslutningen.

- **Rekommendation**: Fjärrservern stängde SQL-anslutningen. Försök igen. Om problemet förebrår kontaktar du Azure SQL-supporten.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Felkod: SqlCreateTableFailedUnsupportedType

- **Meddelande:**`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Felmeddelande: Konverteringen misslyckades när den konverterade från en teckensträng till unikidentifierare

- **Symptom:** När du kopierar data från tabelldatakälla (till exempel SQL Server) till Azure SQL Data Warehouse med hjälp av stegvis kopia och PolyBase, slår du följande fel:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Orsak**: Azure SQL Data Warehouse PolyBase kan inte konvertera tom sträng till GUID.

- **Upplösning**: I Kopiera aktivitetsmottagare, under Polybase-inställningar, anger du alternativet "**använd typtyp**" till false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Felmeddelande: Förväntad datatyp: DECIMAL(x,x), Felande värde

- **Symptom:** När du kopierar data från tabelldatakälla (till exempel SQL Server) till SQL DW med hjälp av stegvis kopia och PolyBase, slår du följande fel:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Orsak**: Azure SQL Data Warehouse Polybase kan inte infoga tom sträng (null-värde) i decimalkolumnen.

- **Upplösning**: I Kopiera aktivitetsmottagare, under Polybase-inställningar, anger du alternativet "**använd typtyp**" till false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Felmeddelande: Java-undantagsmeddelande:HdfsBridge::CreateRecordReader

- **Symptom:** Du kopierar data till Azure SQL Data Warehouse med PolyBase och slår på följande fel:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Orsak**: Den möjliga orsaken är att schemat (total kolumnbredd) är för stort (större än 1 MB). Kontrollera schemat för tabellen mål-SQL DW genom att lägga till storleken på alla kolumner:

    - Int -> 4 byte
    - Bigint -> 8 byte
    - Varchar(n),char(n),binary(n), varbinary(n) -> n bytes
    - Nvarchar(n), nchar(n) -> n*2 byte
    - Datum -> 6 byte
    - Datumtid/(2), smalldatetime -> 16 byte
    - Datetimeoffset -> 20 byte
    - Decimal -> 19 byte
    - Float -> 8 byte
    - Pengar -> 8 byte
    - Småpengar -> 4 byte
    - Real -> 4 byte
    - Smallint -> 2 byte
    - Tid -> 12 byte
    - Tinyint -> 1 byte

- **Upplösning**: Minska kolumnbredden till mindre än 1 MB

- Eller använd massinfogningsmetod genom att inaktivera Polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Felmeddelande: Villkoret som anges med HTTP-villkorliga huvuden uppfylls inte

- **Symptom:** Du använder SQL-frågan för att hämta data från Azure SQL Data Warehouse och slå följande fel:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Orsak**: Azure SQL Data Warehouse träff problem frågar den externa tabellen i Azure Storage.

- **Lösning**: Kör samma fråga i SSMS och kontrollera om du ser samma resultat. Om du gör det öppnar du ett supportärende för Azure SQL Data Warehouse och anger din SQL DW-Server och ditt databasnamn för ytterligare felsökning.
            

## <a name="delimited-text-format"></a>Avgränsat textformat

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Felkod: AvgränsadTextColumnNameNotAllowNull

- **Meddelande:**`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Orsak**: När du ställer in "firstRowAsHeader" i aktivitet används den första raden som kolumnnamn. Det här felet betyder att den första raden innehåller tomt värde. Till exempel: "ColumnA,,ColumnB".

- **Rekommendation**: Kontrollera den första raden och åtgärda värdet om det finns tomt värde.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Felkod: AvgränsadTextMoreColumnsThanDedefinierad

- **Meddelande:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Orsak**: Antalet problematiska rader är stort än antalet kolumner på den första raden. Det kan bero på dataproblem eller felaktiga inställningar för kolumnavgränsare/citattecken.

- **Rekommendation**: Vänligen hämta radantalet i felmeddelandet, kontrollera radens kolumn och åtgärda data.

- **Orsak**: Om det förväntade kolumnantalet är "1" i felmeddelandet är det möjligt att du angav fel komprimerings- eller formatinställningar, vilket gjorde att ADF felaktigt tolkar filen/filerna.

- **Rekommendation**: Kontrollera formatinställningarna för att se till att den matchar källfilerna.

- **Orsak**: Om källan är en mapp är det möjligt att filerna under den angivna mappen har ett annat schema.

- **Rekommendation**: Kontrollera att filerna under den angivna mappen har samma schema.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Felkod: AvgränsadTextIncorrectRowDelimiter

- **Meddelande:**`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Felkod: AvgränsadTextTooLargeColumnCount

- **Meddelande:**`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Felkod: AvgränsadeTextInvalidSettings

- **Meddelande:**`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Felkod: DynamicsCreateServiceClientError

- **Meddelande:**`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Orsak**: Detta är ett tillfälligt problem på dynamikserversidan.

- **Rekommendation**: Kör om rörledningen. Om fortsätt att misslyckas, försök att minska parallellismen. Om det fortfarande misslyckas, kontakta dynamics support.



## <a name="json-format"></a>JSON-Format

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Felkod: JsonInvalidArrayPathDefinition

- **Meddelande:**`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Felkod: JsonEmptyJObjectData

- **Meddelande:**`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Felkod: JsonNullValueInPathDefinition

- **Meddelande:**`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Felkod: JsonUnsupportedHierarchicalComplexValue

- **Meddelande:**`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Felkod: JsonConflictPartitionDiscoverySchema

- **Meddelande:**`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Felkod: JsonInvalidDataFormat

- **Meddelande:**`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Felkod: JsonInvalidDataMixedArrayAndObject

- **Meddelande:**`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Parquet-format

### <a name="error-code--parquetjavainvocationexception"></a>Felkod: ParquetJavaInvocationException

- **Meddelande:**`An error occurred when invoking java, message: %javaException;.`

- **Orsak:** När felmeddelandet innehåller "java.lang.OutOfMemory", "Java heap space" och "doubleCapacity", är det vanligtvis ett problem med minneshantering i gamla versionen av integrationskörningen.

- **Rekommendation**: Om du använder Självvärdförd Integration Runtime och versionen är tidigare än 3.20.7159.1, föreslå att uppgradera till den senaste versionen.

- **Orsak**: När felmeddelandet innehåller "java.lang.OutOfMemory" har integrationskörningen inte tillräckligt med resurser för att bearbeta filen/filerna.

- **Rekommendation**: Begränsa samtidiga körningar på integrationskörningen. För självvärdförd integrationskörning skalar du upp till en kraftfull dator med minne som är lika med eller större än 8 GB.

- **Orsak**: När felmeddelandet innehåller "NullPointerReference" är det möjligt ett tillfälligt fel.

- **Rekommendation**: Försök igen. Om problemet kvarstår kontaktar du supporten.


### <a name="error-code--parquetinvalidfile"></a>Felkod: ParquetInvalidFile

- **Meddelande:**`File is not a valid parquet file.`

- **Orsak:** Parkett fil fråga.

- **Rekommendation**: Kontrollera ingången är en giltig parkett fil.


### <a name="error-code--parquetnotsupportedtype"></a>Felkod: ParquetNotSupportedType

- **Meddelande:**`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Orsak**: Parkettformatet stöds inte i Azure Data Factory.

- **Rekommendation**: Dubbelkolla källdata. Se dokumentet: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Felkod: ParquetMissedDecimalPrecisionScale

- **Meddelande:**`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Orsak**: Försök att tolka antalet precision och skala, men ingen sådan information tillhandahålls.

- **Rekommendation:**"Källa" returnerar inte korrekt precision och skala. Kontrollera problemet kolumn precision och skala.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Felkod: ParquetInvalidDecimalPrecisionScale

- **Meddelande:**`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Orsak**: Schemat är ogiltigt.

- **Rekommendation**: Kontrollera problemet kolumn precision och skala.


### <a name="error-code--parquetcolumnnotfound"></a>Felkod: ParquetColumnNotFound

- **Meddelande:**`Column %column; does not exist in Parquet file.`

- **Orsak**: Källschemat matchar inte med sink-schema.

- **Rekommendation**: Kontrollera "mappningarna" i "aktivitet". Kontrollera att källkolumnen kan mappas till kolumnen höger diskho.


### <a name="error-code--parquetinvaliddataformat"></a>Felkod: ParquetInvalidDataFormat

- **Meddelande:**`Incorrect format of %srcValue; for converting to %dstType;.`

- **Orsak**: Data kan inte konverteras till den typ som anges i mappings.source

- **Rekommendation**: Dubbelkolla källdata eller ange rätt datatyp för den här kolumnen i mappning av kolumnmappning för kopieringsaktivitet. Se dokumentet: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Felkod: ParquetDataCountNotMatchColumnCount

- **Meddelande:**`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Orsak**: Antal källkolumn och antal diskhoskolumner stämmer inte överens

- **Rekommendation:** Antalet kolumner för dubbelkollade källkolumn är samma som antalet sinkkolumner i "mappning".


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Felkod: ParquetDataTypeNotMatchColumnType

- **Meddelande**: Datatypen %srcType; matchar inte den angivna kolumntypen %dstType. kolumnen %columnIndex;.

- **Orsak**: Data från källa kan inte konverteras till maskinskriven definierad i diskhon

- **Rekommendation**: Ange en korrekt typ i mapping.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Felkod: ParquetBridgeInvalidData

- **Meddelande:**`%message;`

- **Orsak**: Datavärde över begränsning

- **Rekommendation**: Försök igen. Om problemet kvarstår, vänligen kontakta oss.


### <a name="error-code--parquetunsupportedinterpretation"></a>Felkod: ParquetUnsupportedInterpretation

- **Meddelande:**`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Orsak**: Scenariot stöds inte

- **Rekommendation**: "ParquetInterpretFor" bör inte vara "sparkSql".


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Felkod: ParquetUnsupportFileLevelCompressionOption

- **Meddelande:**`File level compression is not supported for Parquet.`

- **Orsak**: Scenariot stöds inte

- **Rekommendation**: Ta bort "CompressionType" i nyttolast.



## <a name="general-copy-activity-error"></a>Fel vid allmän kopieringsaktivitet

### <a name="error-code--jrenotfound"></a>Felkod: JreNotFound

- **Meddelande:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Orsak**: Den självvärderade integrationskörningen kan inte hitta Java Runtime. Java Runtime krävs för att läsa viss källa.

- **Rekommendation**: Kontrollera din integration runtime miljö, referens doc:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Felkod: JokerteckenPathSinkNotSupported

- **Meddelande:**`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Orsak**: Sink-datauppsättning stöder inte jokertecken.

- **Rekommendation**: Kontrollera sink-datauppsättningen och åtgärda sökvägen utan jokerteckenvärde.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Felkod: MappingInvalidPropertyWithEmptyValue

- **Meddelande:**`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Felkod: MappingInvalidPropertyWithNamePathAndOrdinal

- **Meddelande:**`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Felkod: MappingDuplicatedOrdinal

- **Meddelande:**`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Felkod: MappingInvalidOrdinalForSinkColumn

- **Meddelande:**`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Nästa steg

Mer felsökningshjälp finns i följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Begäran om datafabriksfunktion](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Spill forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information om Data Factory](https://twitter.com/hashtag/DataFactory)
            
