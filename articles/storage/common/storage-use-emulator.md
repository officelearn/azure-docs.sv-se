---
title: Använd Azure-lagringsemulatorn för utveckling och testning | Microsoft-dokument
description: Azure-lagringsemulatorn tillhandahåller en kostnadsfri lokal utvecklingsmiljö för att utveckla och testa dina Azure Storage-program.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1f13efeb5c2ebcb8b23dea6f9ae997039972f089
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72428309"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Använd Azure-lagringsemulatorn för utveckling och testning

Microsoft Azure-lagringsemulatorn är ett verktyg som emulerar Azure Blob-, Kö- och Table-tjänsterna för lokala utvecklingsändamål. Du kan testa ditt program mot lagringstjänsterna lokalt utan att skapa en Azure-prenumeration eller ådra dig några kostnader. När du är nöjd med hur ditt program fungerar i emulatorn växlar du till att använda ett Azure-lagringskonto i molnet.

## <a name="get-the-storage-emulator"></a>Hämta lagringsemulatorn

Lagringsemulatorn är tillgänglig som en del av [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Du kan också installera lagringsemulatorn med hjälp av det [fristående installationsprogrammet](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (direkthämtning). Om du vill installera lagringsemulatorn måste du ha administratörsbehörighet på datorn.

Lagringsemulatorn körs för närvarande endast i Windows. Om du behöver en lagring emulator för Linux, är ett alternativ den gemenskap underhålls, öppen källkod lagring emulator [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Data som skapas i en version av lagringsemulatorn är inte garanterat tillgängliga när du använder en annan version. Om du behöver spara dina data på lång sikt rekommenderar vi att du lagrar dessa data i ett Azure-lagringskonto i stället för i lagringsemulatorn.
> 
> Lagringsemulatorn beror på specifika versioner av OData-biblioteken. Att ersätta OData DLL-filer som används av lagringsemulatorn med andra versioner stöds inte och kan orsaka oväntat beteende. Alla versioner av OData som stöds av lagringstjänsten kan dock användas för att skicka förfrågningar till emulatorn.

## <a name="how-the-storage-emulator-works"></a>Så här fungerar lagringsemulatorn

Lagringsemulatorn använder en lokal Microsoft SQL Server 2012 Express LocalDB-instans för att emulera Azure-lagringstjänster. Du kan välja att konfigurera lagringsemulatorn för åtkomst till en lokal instans av SQL Server i stället för LocalDB-instansen. Mer information finns i avsnittet [Starta och initiera lagringsemulatorn](#start-and-initialize-the-storage-emulator) senare i den här artikeln.

Lagringsemulatorn ansluter till SQL Server eller LocalDB med Windows-autentisering.

Det finns vissa skillnader i funktionalitet mellan lagringsemulatorn och Azure-lagringstjänsterna. Mer information om dessa skillnader finns i avsnittet [Skillnader mellan lagringsemulatorn och Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) senare i den här artikeln.

## <a name="start-and-initialize-the-storage-emulator"></a>Starta och initiera lagringsemulatorn

Så här startar du Azure-lagringsemulatorn:

1. Välj **Start-knappen** eller tryck på Windows-tangenten. **Windows**
2. Börja skriva `Azure Storage Emulator`.
3. Välj emulatorn i listan över program som visas.

När lagringsemulatorn startar visas ett kommandotolksfönster. Du kan använda det här konsolfönstret för att starta och stoppa lagringsemmulatorn. Du kan också rensa data, hämta status och initiera emulatorn från kommandotolken. Mer information finns i [kommandoradsreferensavsnittet Lagringsmulator](#storage-emulator-command-line-tool-reference) senare i den här artikeln.

> [!NOTE]
> Azure-lagringsemulatorn kanske inte startar korrekt om en annan lagringsemulator, till exempel Azurite, körs på systemet.

När emulatorn körs visas en ikon i aktivitetsfältets meddelandefält i Windows.

När du stänger kommandotolksfönstret för lagringsemulator fortsätter lagringsemulatorn att köras. Om du vill visa konsolfönstret För lagringsemulator igen följer du föregående steg som om du startar lagringsemulatorn.

Första gången du kör lagringsemulatorn initieras den lokala lagringsmiljön åt dig. Initieringsprocessen skapar en databas i LocalDB och reserverar HTTP-portar för varje lokal lagringstjänst.

Lagringsemulatorn installeras som `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`standard till .

> [!TIP]
> Du kan använda [Microsoft Azure Storage Explorer](https://storageexplorer.com) för att arbeta med lokala lagringsemulatorresurser. Leta efter "(Emulator - Standardportar) (Nyckel)" under "Lokal & Ansluten" i resurserträdet för Storage Explorer när du har installerat och startat lagringsemulatorn.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initiera lagringsemulatorn för att använda en annan SQL-databas

Du kan använda kommandoradsverktyget för lagring emulator för att initiera lagringsemulatorn så att den pekar på en annan SQL-databasinstans än standardinstansen LocalDB:

1. Öppna konsolfönstret Lagringsmulator enligt beskrivningen i [avsnittet Start och initiera lagringsemmulatorn.](#start-and-initialize-the-storage-emulator)
1. I konsolfönstret skriver du följande `<SQLServerInstance>` kommando, där är namnet på SQL Server-instansen. Om du vill använda `(localdb)\MSSQLLocalDb` LocalDB anger du som SQL Server-instans.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Du kan också använda följande kommando, som leder emulatorn att använda standard SQL Server-instansen:

   `AzureStorageEmulator.exe init /server .`

   Du kan också använda följande kommando, som initierar om databasen till standardinstansen LocalDB:

   `AzureStorageEmulator.exe init /forceCreate`

Mer information om dessa kommandon finns i [Kommandoradsreferens för lagringsmulator](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Du kan använda [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) för att hantera DINA SQL Server-instanser, inklusive LocalDB-installationen. I dialogrutan SMSS Connect to `(localdb)\MSSQLLocalDb` **Server** anger du i fältet **Servernamn:** för att ansluta till LocalDB-instansen.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autentisera begäranden mot lagringsemulatorn

När du har installerat och startat lagringsemulatorn kan du testa koden mot den. Varje begäran du gör mot lagringsemulatorn måste godkännas, såvida det inte är en anonym begäran. Du kan auktorisera begäranden mot lagringsemulatorn med autentisering av delad nyckel eller med en signatur för delad åtkomst (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Auktorisera med autentiseringsuppgifter för delad nyckel

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Mer information om anslutningssträngar finns i [Konfigurera Azure Storage-anslutningssträngar](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Auktorisera med en signatur för delad åtkomst

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Vissa Azure-lagringsklientbibliotek, till exempel Xamarin-biblioteket, stöder endast autentisering med en SAS-token (Shared Access Signature). Du kan skapa SAS-token med [Lagringsutforskaren](https://storageexplorer.com/) eller ett annat program som stöder autentisering av delad nyckel.

Du kan också generera en SAS-token med hjälp av Azure PowerShell. I följande exempel genereras en SAS-token med fullständig behörighet till en blob-behållare:

1. Installera Azure PowerShell om du inte redan har (med den senaste versionen av Azure PowerShell-cmdlets rekommenderas). Installationsinstruktioner finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).
2. Öppna Azure PowerShell och kör följande `CONTAINER_NAME` kommandon och ersätt med ett namn som du väljer:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Den resulterande signatur-URI:n för delad åtkomst för den nya behållaren bör likna:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Signaturen för delad åtkomst som skapats med det här exemplet är giltig i en dag. Signaturen ger fullständig åtkomst (läsa, skriva, ta bort, lista) till blobbar i behållaren.

Mer information om signaturer för delad åtkomst finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](storage-sas-overview.md)

## <a name="addressing-resources-in-the-storage-emulator"></a>Adressering av resurser i lagringsemulatorn

Tjänstslutpunkterna för lagringsemulatorn skiljer sig från slutpunkterna för ett Azure-lagringskonto. Den lokala datorn gör inte domännamnsmatchning, vilket kräver att lagringsemulatorslutpunkterna är lokala adresser.

När du adresserar en resurs i ett Azure-lagringskonto använder du följande schema. Kontonamnet är en del av URI-värdnamnet och resursen som adresseras är en del av URI-sökvägen:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Följande URI är till exempel en giltig adress för en blob i ett Azure-lagringskonto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Eftersom den lokala datorn inte gör domännamnsmatchning är kontonamnet en del av URI-sökvägen i stället för värdnamnet. Använd följande URI-format för en resurs i lagringsemulatorn:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Följande adress kan till exempel användas för åtkomst till en blob i lagringsemulatorn:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Tjänstslutpunkterna för lagringsemulatorn är:

* Blob-tjänst:`http://127.0.0.1:10000/<account-name>/<resource-path>`
* Kötjänst:`http://127.0.0.1:10001/<account-name>/<resource-path>`
* Tabelltjänst:`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Adressering av kontot sekundärt med RA-GRS

Från och med version 3.1 stöder lagringsemulatorn geo redundant replikering (RA-GRS). Du kan komma åt den sekundära platsen genom att lägga till -sekundärt till kontonamnet. Följande adress kan till exempel användas för åtkomst till en blob med den skrivskyddade sekundära i lagringsemulatorn:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> För programmatisk åtkomst till sekundärt med lagringsemulatorn använder du storage client library för .NET version 3.2 eller senare. Mer information finns i [Microsoft Azure Storage Client Library for .NET.](https://msdn.microsoft.com/library/azure/dn261237.aspx)
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Kommandoradsreferens för lagringsmulator

Från och med version 3.0 visas ett konsolfönster när du startar lagringsemmulatorn. Använd kommandoraden i konsolfönstret för att starta och stoppa emulatorn. Du kan också fråga efter status och utföra andra åtgärder från kommandoraden.

> [!NOTE]
> Om du har installerat Microsoft Azure-beräkningsemulatorn visas en systemikon när du startar storage emulator. Högerklicka på ikonen för att visa en meny som ger ett grafiskt sätt att starta och stoppa lagringsemulatorn.
>
>

### <a name="command-line-syntax"></a>Syntax för kommandorad

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Alternativ

Om du vill visa en lista över alternativ skriver du `/help` i kommandotolken.

| Alternativ | Beskrivning | Kommando | Argument |
| --- | --- | --- | --- |
| **Start** |Startar lagringsemulatorn. |`AzureStorageEmulator.exe start [-inprocess]` |*-Upparbetning:* Starta emulatorn i den aktuella processen i stället för att skapa en ny process. |
| **Stopp** |Stoppar lagringsemulatorn. |`AzureStorageEmulator.exe stop` | |
| **Status** |Skriver ut lagringsemulatorns status. |`AzureStorageEmulator.exe status` | |
| **Rensa** |Rensar data i alla tjänster som anges på kommandoraden. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: Rensar blob-data. <br/>*kön*: Rensar ködata. <br/>*tabell*: Rensar tabelldata. <br/>*alla*: Rensar alla data i alla tjänster. |
| **Init** |Har enstaka initiering för att ställa in emulatorn. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: Anger den server som är värd för SQL-instansen. <br/>*-sqlinstance instanceName*: Anger namnet på den SQL-instans som ska användas i standardserverinstansen. <br/>*-forcecreate*: Tvingar skapandet av SQL-databasen, även om den redan finns. <br/>*-skipcreate*: Hoppar över skapandet av SQL-databasen. Detta har företräde framför -forcecreate.<br/>*-reserveports*: Försök att reservera HTTP-portar som är associerade med tjänsterna.<br/>*-unreserveports*: Försök att ta bort reservationer för HTTP-portar som är associerade med tjänsterna. Detta har företräde framför -reserveports.<br/>*-inprocess*: Utför initiering i den aktuella processen istället för att leka en ny process. Den aktuella processen måste startas med förhöjda behörigheter om portreservationer ändras. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Skillnader mellan lagringsemulatorn och Azure Storage

Eftersom lagringsemulatorn är en lokal emulerad miljö finns det skillnader mellan att använda emulatorn och ett Azure-lagringskonto i molnet:

* Lagringsemulatorn stöder endast ett enda fast konto och en välkänd autentiseringsnyckel.
* Lagringsemulatorn är inte en skalbar lagringstjänst och stöder inte ett stort antal samtidiga klienter.
* Som beskrivs i [Adressering av resurser i lagringsemulatorn](#addressing-resources-in-the-storage-emulator)hanteras resurser på olika sätt i lagringsemulatorn jämfört med ett Azure-lagringskonto. Skillnaden beror på att domännamnsmatchning är tillgänglig i molnet men inte på den lokala datorn.
* Från och med version 3.1 stöder lagringsemulatorkontot geo redundant replikering (RA-GRS). I emulatorn har alla konton RA-GRS aktiverat och det finns aldrig någon fördröjning mellan de primära och sekundära replikerna. Åtgärderna Hämta Blob Service Stats, Get Queue Service Stats och Get Table Service Stats stöds `LastSyncTime` på kontots sekundära konto och returnerar alltid värdet för svarselementet som aktuell tid enligt den underliggande SQL-databasen.
* Slutpunkterna Filtjänst och SMB-protokolltjänst stöds för närvarande inte i lagringsemmulatorn.
* Om du använder en version av lagringstjänsterna som inte stöds av emulatorn returnerar emulatorn ett Versionsfel som inte stöds av Avemulator (HTTP-statuskod 400 - Felaktig begäran).

### <a name="differences-for-blob-storage"></a>Skillnader för Blob-lagring

Följande skillnader gäller blob-lagring i emulatorn:

* Lagringsemulatorn stöder endast blobstorlekar på upp till 2 GB.
* Den maximala längden på ett blob-namn i lagringsemulatorn är 256 tecken, medan den maximala längden på ett blob-namn i Azure Storage är 1024 tecken.
* Inkrementell kopia gör att ögonblicksbilder från överskrivna blobbar kopieras, vilket returnerar ett fel på tjänsten.
* Get Page Ranges Diff fungerar inte mellan ögonblicksbilder som kopieras med inkrementell kopia blob.
* En Put Blob-åtgärd kan lyckas mot en blob som finns i lagringsemulatorn med ett aktivt lån även om låne-ID:t inte har angetts i begäran.
* Append Blob-åtgärder stöds inte av emulatorn. Om du försöker utföra en åtgärd på en tilläggsblob returnerar ett FeatureNotSupportedByEmulator-fel (HTTP-statuskod 400 - Felaktig begäran).

### <a name="differences-for-table-storage"></a>Skillnader för tabelllagring

Följande skillnader gäller för tabelllagring i emulatorn:

* Datumegenskaper i tabelltjänsten i lagringsemulatorn stöder endast det intervall som stöds av SQL Server 2005 (de måste vara senare än den 1 januari 1753). Alla datum före den 1 januari 1753 ändras till det här värdet. Datumprecisionen är begränsad till precisionen i SQL Server 2005, vilket innebär att datumen är exakta till 1/300:e sekund.
* Lagringsemulatorn stöder egenskapsvärden för partitionsnyckel och radnyckel på mindre än 512 byte vardera. Den totala storleken på kontonamnet, tabellnamnet och nyckelegenskapsnamnen tillsammans får inte överstiga 900 byte.
* Den totala storleken på en rad i en tabell i lagringsemulatorn är begränsad till mindre än 1 MB.
* I lagringsemulatorn stöder `Edm.Guid` egenskaper `Edm.Binary` för `Equal (eq)` datatyp eller endast operatorerna och `NotEqual (ne)` jämförelseoperatorn i frågefiltersträngar.

### <a name="differences-for-queue-storage"></a>Skillnader för kölagring

Det finns inga skillnader som är specifika för kölagring i emulatorn.

## <a name="storage-emulator-release-notes"></a>Viktig information om lagringsemulatorn

### <a name="version-510"></a>Version 5.10

* Lagringsemulatorn kommer inte att avvisa version 2019-07-07 av lagringstjänsterna på slutpunkter för Blob-, kö- och tabelltjänsten.

### <a name="version-59"></a>Version 5.9

* Lagringsemulatorn kommer inte att avvisa version 2019-02-02 av lagringstjänsterna på slutpunkter för Blob-, kö- och tabelltjänsten.

### <a name="version-58"></a>Version 5.8

* Lagringsemulatorn kommer inte att avvisa version 2018-11-09 av lagringstjänsterna på slutpunkter för Blob-, kö- och tabelltjänsten.

### <a name="version-57"></a>Version 5.7

* Fixade ett fel som skulle orsaka en krasch om loggning var aktiverad.

### <a name="version-56"></a>Version 5.6

* Lagringsemulatorn stöder nu slutpunkter för versions 2018-03-28 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter.

### <a name="version-55"></a>Version 5.5

* Lagringsemulatorn stöder nu slutpunkter för versions 2017-11-09 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter.
* Stöd har lagts till för egenskapen blob **Created,** som returnerar blobens skapandetid.

### <a name="version-54"></a>Version 5.4

* För att förbättra installationens stabilitet försöker emulatorn inte längre att reservera portar vid installationstiden. Om du vill ha portreservationer använder du alternativet *-reserveports* **för kommandot init** för att ange dem.

### <a name="version-53"></a>Version 5.3

* Lagringsemulatorn stöder nu slutpunkter för versions 2017-07-29 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter.

### <a name="version-52"></a>Version 5.2

* Lagringsemulatorn stöder nu slutpunkter för versions 2017-04-17 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter.
* Fixade ett fel där tabellegenskapsvärden inte var korrekt kodade.

### <a name="version-51"></a>Version 5.1

* Fixade ett fel där lagringsemulatorn returnerade `DataServiceVersion` huvudet i vissa svar där tjänsten inte var det.

### <a name="version-50"></a>Version 5.0

* Installationsprogrammet för lagringsemulatorn söker inte längre efter befintliga MSSQL- och .NET Framework-installationer.
* Installationsprogrammet för lagringsemulatorn skapar inte längre databasen som en del av installationen. Databasen skapas fortfarande om det behövs som en del av starten.
* Att skapa databaser kräver inte längre höjd.
* Portreservationer behövs inte längre för start.
* Lägger till `init`följande `-reserveports` alternativ i `-unreserveports` : (kräver höjd), (kräver höjd), `-skipcreate`.
* Alternativet Storage Emulator UI på systemfältet ikonen lanserar nu kommandoradsgränssnittet. Det gamla grafiska gränssnittet är inte längre tillgängligt.
* Vissa DLL-filer har tagits bort eller bytt namn.

### <a name="version-46"></a>Version 4.6

* Lagringsemulatorn stöder nu slutpunkter för versions 2016-05-31 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter.

### <a name="version-45"></a>Version 4.5

* Fixade ett fel som gjorde att installationen och initieringen misslyckades när stöddatabasen har bytt namn.

### <a name="version-44"></a>Version 4.4

* Lagringsemulatorn stöder nu slutpunkter för versions 2015-12-11 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter.
* Lagringsemulatorns skräpinsamling av blob-data är nu effektivare när det handlar om ett stort antal blobbar.
* Fixade ett fel som orsakade att ACL-XML-filen som skulle valideras något annorlunda än hur lagringstjänsten gör det.
* Fixade ett fel som ibland orsakade max- och min DateTime-värden som ska rapporteras i den felaktiga tidszonen.

### <a name="version-43"></a>Version 4.3

* Lagringsemulatorn stöder nu slutpunkter för versions 2015-07-08 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter.

### <a name="version-42"></a>Version 4.2

* Lagringsemulatorn stöder nu slutpunkter för versions 2015-04-05 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter.

### <a name="version-41"></a>Version 4.1

* Lagringsemulatorn stöder nu slutpunkter för versions 2015-02-21 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter. Den stöder inte de nya Append Blob-funktionerna.
* Emulatorn returnerar nu ett meningsfullt felmeddelande för versioner av lagringstjänster som inte stöds. Vi rekommenderar att du använder den senaste versionen av emulatorn. Om du får ett Versionsfel Avslag (HTTP-statuskod 400 - Felaktig begäran) laddar du ned den senaste versionen av emulatorn.
* Fixade ett fel där ett konkurrenstillstånd orsakade att tabellentitetsdata var felaktiga under samtidiga sammanfogningsåtgärder.

### <a name="version-40"></a>Version 4.0

* Den körbara lagringsemulatorn har bytt namn till *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Version 3.2

* Lagringsemulatorn stöder nu slutpunkter för versions 2014-02-14 av lagringstjänsterna på Blob-, kö- och tabelltjänstslutpunkter. Slutpunkter för filtjänst stöds för närvarande inte i lagringsemulatorn. Mer information om version 2014-02-14 finns i [Versionshantering för Azure Storage Services.](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services)

### <a name="version-31"></a>Version 3.1

* Läsåtkomst geo-redundant lagring (RA-GRS) stöds nu i lagringsemulatorn. De `Get Blob Service Stats` `Get Queue Service Stats`, `Get Table Service Stats` och API:erna stöds för kontots sekundära och returnerar alltid värdet för LastSyncTime-svarselementet som aktuell tid enligt den underliggande SQL-databasen. För programmatisk åtkomst till sekundärt med lagringsemulatorn använder du storage client library för .NET version 3.2 eller senare. Mer information finns i Microsoft Azure Storage Client Library for .NET Reference.

### <a name="version-30"></a>Version 3.0

* Azure-lagringsemulatorn levereras inte längre i samma paket som beräkningemulatorn.
* Det grafiska användargränssnittet för lagringsemulator är inaktuellt. Det har ersatts av ett skriptbart kommandoradsgränssnitt. Mer information om kommandoradsgränssnittet finns i Kommandoradsreferens för lagringsmulator. Det grafiska gränssnittet kommer att fortsätta att finnas i version 3.0, men det kan bara nås när Compute Emulator installeras genom att högerklicka på systemfältet ikonen och välja Visa lagring emulator UI.
* Version 2013-08-15 av Azure-lagringstjänster stöds nu fullt ut. (Tidigare stöddes den här versionen endast av förhandsversionen av Storage Emulator version 2.2.1.)

## <a name="next-steps"></a>Nästa steg

* Utvärdera den plattformsoberoende, community-underhållna lagringsemulatorn [Azurite](https://github.com/arafato/azurite)med öppen källkod . 
* [Azure Storage-exempel med .NET](../storage-samples-dotnet.md) innehåller länkar till flera kodexempel som du kan använda när du utvecklar ditt program.
* Du kan använda [Microsoft Azure Storage Explorer](https://storageexplorer.com) för att arbeta med resurser i ditt molnlagringskonto och i lagringsemmulatorn.
