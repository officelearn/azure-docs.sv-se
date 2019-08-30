---
title: Felsöka Azure Data Factory anslutningar | Microsoft Docs
description: Lär dig hur du felsöker anslutnings problem i Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143510"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Felsöka Azure Data Factory anslutningar

Den här artikeln visar vanliga fel söknings metoder för anslutningar i Azure Data Factory.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Felmeddelande: Fjärrservern returnerade ett fel: (403) ej tillåtet

- **Symptom**: Kopierings aktiviteten fungerar inte med följande fel: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Orsak**: En möjlig orsak är att tjänstens huvud namn eller hanterade identitet som du använder inte har behörighet att komma åt en viss mapp/fil.

- **Lösning**: Ge motsvarande behörigheter för alla mappar och undermappar som du behöver kopiera. Referera till [det här dokumentet](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Felmeddelande: Det gick inte att hämta åtkomsttoken genom att använda tjänstens huvud namn. ADAL-fel: service_unavailable

- **Symptom**: kopierings aktiviteten fungerar inte med följande fel:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Orsak**: När den service token Server (STS) som ägs av Azure Active Directory inte är tillgänglig, så returneras ett HTTP-fel 503 för att hantera begär Anden. 

- **Lösning**: Kör kopierings aktiviteten igen om några minuter.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Felmeddelande: Konverteringen misslyckades vid konvertering från en tecken sträng till uniqueidentifier

- **Symptom**: När du kopierar data från tabell data källa (t. ex. SQL Server) till Azure SQL Data Warehouse med hjälp av mellanlagrad kopiering och PolyBase, trycker du på följande fel:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Orsak**: Azure SQL Data Warehouse PolyBase kan inte konvertera en tom sträng till GUID.

- **Lösning**: I Kopiera aktivitets handfat, under PolyBase-inställningar, anger du alternativet**Använd typ standard**till falskt.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Felmeddelande: Förväntad datatyp: DECIMAL (x, x), felvärde

- **Symptom**: När du kopierar data från tabell data källa (t. ex. SQL Server) till SQL DW med mellanlagrad kopia och PolyBase, skulle du trycka på följande fel:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Orsak**: Azure SQL Data Warehouse PolyBase kan inte infoga en tom sträng (null-värde) i decimal kolumn.

- **Lösning**: I Kopiera aktivitets handfat, under PolyBase-inställningar, anger du alternativet**Använd typ standard**till falskt.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Felmeddelande: Java-undantags meddelande: HdfsBridge:: CreateRecordReader

- **Symptom**: Du kopierar data till Azure SQL Data Warehouse med PolyBase och trycker på följande fel:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Orsak**: Den möjliga orsaken är att schemat (total kolumn bredd) är för stort (större än 1 MB). Kontrol lera schemat för mål-SQL DW-tabellen genom att lägga till storleken på alla kolumner:

    - Int-> 4 byte
    - Bigint-> 8 byte
    - Varchar (n), Char (n), binär (n), varbinary (n)-> n byte
    - Nvarchar (n), nchar (n)-> n * 2 byte
    - Datum-> 6 byte
    - Datetime/(2), smalldatetime-> 16 byte
    - DateTimeOffset-> 20 byte
    - Decimal-> 19 byte
    - Float-> 8 byte
    - Money-> 8 byte
    - Smallmoney-> 4 byte
    - Real-> 4 byte
    - Smallint – > 2 byte
    - Time-> 12 byte
    - Tinyint – > 1 byte

- **Lösning**: Minska kolumn bredden till mindre än 1 MB

- Eller Använd Mass infognings metod genom att inaktivera PolyBase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Felmeddelande: Villkoret som anges med HTTP-villkorliga huvud (a) är inte uppfyllt

- **Symptom**: Du använder SQL-fråga för att hämta data från Azure SQL Data Warehouse och träffa följande fel:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Orsak**: Azure SQL Data Warehouse träff-problem fråga den externa tabellen i Azure Storage.

- **Lösning**: Kör samma fråga i SSMS och kontrol lera att du ser samma resultat. Om ja, öppna ett support ärende för att Azure SQL Data Warehouse och ange din SQL DW-Server och databas namn för ytterligare fel sökning.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Felmeddelande: Begär ande storleken är för stor

- **Symptom**: Du kopierar data till Azure Cosmos DB med standard storleken för Skriv journal och felet *"**begär ande storleken är för stor**"* .

- **Orsak**: Cosmos DB begränsar storleken på en enskild begäran till 2 MB. Formeln är en begär ande storlek = enskild dokument storlek * Skriv batchstorleken. Om din dokument storlek är stor resulterar standard beteendet i för stor storlek på begäran. Du kan finjustera Skriv batchstorleken.

- **Lösning**: Minska värdet för "Skriv batchstorlek" i kopierings aktivitets Sink (Standardvärdet är 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Felmeddelande: Fel vid unik index begränsning

- **Symptom**: När du kopierar data till Cosmos DB, trycker du på följande fel:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Orsak**: Det finns två möjliga orsaker:

    - Om du använder **Infoga** som Skriv-beteende innebär det här felet att källdata har rader/objekt med samma ID.

    - Om du använder **upsert** som Skriv beteende och anger en annan unik nyckel till behållaren, innebär det här felet att källdata har rader/objekt med olika ID: n men samma värde för den definierade unika nyckeln.

- **Lösning**: 

    - Ange **upsert** som Skriv-beteende för cause1.
    - För orsak 2, se till att varje dokument har ett annat värde för definierad unik nyckel.

### <a name="error-message-request-rate-is-large"></a>Felmeddelande: Frågehastigheten är för hög

- **Symptom**: När du kopierar data till Cosmos DB, trycker du på följande fel:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Orsak**: De enheter för programbegäran som används är större än den tillgängliga RU som kon figurer ATS i Cosmos DB. Lär dig hur Cosmos DB beräknar RU [](../cosmos-db/request-units.md#request-unit-considerations)härifrån.

- **Lösning**: Här följer två lösningar:

    1. **Öka** värdet för containern ru till större i Cosmos DB, vilket förbättrar prestandan för kopierings aktiviteten, men kostar mer på Cosmos dB. 

    2. Minska **writeBatchSize** till ett mindre värde (till exempel 1000) och ange **parallelCopies** till ett mindre värde, till exempel 1, vilket gör att kopieringen körs sämre än vad som är aktuellt men inte kostar mer kostnad i Cosmos dB.

### <a name="column-missing-in-column-mapping"></a>Kolumn saknas i kolumn mappning

- **Symptom**: När du importerar schema för Cosmos DB för kolumn mappning saknas vissa kolumner. 

- **Orsak**: ADF härleder schemat från de första 10 Cosmos DB dokumenten. Om vissa kolumner/egenskaper inte har något värde i dessa dokument, upptäcks de inte av ADF, vilket innebär att de inte kan visas.

- **Lösning**: Du kan justera frågan enligt nedan om du vill att kolumnen ska visas i resultat uppsättningen med tomt värde: (Antag: kolumnen "omöjlig" saknas i de första 10 dokumenten). Alternativt kan du lägga till kolumnen manuellt för mappning.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Felmeddelande: GuidRepresentation för läsaren är CSharpLegacy

- **Symptom**: När du kopierar data från Cosmos DB MongoAPI/MongoDB med UUID-fältet, så skulle du trycka på följande fel:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Orsak**: Det finns två sätt att representera UUID i BSON-UuidStardard och UuidLegacy. Som standard används UuidLegacy för att läsa data. Du kommer att trycka på fel om dina UUID-data i MongoDB är UuidStandard.

- **Lösning**: I MongoDB anslutnings sträng lägger du till alternativet "**uuidRepresentation = standard**". Mer information finns i [anslutnings strängen för MongoDB](connector-mongodb.md#linked-service-properties).

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Felmeddelande: Ogiltiga SFTP-autentiseringsuppgifter har angetts för autentiseringstypen ' SshPublicKey '

- **Symptom**: Du använder `SshPublicKey` autentisering och trycker på följande fel:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Orsak**: Det finns tre möjliga orsaker:

    1. Om du använder användar gränssnittet för ADF-redigering för att skapa en länkad SFTP-tjänst, innebär det här felet att den privata nyckel som du väljer att använda har fel format. Du kan använda ett PKCS # 8-format för den privata SSH-nyckeln, men Observera att ADF endast stöder formatet traditionellt SSH-nyckel. Mer specifikt är skillnaden mellan PKCS # 8-format och traditionellt nyckel format en PKCS # 8-nyckel innehåll som börjar med " *-----BEGIN ENcrypted private key-----* " medan traditionellt nyckel format börjar med " *-----påbörja RSA Private Key-----* ".
    2. Om du använder Azure Key Vault för att lagra innehållet i den privata nyckeln eller använda programmatiskt sätt för att redigera den länkade SFTP-tjänsten, innebär det här felet att det privata nyckel innehållet är felaktigt, antagligen att det inte är Base64-kodat.
    3. Ogiltig autentiseringsuppgift eller privat nyckel innehåll.

- **Lösning**: 

    - Om du vill ha #1 kör du följande kommandon för att konvertera nyckeln till traditionellt nyckel format och använder den sedan i användar gränssnittet för ADF-redigering.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - För att orsaka att #2 genererar en sådan sträng kan kunden använda följande två sätt:
    - Använda base64-konverterings verktyg från tredje part: klistra in hela privat nyckel innehåll i verktyg som [base64-koda och avkoda](https://www.base64encode.org/), koda dem till en Base64-format sträng och klistra in den här strängen i Key Vault eller Använd det här värdet för redigering av SFTP-länkad tjänst program mässigt.
    - Använda C# kod:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - För orsak #3, dubbelklicka på om nyckel filen eller lösen ordet är korrekt med andra verktyg för att kontrol lera om du kan använda den för att komma åt SFTP-servern korrekt.

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)



