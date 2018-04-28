---
title: Använd Azure storage-emulatorn för utveckling och testning | Microsoft Docs
description: Azure storage-emulatorn är en kostnadsfri lokal utvecklingsmiljö för att utveckla och testa dina Azure Storage-program. Lär dig hur begäranden autentiseras, hur du ansluter till emulatorn från ditt program och hur du använder kommandoradsverktyget.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: f480b059-df8a-4a63-b05a-7f2f5d1f5c2a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: tamram
ms.openlocfilehash: f98b8c3a8217b60fd0ba3754ac4ba72e09039f24
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Använd Azure storage-emulatorn för utveckling och testning

Microsoft Azure storage-emulatorn tillhandahåller en lokal miljö som emulerar tjänsterna Azure Blob, köer och tabell för utveckling. Du kan använda lagringsemulatorn för att testa programmet mot lagringstjänster lokalt, utan att skapa en Azure-prenumeration eller kostnader. När du är nöjd med hur programmet fungerar i emulatorn kan växla du till med ett Azure storage-konto i molnet.

## <a name="get-the-storage-emulator"></a>Hämta storage-emulatorn
Storage-emulatorn är tillgänglig som en del av den [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Du kan också installera storage-emulatorn genom att använda den [fristående installationsprogram](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (direct nedladdning). Om du vill installera storage-emulatorn måste du ha administratörsbehörighet på datorn.

Storage-emulatorn för närvarande kan endast köras på Windows. För de överväger storage-emulatorn för Linux, ett alternativ är gemenskapen underhålls öppen källkod storage-emulatorn [Azurite](https://github.com/azure/azurite).

> [!NOTE]
> Data som har skapats i en version av storage-emulatorn är inte säkert att vara tillgänglig när du använder en annan version. Om du behöver att spara dina data på lång sikt, rekommenderar vi att du lagrar data i Azure storage-konto i stället för storage-emulatorn.
> <p/>
> Storage-emulatorn är beroende av specifika versioner av OData-bibliotek. Ersätt OData-DLL: er som används av storage-emulatorn med andra versioner stöds inte och kan orsaka oväntade resultat. Någon version av OData som stöds av lagringstjänsten kan dock användas för att skicka begäranden till emulatorn.
>

## <a name="how-the-storage-emulator-works"></a>Så här fungerar storage-emulatorn
Storage-emulatorn använder en lokal Microsoft SQL Server-instans och det lokala filsystemet för att emulera Azure storage-tjänster. Som standard använder lagringsemulatorn en databas i Microsoft SQL Server 2012 Express LocalDB. Du kan välja att konfigurera lagringsemulatorn för att få åtkomst till en lokal instans av SQL Server i stället för LocalDB-instans. Mer information finns i [Start- och initiera storage-emulatorn](#start-and-initialize-the-storage-emulator) senare i den här artikeln.

Storage-emulatorn ansluter till SQL Server eller LocalDB med Windows-autentisering.

Det finns vissa skillnader i funktionalitet mellan storage-emulatorn och Azure storage-tjänster. Mer information om skillnaderna finns i [skillnader mellan storage-emulatorn och Azure Storage](#differences-between-the-storage-emulator-and-azure-storage) senare i den här artikeln.

## <a name="start-and-initialize-the-storage-emulator"></a>Starta och initiera storage-emulatorn

### <a name="run-the-azure-storage-emulator-in-dockerhttpshubdockercomrmicrosoftazure-storage-emulator"></a>[Kör Azure storage-emulatorn i Docker](https://hub.docker.com/r/microsoft/azure-storage-emulator/)
```
docker run -p 10000:10000 -p 10001:10001 -p 10002:10002 microsoft/azure-storage-emulator
```

### <a name="using-sdk"></a>Med hjälp av SDK

Så här startar Azure storage-emulatorn:
1. Välj den **starta** knappen eller tryck på den **Windows** nyckel.
1. Börja skriva `Azure Storage Emulator`.
1. Välj emulatorn från listan med visade program.

När storage-emulatorn startar visas Kommandotolkens fönster. Du kan använda det här konsolfönstret för att starta och stoppa storage-emulatorn, ta bort data, hämta status och initiera emulatorn. Mer information finns i [referens för Storage-emulatorn kommandoradsverktyget](#storage-emulator-command-line-tool-reference) senare i den här artikeln.

När emulatorn körs visas en ikon i meddelandefältet i Aktivitetsfältet.

När du stänger fönstret storage-emulatorn Kommandotolken fortsätter storage-emulatorn att köras. Följ föregående steg om du vill öppna konsolfönstret Storage-emulatorn igen, som om startar storage-emulatorn.

Första gången du kör storage-emulatorn har miljö för lokal lagring initierats för dig. Initieringsprocessen skapar en databas i LocalDB och reserverar http-portar för varje tjänst för lokal lagring.

Storage-emulatorn installeras som standard till `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> Du kan använda den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com) att arbeta med emulatorn för lokala lagringsresurser. Leta efter ”(utveckling)” under ”Lagringskonton” i trädet Lagringsutforskaren resurser när du har installerat och igång storage-emulatorn.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initiera lagringsemulatorn för att använda en annan SQL-databas
Du kan använda kommandoradsverktyget storage-emulatorn för att initiera storage-emulatorn att peka till en SQL-databasinstans än standard LocalDB-instans:

1. Öppna konsolfönstret Storage-emulatorn som beskrivs i den [Start- och initiera storage-emulatorn](#start-and-initialize-the-storage-emulator) avsnitt.
1. Skriv följande kommando i konsolfönstret, där `<SQLServerInstance>` är namnet på SQL Server-instansen. Om du vill använda LocalDB, ange `(localdb)\MSSQLLocalDb` som SQL Server-instansen.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  Du kan också använda följande kommando, som riktar emulatorn ska använda standardinstansen för SQL Server:

  `AzureStorageEmulator.exe init /server .\\`

  Eller, du kan använda följande kommando, som initierar databasen till standard LocalDB-instans:

  `AzureStorageEmulator.exe init /forceCreate`

Mer information om dessa kommandon finns [referens för Storage-emulatorn kommandoradsverktyget](#storage-emulator-command-line-tool-reference).

> [!TIP]
> Du kan använda den [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) för att hantera SQL Server-instanser, inklusive LocalDB-installation. I SMSS **Anslut till Server** dialogrutan Ange `(localdb)\MSSQLLocalDb` i den **servernamn:** fält som du vill ansluta till LocalDB-instans.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autentisering av förfrågningar mot storage-emulatorn
När du har installerat och igång lagringsemulatorn kan testa du din kod mot den. Precis som med Azure Storage i molnet, måste varje begäran som du gör mot storage-emulatorn autentiseras, såvida det inte är en anonym begäran. Du kan autentisera begäranden mot storage-emulatorn använder autentisering med delad nyckel eller med en signatur för delad åtkomst (SAS).

### <a name="authenticate-with-shared-key-credentials"></a>Autentisera med delad nyckel autentiseringsuppgifter
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Mer information om anslutningssträngar finns [konfigurera Azure Storage-anslutningssträngar](../storage-configure-connection-string.md).

### <a name="authenticate-with-a-shared-access-signature"></a>Autentisering med signatur för delad åtkomst
Vissa Azure storage-klientbibliotek, till exempel Xamarin-biblioteket har endast stöd för autentisering med signatur (SAS) för delade åtkomsttoken. Du kan skapa ett SAS-token med ett verktyg som den [Lagringsutforskaren](http://storageexplorer.com/) eller ett annat program som stöder autentisering med delad nyckel.

Du kan också generera en SAS-token med hjälp av Azure PowerShell. I följande exempel skapar en SAS-token med fullständig behörighet till en blob-behållare:

1. Installera Azure PowerShell om du inte redan gjort med (den senaste versionen av Azure PowerShell cmdlet: ar rekommenderas). Installationsanvisningar finns i [installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Öppna Azure PowerShell och kör följande kommandon och ersätta `ACCOUNT_NAME` och `ACCOUNT_KEY==` med dina autentiseringsuppgifter och `CONTAINER_NAME` med ett namn du väljer:

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Den resulterande signaturen för delad åtkomst URI för den nya behållaren bör likna följande:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Signatur för delad åtkomst som skapats med det här exemplet är giltig i en dag. Signaturen ger fullständig åtkomst (läsa, skriva, ta bort, lista) till blobbar i behållaren.

Mer information om signaturer för delad åtkomst finns [använder signaturer för delad åtkomst (SAS) i Azure Storage](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Hantera resurser i storage-emulatorn
Service-slutpunkter för storage-emulatorn skiljer sig från de Azure storage-konto. Skillnaden beror på den lokala datorn inte utföra namnmatchning för domänen som kräver att storage-emulatorn slutpunkter vara lokala adresser.

När du adresserar en resurs i ett Azure storage-konto kan du använda följande schema. Kontonamnet är en del av URI-värdnamn och resursen står inför är en del av URI-sökväg:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Till exempel är följande URI: N en giltig adress för en blobb i Azure storage-konto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Storage-emulatorn eftersom den lokala datorn inte utföra namnmatchning för domänen, namnet på kontot är en del av URI-sökväg i stället för värdnamnet. Använd följande URI-format för en resurs i storage-emulatorn:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Följande adress kan till exempel användas för åtkomst till en blobb i storage-emulatorn:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Service-slutpunkter för storage-emulatorn är:

* BLOB-tjänsten: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Kötjänsten: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Tabelltjänsten: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Kontot som sekundär med RA-GRS-adressering
Från och med version 3.1, stöder storage-emulatorn geo-redundant replikering för läsbehörighet (RA-GRS). För lagringsresurser både i molnet och lokala emulatorn, du kan komma åt den sekundära platsen genom att lägga till - sekundär i kontonamnet. Följande adress kan till exempel användas för att komma åt en blob med skrivskyddad sekundär i storage-emulatorn:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Använd Storage-klientbiblioteket för .NET version 3.2 eller senare för programmatisk åtkomst till sekundärt med storage-emulatorn. Finns det [Microsoft Azure Storage-klientbibliotek för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) mer information.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referens för Storage-emulatorn kommandoradsverktyget
Från och med version 3.0, visas ett konsolfönster när du startar Storage-emulatorn. Använda kommandoraden i konsolfönstret för att starta och stoppa emulatorn samt fråga om status och utföra andra åtgärder.

> [!NOTE]
> Om du har Microsoft Azure compute emulator installerad visas en ikon i systemet när du startar Storage-emulatorn. Högerklicka på ikonen visas en meny som ger ett grafiskt sätt att starta och stoppa Storage-emulatorn.
>
>

### <a name="command-line-syntax"></a>Kommandoradssyntax
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Alternativ
Om du vill visa listan över alternativ skriver `/help` i Kommandotolken.

| Alternativ | Beskrivning | Kommando | Argument |
| --- | --- | --- | --- |
| **Börja** |Startar storage-emulatorn. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: starta emulatorn i den aktuella processen i stället för att skapa en ny process. |
| **Stanna** |Stoppar storage-emulatorn. |`AzureStorageEmulator.exe stop` | |
| **Status** |Skriver ut status för storage-emulatorn. |`AzureStorageEmulator.exe status` | |
| **Rensa** |Tar bort data i alla tjänster som anges på kommandoraden. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*BLOB*: raderar blob-data. <br/>*kön*: tar bort data för kön. <br/>*tabellen*: rensar tabelldata. <br/>*alla*: tar bort alla data i alla tjänster. |
| **Init** |Utför en initieringen att ställa in emulatorn. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-servern ServerNamn\InstansNamn*: Anger den server som värd för SQL-instansen. <br/>*-sqlinstance instanceName*: Anger namnet på SQL-instans som ska användas i standardserverinstansen. <br/>*-forcecreate*: tvingar skapa SQL-databasen, även om den redan finns. <br/>*-skipcreate*: hoppar över skapandet av SQL-databasen. Detta åsidosätter - forcecreate.<br/>*-reserveports*: försöker att reservera HTTP-portar som är kopplade till tjänsterna.<br/>*-unreserveports*: försök att ta bort reservationer för HTTP-portar som är kopplade till tjänsterna. Detta åsidosätter - reserveports.<br/>*-inprocess*: utför initieringen i den aktuella processen i stället för att skapa en ny process. Den aktuella processen måste startas med förhöjda behörigheter om ändring av porten reservationer. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Skillnader mellan storage-emulatorn och Azure Storage
Eftersom storage-emulatorn är en emulerade miljö som körs i en lokal SQL-instans, finns det skillnader i funktionalitet mellan emulatorn och ett Azure storage-konto i molnet:

* Storage-emulatorn stöder bara ett fast konto och en välkänd autentiseringsnyckel.
* Storage-emulatorn är inte en skalbar lagring-tjänst och har inte stöd för ett stort antal samtidiga klienter.
* Enligt beskrivningen i [adressering resurser i lagringsemulatorn](#addressing-resources-in-the-storage-emulator), resurser behandlas på olika sätt i storage-emulatorn jämfört med ett Azure storage-konto. Denna skillnad beror på att domänen namnmatchning finns i molnet men inte på den lokala datorn.
* Från och med version 3.1, stöder storage-emulatorn konto geo-redundant replikering för läsbehörighet (RA-GRS). Alla konton har RA-GRS aktiverad i emulatorn och det finns aldrig någon fördröjning mellan de primära och sekundära replikerna. Åtgärderna hämta statistik för Blob-tjänsten, få statistik för kön tjänsten och hämta Service tabellstatistik stöds på kontot som sekundär och returneras alltid värdet för den `LastSyncTime` svar element som den aktuella tiden enligt den underliggande SQL-databasen.
* Tjänsten File och slutpunkter för SMB-protokollet stöds inte för närvarande i storage-emulatorn.
* Om du använder en version av storage-tjänster som ännu inte stöds av emulatorn returnerar lagringsemulatorn VersionNotSupportedByEmulator-fel (HTTP-statuskod 400 - Felaktig begäran).

### <a name="differences-for-blob-storage"></a>Skillnaderna för Blob storage
Blob storage i emulatorn gäller följande skillnader:

* Storage-emulatorn endast stöder blob storlek upp till 2 GB.
* Inkrementell kopia kan ögonblicksbilder från över blobbar som ska kopieras som returnerar ett fel på tjänsten.
* Get-sidintervall Diff fungerar inte mellan ögonblicksbilder kopieras med hjälp av inkrementell kopiera Blob.
* En Blob Put-åtgärden kan lyckas mot en blob som finns i storage-emulatorn med en aktiv livslängd, även om lease-ID inte har angetts i begäran.
* Lägg till Blob-åtgärder inte stöds av emulatorn. Försöker utföra en åtgärd på en tilläggsblobb returnerar FeatureNotSupportedByEmulator-fel (HTTP-statuskod 400 - Felaktig begäran).

### <a name="differences-for-table-storage"></a>Skillnaderna för tabellagring
Table storage i emulatorn gäller följande skillnader:

* Datum egenskaper i tabelltjänsten i lagringsemulatorn stöder endast det intervall som stöds av SQL Server 2005 (de måste vara senare än 1 januari 1753). Alla datum före den 1 januari 1753 har ändrats till det här värdet. Precisionen för datum är begränsad till precisionen för SQL Server 2005, vilket innebär att datum är exakt 1/300th av en sekund.
* Storage-emulatorn stöder partition nyckel och raden nyckelegenskapen värden för mindre än 512 byte. Dessutom kan får inte den totala storleken på kontonamnet, tabellnamnet och nyckelegenskapen namn tillsammans överskrida 900 byte.
* Den totala storleken på en rad i en tabell i storage-emulatorn är begränsad till mindre än 1 MB.
* Egenskaper för data Skriv i storage-emulatorn `Edm.Guid` eller `Edm.Binary` stöder bara den `Equal (eq)` och `NotEqual (ne)` jämförelseoperatorer i frågan filtrera strängar.

### <a name="differences-for-queue-storage"></a>Skillnaderna för Queue storage
Det finns inga skillnader som är specifika för Queue storage i emulatorn.

## <a name="storage-emulator-release-notes"></a>Storage-emulatorn viktig information
### <a name="version-52"></a>Version 5.2
* Storage-emulatorn stöder nu version 2017-04-17 av lagringstjänsterna på Blob, köer och tabellen slutpunkter.
* Fast ett programfel där egenskapsvärden har inte som kodats korrekt.

### <a name="version-51"></a>Version 5.1
* Fast ett programfel där lagringsemulatorn returnerade den `DataServiceVersion` huvudet i vissa svar där tjänsten inte kunde.

### <a name="version-50"></a>Version 5.0
* Storage-emulatorn inte längre kontrolleras för befintliga MSSQL och installerar .NET Framework.
* Storage-emulatorn installationsprogrammet skapar inte längre databasen som en del av installationen. Databasen kommer fortfarande att skapas vid behov som en del av Start.
* Databasen skapas inte längre kräver utökad behörighet.
* Port reservationer behövs inte längre för start.
* Lägger till följande alternativ för att `init`: `-reserveports` (kräver höjning) `-unreserveports` (kräver höjning) `-skipcreate`.
* Storage-Emulator UI alternativet på ikonen i systemfältet nu startar kommandoradsgränssnittet. Den gamla GUI är inte längre tillgänglig.
* Vissa DLL-filer har tagits bort eller bytt namn.

### <a name="version-46"></a>Version 4.6
* Storage-emulatorn stöder nu version 2016-05-31 av lagringstjänsterna på Blob, köer och tabellen slutpunkter.

### <a name="version-45"></a>Version 4.5
* Fast ett fel som orsakade initieringen och installation av lagringsemulatorn för att kunna säkerhetskopiera databasen har bytt namn.

### <a name="version-44"></a>Version 4.4
* Storage-emulatorn stöder nu version 2015-12-11 av lagringstjänsterna på Blob, köer och tabellen slutpunkter.
* Storage-emulatorn skräpinsamling blob-data är nu mer effektiv när du hanterar stort antal blobbar.
* Fast ett fel som orsakade att behållarens ACL XML som ska verifieras lite annorlunda från hur lagringstjänsten fungerar.
* Fast ett programfel som ibland orsakade max och min DateTime-värden som ska rapporteras i felaktigt tidszon.

### <a name="version-43"></a>Version 4.3
* Storage-emulatorn stöder nu version 2015-07-08 av lagringstjänsterna på Blob, köer och tabellen slutpunkter.

### <a name="version-42"></a>Version 4.2
* Storage-emulatorn stöder nu version 2015-04-05 av lagringstjänsterna på Blob, köer och tabellen slutpunkter.

### <a name="version-41"></a>Version 4.1
* Storage-emulatorn stöder nu version 2015-02-21 storage-tjänster på Blob, köer och tabellen slutpunkter, förutom de nya funktionerna i Lägg till Blob.
* Om du använder en version av storage-tjänster som ännu inte stöds av emulatorn returnerar emulatorn ett beskrivande felmeddelande. Vi rekommenderar att du använder den senaste versionen av emulatorn. Om det uppstår ett fel i VersionNotSupportedByEmulator (HTTP-statuskod 400 - Felaktig begäran) kan hämta den senaste versionen av storage-emulatorn.
* Fasta ett programfel inom vilket en RAS tillståndet som orsakade tabell entiteten för att vara felaktigt under samtidiga merge-operationer.

### <a name="version-40"></a>Version 4.0
* Storage-emulatorn körbara har bytt namn till *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Version 3.2
* Storage-emulatorn har nu stöd för version 2014-02-14 av lagringstjänsterna på Blob, köer och tabellen slutpunkter. Filen Tjänsteslutpunkter stöds inte för närvarande i storage-emulatorn. Se [versionshantering för Azure Storage-tjänster](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) mer information om version 2014-02-14.

### <a name="version-31"></a>Version 3.1
* Geo-redundant lagring med läsbehörighet (RA-GRS) stöds nu i storage-emulatorn. Hämta statistik för Blob-tjänsten, få statistik för kön tjänsten och hämta tabellen Service Stats API: er som stöds för kontot som sekundär och returneras alltid värdet för elementet LastSyncTime svar som den aktuella tiden enligt den underliggande SQL-databasen. Använd Storage-klientbiblioteket för .NET version 3.2 eller senare för programmatisk åtkomst till sekundärt med storage-emulatorn. Information finns i Microsoft Azure Storage-klientbibliotek för .NET-referens.

### <a name="version-30"></a>Version 3.0
* Azure storage-emulatorn levereras inte längre i samma paket som beräkningsemulatorn.
* Storage-emulatorn grafiskt gränssnitt har ersatts av ett skript kommandoradsgränssnitt. Mer information om kommandoradsgränssnittet finns i Storage-emulatorn Kommandoradsreferens verktyget. Det grafiska gränssnittet fortsätter att vara tillgängliga i version 3.0, men den kan endast användas när Compute-emulatorn har installerats genom att högerklicka på ikonen i systemfältet och välja Visa lagring Emulator UI.
* Version 2013-08-15 i Azure-lagringstjänster har nu fullt stöd. (Den här versionen har tidigare endast stöds av Storage-emulatorn version 2.2.1 förhandsgranskning.)

## <a name="next-steps"></a>Nästa steg

* Utvärdera plattformsoberoende, community-underhålls öppen källkod storage-emulatorn [Azurite](https://github.com/arafato/azurite). 
* [Azure Storage-exempel med hjälp av .NET](../storage-samples-dotnet.md) innehåller länkar till flera kodexempel som du kan använda när du utvecklar programmet.
* Du kan använda den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com) att arbeta med resurserna i ditt moln Storage-konto och storage-emulatorn.
