---
title: Använd Azure storage-emulatorn för utveckling och testning | Microsoft Docs
description: Azure storage-emulatorn ger en kostnadsfri lokal utvecklingsmiljö för att utveckla och testa dina Azure Storage-program. Lär dig hur begäranden auktoriseras, hur du ansluter till emulatorn från ditt program och hur du använder kommandoradsverktyget.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 4f4c5870bb1bdd804aa4b2da8c46c0698b897798
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526189"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Använd Azure storage-emulatorn för utveckling och testning

Microsoft Azure storage-emulatorn tillhandahåller en lokal miljö som emulerar Azure Blob, Queue och Table-tjänster för utvecklingsändamål. Med storage-emulatorn kan testa du programmet mot lagringstjänster lokalt, utan att skapa en Azure-prenumeration och utan kostnad. När du är nöjd med hur programmet fungerar i emulatorn kan växla du till ett Azure storage-konto i molnet.

## <a name="get-the-storage-emulator"></a>Hämta lagringsemulatorn
Storage-emulatorn är tillgänglig som en del av den [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Du kan också installera storage-emulatorn genom att använda den [fristående installationsprogram](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (direct nedladdning). Om du vill installera storage-emulatorn måste du ha administratörsbehörighet på datorn.

Storage-emulatorn körs för närvarande endast på Windows. För de överväger en storage-emulatorn för Linux, ett alternativ är gemenskapen bibehålls, öppen källkod storage-emulatorn [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Data som skapats i en version av storage-emulatorn är inte säkert att vara tillgänglig när du använder en annan version. Om du vill spara dina data långsiktigt rekommenderar vi att du lagrar dessa data i ett Azure storage-konto och inte i storage-emulatorn.
> <p/>
> Storage-emulatorn är beroende av specifika versioner av OData-bibliotek. Ersätt OData-DLL: er som används av storage-emulatorn med andra versioner stöds inte och kan orsaka oförutsett beteende. Någon version av OData som stöds av storage-tjänsten kan dock användas för att skicka begäranden till emulatorn.
>

## <a name="how-the-storage-emulator-works"></a>Så här fungerar lagringsemulatorn
Lagringsemulatorn använder en lokal Microsoft SQL Server-instans och det lokala filsystemet för att emulera Azure storage-tjänster. Lagringsemulatorn använder en databas i Microsoft SQL Server 2012 Express LocalDB som standard. Du kan välja att konfigurera storage-emulatorn för att komma åt en lokal instans av SQL Server i stället för LocalDB-instans. Mer information finns i den [Start- och initiera lagringsemulatorn](#start-and-initialize-the-storage-emulator) senare i den här artikeln.

Storage-emulatorn ansluter till SQL Server eller LocalDB med hjälp av Windows-autentisering.

Det finns vissa skillnader i funktionalitet mellan storage-emulatorn och Azure storage-tjänster. Mer information om skillnaderna finns i den [skillnader mellan storage-emulatorn och Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) senare i den här artikeln.

## <a name="start-and-initialize-the-storage-emulator"></a>Starta och initiera lagringsemulatorn

Starta Azure storage-emulatorn:
1. Välj den **starta** knapp eller genom att trycka på den **Windows** nyckel.
2. Börja skriva `Azure Storage Emulator`.
3. Välj emulatorn i listan över visade program.

När storage-emulatorn startar visas Kommandotolkens fönster. Du kan använda det här konsolfönstret för att starta och stoppa lagringsemulatorn, rensa data, hämta status och starta emulatorn. Mer information finns i den [referens för Storage-emulatorn kommandoradsverktyget](#storage-emulator-command-line-tool-reference) senare i den här artikeln.

När emulatorn körs visas en ikon i aktivitetsfältets meddelandefält i Windows.

När du stänger fönstret storage-emulatorn Kommandotolken fortsätter lagringsemulatorn att köras. Följ föregående steg så att Storage-emulatorn konsolfönstret igen, som om startar storage-emulatorn.

Första gången du kör storage-emulatorn har miljö för lokal lagring initierats för dig. Initieringsprocessen skapar en databas i LocalDB och reserverar HTTP-portar för varje tjänst för lokal lagring.

Storage-emulatorn har installerats som standard att `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Du kan använda den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com) att arbeta med lokala lagringsemulatorresurser. Leta efter ”(utveckling)” under ”Lagringskonton” i trädet Lagringsutforskaren resurser när du har installerat och igång av storage-emulatorn.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initiera lagringsemulatorn för att använda en annan SQL-databas

Du kan använda kommandoradsverktyget för storage-emulatorn för att initiera storage-emulatorn att den pekar på en SQL-databasinstans än LocalDB standardinstansen:

1. Öppna konsolfönstret Lagringsemulatorn enligt beskrivningen i den [Start- och initiera lagringsemulatorn](#start-and-initialize-the-storage-emulator) avsnittet.
1. I konsolfönstret skriver du följande kommando, där `<SQLServerInstance>` är namnet på SQL Server-instansen. Om du vill använda LocalDB, ange `(localdb)\MSSQLLocalDb` som SQL Server-instansen.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  Du kan också använda följande kommando, som riktar emulatorn för att använda standardinstansen för SQL Server:

  `AzureStorageEmulator.exe init /server .\\`

  Du kan också använda följande kommando, där återinitierar databasen till LocalDB standardinstansen:

  `AzureStorageEmulator.exe init /forceCreate`

Mer information om dessa kommandon finns i [referens för Storage-emulatorn kommandoradsverktyget](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Du kan använda den [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) för att hantera dina SQL Server-instanser, inklusive LocalDB-installation. I SMSS **Anslut till Server** dialogrutan Ange `(localdb)\MSSQLLocalDb` i den **servernamn:** fält som du vill ansluta till LocalDB-instansen.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autentisering av förfrågningar mot lagringsemulatorn
När du har installerat och igång av storage-emulatorn kan testa du koden mot den. Precis som med Azure Storage i molnet, måste varje begäran som du har gjort mot storage-emulatorn ha behörighet, om det inte är en anonym begäran. Du kan godkänna begäranden mot lagringsemulatorn använder autentisering med delad nyckel eller med en signatur för delad åtkomst (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Auktorisera med autentiseringsuppgifter för delad nyckel
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Mer information om anslutningssträngar finns i [konfigurera Azure Storage-anslutningssträngar](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Auktorisera med signatur för delad åtkomst
Vissa Azure storage, klientbiblioteken, till exempel Xamarin-klientbibliotek har endast stöd för autentisering med signatur för delad åtkomsttoken. Du kan skapa SAS-token med ett verktyg som de [Lagringsutforskaren](http://storageexplorer.com/) eller ett annat program som har stöd för autentisering med delad nyckel.

Du kan också generera en SAS-token med hjälp av Azure PowerShell. I följande exempel skapar en SAS-token med fullständig behörighet till en blobbehållare:

1. Installera Azure PowerShell om du inte redan gjort med (den senaste versionen av Azure PowerShell cmdlet: ar rekommenderas). Installationsanvisningar finns i [installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Öppna Azure PowerShell och kör följande kommandon och Ersätt `ACCOUNT_NAME` och `ACCOUNT_KEY==` med dina autentiseringsuppgifter och `CONTAINER_NAME` med ett namn du väljer:

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Den resulterande signaturen för delad åtkomst URI för den nya behållaren ska vara liknar:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Signatur för delad åtkomst som skapats med det här exemplet är giltig i en dag. Signaturen ger fullständig åtkomst (läsa, skriva, ta bort, lista) till blobbar i behållaren.

Mer information om signaturer för delad åtkomst finns i [använda signaturer för delad åtkomst (SAS) i Azure Storage](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Hantera resurser i storage-emulatorn
Tjänsteslutpunkter för storage-emulatorn skiljer sig från dem på ett Azure storage-konto. Skillnaden beror på den lokala datorn inte utför Domännamnsmatchning, att storage-emulatorn slutpunkter vara lokala adresser.

När du har gått en resurs i ett Azure storage-konto ska använda du följande schema. Kontonamnet är en del av URI-värdnamn och resursen håller på att åtgärdas är en del av den URI-sökvägen:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Till exempel är följande URI: N en giltig adress för en blob i ett Azure storage-konto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Storage-emulatorn eftersom den lokala datorn inte utför Domännamnsmatchning, kontonamnet är dock en del av den URI-sökvägen i stället för värdnamnet. Använd följande URI-format för en resurs i storage-emulatorn:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Till exempel kan följande adress användas för att komma åt en blob i storage-emulatorn:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Tjänsteslutpunkter för storage-emulatorn är:

* BLOB-tjänsten: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Kötjänst: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Tabelltjänsten: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Kontot som sekundär med RA-GRS-adressering
Från och med version 3.1, storage-emulatorn har stöd för läsåtkomst till geografiskt redundant replikering (RA-GRS). För lagringsresurser både i molnet och i den lokala emulatorn, du kan komma åt den sekundära platsen genom att lägga till - sekundär till namnet på kontot. Till exempel kan följande adress användas för att komma åt en blob med hjälp av sekundärt skrivskyddade i storage-emulatorn:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> För programmässig åtkomst till sekundärt med storage-emulatorn använder du Lagringsklientbiblioteket för .NET version 3.2 eller senare. Se den [Microsoft Azure Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) mer information.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referens för Storage-emulatorn kommandoradsverktyget
Från och med version 3.0, visas ett konsolfönster när du startar Storage-emulatorn. Använda kommandoraden i konsolfönstret för att starta och stoppa emulatorn samt fråga om status och utföra andra åtgärder.

> [!NOTE]
> Om du har Microsoft Azure compute emulator installerad, visas en ikonen i systemfältet när du startar Storage-emulatorn. Högerklicka på ikonen för att visa en meny som ger ett grafiskt sätt att starta och stoppa Storage-emulatorn.
>
>

### <a name="command-line-syntax"></a>Kommandoradssyntax
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Alternativ
Om du vill visa en lista över alternativ skriver du `/help` i kommandotolken.

| Alternativ | Beskrivning | Kommando | Argument |
| --- | --- | --- | --- |
| **Börja** |Startar storage-emulatorn. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: starta emulatorn i den aktuella processen istället för att skapa en ny process. |
| **Stanna** |Stoppar storage-emulatorn. |`AzureStorageEmulator.exe stop` | |
| **Status** |Skriver status för storage-emulatorn. |`AzureStorageEmulator.exe status` | |
| **Rensa** |Tar bort data i alla tjänster som anges på kommandoraden. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*BLOB*: raderar blob-data. <br/>*kön*: tar bort kön data. <br/>*tabellen*: tar bort tabelldata. <br/>*alla*: tar bort alla data i alla tjänster. |
| **Init** |Utför enstaka initiering att konfigurera emulatorn. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-servern ServerNamn\InstansNamn*: Anger den server som är värd för SQL-instansen. <br/>*-sqlinstance instanceName*: Anger namnet på den SQL-instansen som ska användas i standardserverinstansen. <br/>*-forcecreate*: tvingar skapandet av SQL-databasen, även om den redan finns. <br/>*-skipcreate*: hoppar över skapandet av SQL-databasen. Detta har företräde framför - forcecreate.<br/>*-reserveports*: försöker att reservera HTTP-portar som är associerade med tjänsterna.<br/>*-unreserveports*: försök att ta bort reservationer för HTTP-portar som är associerade med tjänsterna. Detta har företräde framför - reserveports.<br/>*-inprocess*: utför initiering i den aktuella processen i stället för att skapa en ny process. Den aktuella processen måste startas med förhöjd behörighet om ändring av porten reservationer. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Skillnader mellan storage-emulatorn och Azure Storage
Eftersom storage-emulatorn är en emulerade miljö som körs i en lokal SQL-instans, finns det skillnader i funktionalitet mellan emulatorn och ett Azure storage-konto i molnet:

* Storage-emulatorn stöder bara ett enda fast konto och en välkänd autentiseringsnyckel.
* Storage-emulatorn är inte en skalbar tjänst och har inte stöd för ett stort antal samtidiga klienter.
* Mer information finns i [adressering av resurser i storage-emulatorn](#addressing-resources-in-the-storage-emulator), resurser behandlas på olika sätt i storage-emulatorn jämfört med ett Azure storage-konto. Den här skillnaden är eftersom Domännamnsmatchning är tillgänglig i molnet men inte på den lokala datorn.
* Från och med version 3.1, storage-emulatorn-konto har stöd för läsåtkomst till geografiskt redundant replikering (RA-GRS). Alla konton har RA-GRS aktiveras i emulatorn och det finns aldrig någon fördröjning mellan primära och sekundära replikerna. Hämta statistik för Blob-tjänsten, få statistik för tjänsten i kö och få statistik för tabellen tjänsten åtgärder stöds på kontot som sekundär och alltid returnerar värdet för den `LastSyncTime` svar element som den aktuella tiden enligt den underliggande SQL-databasen.
* Tjänsten File och tjänstslutpunkter för SMB-protokoll stöds inte för närvarande i storage-emulatorn.
* Om du använder en version av storage-tjänster som ännu inte stöds av emulatorn returnerar lagringsemulatorn VersionNotSupportedByEmulator-fel (HTTP-statuskod 400 - Felaktig begäran).

### <a name="differences-for-blob-storage"></a>Skillnaderna för Blob storage
Följande skillnader gäller för Blob storage i emulatorn:

* Storage-emulatorn endast stöder blob storlekar upp till 2 GB.
* Inkrementell kopia kan ögonblicksbilder från skrevs över BLOB-objekt som ska kopieras, som returnerar ett fel i tjänsten.
* Get Page Ranges Diff fungerar inte mellan ögonblicksbilder kopieras med hjälp av inkrementell kopiering av Blob.
* En Placera Blob-åtgärden kan lyckas mot en blob som finns i storage-emulatorn med ett aktivt lån, även om lån-ID inte har angetts i begäran.
* Bifoga Blob åtgärder inte stöds av emulatorn. Försök en åtgärd på en tilläggsblobb returnerar FeatureNotSupportedByEmulator-fel (HTTP-statuskod 400 - Felaktig begäran).

### <a name="differences-for-table-storage"></a>Skillnaderna för tabellagring
Följande skillnader gäller för tabellagring i emulatorn:

* Datum egenskaper i tabelltjänsten i lagringsemulatorn stöd för endast de som stöds av SQL Server 2005 (de måste vara senare än 1 januari 1753). Alla datum före den 1 januari 1753 ändras till det här värdet. Precisionen för datum är begränsad till precisionen för SQL Server 2005, vilket innebär att datum är exakt 1/300th av en sekund.
* Storage-emulatorn har stöd för partition och radnyckel viktiga egenskapsvärden på mindre än 512 byte varje. Dessutom får inte den totala storleken på det kontonamn och tabellnamn nyckelns egenskapsnamn tillsammans överskrida 900 byte.
* Den totala storleken på en rad i en tabell i storage-emulatorn är begränsad till mindre än 1 MB.
* I storage-emulatorn, skriver du egenskaper för data `Edm.Guid` eller `Edm.Binary` stöder bara den `Equal (eq)` och `NotEqual (ne)` jämförelseoperatorer i fråga filtrera strängar.

### <a name="differences-for-queue-storage"></a>Skillnaderna för kölagring
Det finns inga skillnader som är specifika för kölagring i emulatorn.

## <a name="storage-emulator-release-notes"></a>Storage-emulatorn viktig information

### <a name="version-55"></a>Version 5.5
* Storage-emulatorn har nu stöd för version 2017-11-09 storage-tjänster på Blob, Queue och Table service-slutpunkter.
* Stöd har lagts till för bloben **Skapad** egenskapen, som returnerar blobens Skapandetid.

### <a name="version-54"></a>Version 5.4
För att förbättra stabiliteten för installation, försöker emulatorn inte längre att reservera portar under installationen. Om port reservationer är det önskade kan du använda den *- reserveports* möjlighet att den **init** kommando för att ange dem.

### <a name="version-53"></a>Version 5.3
Storage-emulatorn har nu stöd för version 2017-07-29 storage-tjänster på Blob, Queue och Table service-slutpunkter.

### <a name="version-52"></a>Version 5.2
* Storage-emulatorn har nu stöd för version 2017-04-17 storage-tjänster på Blob, Queue och Table service-slutpunkter.
* Ett fel har åtgärdats där tabellen egenskapsvärden har inte som kodats korrekt.

### <a name="version-51"></a>Version 5.1
Ett fel har åtgärdats där lagringsemulatorn returnerade den `DataServiceVersion` rubrik i vissa svar där tjänsten inte kunde.

### <a name="version-50"></a>Version 5.0
* Storage-emulatorn inte längre kontrolleras för befintliga MSSQL och installerar .NET Framework.
* Storage-emulatorn installationsprogrammet skapar inte längre databasen som en del av installationen. Databasen skapas fortfarande om det behövs som en del av Start.
* Databasen skapas inte längre kräver utökad behörighet.
* Port reservationer behövs inte längre för start.
* Lägger till följande alternativ för att `init`: `-reserveports` (kräver utökad behörighet), `-unreserveports` (kräver utökad behörighet), `-skipcreate`.
* Storage-emulatorn Användargränssnittet alternativet på ikonen i systemfältet nu startar kommandoradsgränssnittet. Det gamla grafiska Användargränssnittet är inte längre tillgänglig.
* Vissa DLL-filer har tagits bort eller bytt namn.

### <a name="version-46"></a>Version 4.6
* Storage-emulatorn har nu stöd för version 2016-05-31 storage-tjänster på Blob, Queue och Table service-slutpunkter.

### <a name="version-45"></a>Version 4.5
* Ett fel som orsakade initieringen och installation av storage-emulatorn inte att kunna säkerhetskopiera databasen har bytt namn har åtgärdats.

### <a name="version-44"></a>Version 4.4
* Storage-emulatorn har nu stöd för version 2015-12-11 storage-tjänster på Blob, Queue och Table service-slutpunkter.
* Storage-emulatorn skräpinsamling blob-data är nu mer effektivt när du hanterar stora mängder BLOB-objekt.
* Ett fel som orsakade behållare ACL XML som ska verifieras lite annorlunda från hur storage-tjänsten gör det har åtgärdats.
* En bugg som ibland orsakade maximalt och minimalt antal DateTime-värden som ska rapporteras i felaktig tidszonen.

### <a name="version-43"></a>Version 4.3
* Storage-emulatorn har nu stöd för version 2015-07-08 storage-tjänster på Blob, Queue och Table service-slutpunkter.

### <a name="version-42"></a>Version 4.2
* Storage-emulatorn har nu stöd för version 2015-04-05 storage-tjänster på Blob, Queue och Table service-slutpunkter.

### <a name="version-41"></a>Version 4.1
* Storage-emulatorn har nu stöd för version 2015-02-21 storage-tjänster på Blob, Queue och Table-tjänstslutpunkter, förutom de nya funktionerna i Lägg till Blob.
* Om du använder en version av storage-tjänster som ännu inte stöds av emulatorn returnerar felmeddelandet meningsfulla i emulatorn. Vi rekommenderar att du använder den senaste versionen av emulatorn. Om det uppstår ett fel i VersionNotSupportedByEmulator (HTTP-statuskod 400 - Felaktig begäran) kan hämta den senaste versionen av storage-emulatorn.
* En bugg där en ”vinner” villkor som orsakade tabell entitetsdata om du vill vara felaktigt under samtidiga merge-operationer.

### <a name="version-40"></a>Version 4.0
* Storage-emulatorn körbara har bytt namn till *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Version 3.2
* Storage-emulatorn har nu stöd för version 2014-02-14 storage-tjänster på Blob, Queue och Table service-slutpunkter. Tjänstslutpunkter i filen stöds inte för närvarande i storage-emulatorn. Se [versionshantering för Azure Storage-tjänster](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) mer information om version 2014-02-14.

### <a name="version-31"></a>Version 3.1
* Läsåtkomst till geografiskt redundant lagring (RA-GRS) stöds nu i storage-emulatorn. Hämta statistik för Blob-tjänsten, få statistik för tjänsten i kö och hämta tabell Service Stats API: er som stöds för kontot som sekundär och returnerar alltid värdet för elementet LastSyncTime svar som den aktuella tiden enligt den underliggande SQL-databasen. För programmässig åtkomst till sekundärt med storage-emulatorn använder du Lagringsklientbiblioteket för .NET version 3.2 eller senare. Information finns i Microsoft Azure Storage-klientbiblioteket för .NET-referens.

### <a name="version-30"></a>Version 3.0
* Azure storage-emulatorn levereras inte längre i samma paket som beräkningsemulatorn.
* Storage-emulatorn grafiskt användargränssnitt är inaktuell och ersatts med ett skriptbara kommandoradsgränssnitt. Information om hur kommandoradsgränssnittet, finns i Storage-emulatorn Kommandoradsreferens verktyget. Det grafiska gränssnittet fortsätter att vara tillgängliga i version 3.0, men det kan bara användas när Compute-emulatorn har installerats genom att högerklicka på ikonen i systemfältet och välja Visa Användargränssnittet för Storage-emulatorn.
* Version 2013-08-15, Azure storage-tjänster stöds nu fullt ut. (Tidigare den här versionen har stöds endast av Storage-emulatorn version 2.2.1 förhandsversion.)

## <a name="next-steps"></a>Nästa steg

* Utvärdera lagringsemulatorn plattformsoberoende, community-underhålls öppen källkod [Azurite](https://github.com/arafato/azurite). 
* [Azure Storage-exempel med hjälp av .NET](../storage-samples-dotnet.md) innehåller länkar till flera kodexempel som du kan använda när du utvecklar dina program.
* Du kan använda den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com) att arbeta med resurser i din cloud Storage-konto och i storage-emulatorn.