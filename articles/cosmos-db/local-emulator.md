---
title: Utveckla lokalt med Azure Cosmos DB-emulatorn | Microsoft Docs
description: Med Azure Cosmos DB-emulatorn kan du utveckla och testa ditt program lokalt kostnadsfritt, utan att skapa en Azure-prenumeration.
services: cosmos-db
keywords: Azure Cosmos DB-emulator
author: David-Noble-at-work
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: danoble
ms.openlocfilehash: 7067a71eea3ffbfadf006a102ee926fb15347f63
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423654"
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Använda Azure Cosmos DB-emulatorn för lokal utveckling och testning

<table>
<tr>
  <td><strong>Binärfiler</strong></td>
  <td>[Ladda ned MSI](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Docker-källa</strong></td>
  <td>[Github](https://github.com/Azure/azure-cosmos-db-emulator-docker)</td>
</tr>
</table>
  
Azure Cosmos DB-emulatorn ger en lokal miljö som emulerar Azure Cosmos DB-tjänsten för utveckling. Med Azure Cosmos DB-emulatorn kan du utveckla och testa ditt program lokalt, utan att skapa en Azure-prenumeration och utan kostnad. När du är nöjd med hur programmet fungerar i Azure Cosmos DB-emulatorn kan du växla till ett Azure Cosmos DB-konto i molnet. Du kan använda Azure Cosmos DB Emulator med samtliga API:er – SQL, MongoDB, Cassandra, Gremlin och Table.

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Installera emulatorn
> * Autentisera förfrågningar
> * Använda Datautforskaren i emulatorn
> * Exportera SSL-certifikat
> * Anropa emulatorn från kommandoraden
> * Köra emulatorn i Docker för Windows
> * Samla in spårningsfiler
> * Felsökning

## <a name="how-the-emulator-works"></a>Så här fungerar emulatorn

Azure Cosmos DB-emulatorn erbjuder mycket tillförlitlig emulering av Azure Cosmos DB-tjänsten. Den stöder identiska funktioner som Azure Cosmos DB, inklusive stöd för att skapa och fråga JSON-dokument, etablera och skala samlingar och köra lagrade procedurer och utlösare. Du kan utveckla och testa program med Azure Cosmos DB-emulatorn och distribuera dem till Azure i global skala genom att bara göra en enda konfigurationsändring i anslutningens slutpunkt för Azure Cosmos DB.

Även om emuleringen av Azure Cosmos DB-tjänsten är verklighetstrogen så implementeras emulatorn på ett annat sätt än tjänsten. Till exempel använder emulatorn standardkomponenter i operativsystemet som det lokala filsystemet för beständighet och HTTP-protokollstacken för anslutningar. Funktioner som förlitar sig på Azure-infrastruktur som global replikering, latensvärden på enstaka millisekunder för läsning/skrivning och justerade konsekvensnivåer inte är tillgängliga.

## <a name="differences-between-the-emulator-and-the-service"></a>Skillnader mellan emulatorn och tjänsten 
Eftersom Azure Cosmos DB-emulatorn tillhandahåller en emulerad miljö som körs på en lokal utvecklararbetsstation finns det några skillnader mellan emulatorn och ett Azure Cosmos DB-konto i molnet:

* För närvarande stöder datautforskaren i emulatorn endast SQL API-samlingar och MongoDB-samlingar. API:er för Table, Graph och Cassandra stöds ännu inte.  
* Azure Cosmos DB-emulatorn stöder endast ett enda fast konto och en välkänd huvudnyckel.  Nyckelomgenerering fungerar inte i Azure Cosmos DB-emulatorn.
* Azure Cosmos DB-emulatorn är inte en skalbar tjänst och stöder inte ett stort antal samlingar.
* Azure Cosmos DB-emulatorn kan inte simulera olika [Azure Cosmos DB-konsekvensnivåer](consistency-levels.md).
* Azure Cosmos DB-emulatorn simulerar inte [replikering över flera regioner](distribute-data-globally.md).
* Azure Cosmos DB-emulatorn stöder inte åsidosättning av tjänstkvoter som är tillgänglig i Azure Cosmos DB-tjänsten (exempelvis begränsningar för dokumentstorlekar, ökad lagring av partitionerad samling).
* Eftersom ditt exemplar av Azure Cosmos DB-emulatorn eventuellt inte är uppdaterat med de senaste ändringarna av Azure Cosmos DB-tjänsten bör du använda [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) för att uppskatta behovet av produktionsdataflöden (RU:er) för programmet.

## <a name="system-requirements"></a>Systemkrav
Azure Cosmos DB-emulatorn har följande krav på maskin- och programvara:

* Programvarukrav
  * Windows Server 2012 R2, Windows Server 2016 eller Windows 10
*   Lägsta maskinvarukrav
  * 2 GB RAM-minne
  * 10 GB ledigt hårddiskutrymme

## <a name="installation"></a>Installation
Du kan ladda ned och installera Azure Cosmos DB-emulatorn från [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) eller köra emulatorn på Docker för Windows. Instruktioner om hur du använder emulatorn i Docker för Windows finns i [Köra med Docker](#running-on-docker). 

> [!NOTE]
> Om du ska installera, konfigurera och köra Azure Cosmos DB-emulatorn måste du ha administratörsbehörighet på datorn.

## <a name="running-on-windows"></a>Köra i Windows

Du kan starta Azure Cosmos DB-emulatorn genom att först klicka på Start eller trycka på Windows-tangenten. Börja skriva **Azure Cosmos DB Emulator** och välj emulatorn från listan med program. 

![Välj Start-knappen eller tryck på Windows-tangenten, börja skriva **Azure Cosmos DB Emulator** och välj emulatorn i listan med program](./media/local-emulator/database-local-emulator-start.png)

När emulatorn körs visas en ikon i aktivitetsfältets meddelandefält i Windows. ![Meddelande i aktivitetsfältet för den lokala Azure Cosmos DB-emulatorn](./media/local-emulator/database-local-emulator-taskbar.png)

Azure Cosmos-DB-emulatorn körs som standard på den lokala datorn (”localhost”) som lyssnar på port 8081.

Azure Cosmos DB-emulatorn är installerad som standard i `C:\Program Files\Azure Cosmos DB Emulator`. Du kan också starta och stoppa emulatorn från kommandoraden. För mer information, se [kommandoradsverktygsreferensen](#command-line).

## <a name="start-data-explorer"></a>Starta Datautforskaren

När Azure Cosmos DB-emulatorn startar öppnas automatiskt Azure Cosmos DB-datautforskaren i webbläsaren. Adressen visas som [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Om du stänger Utforskaren och vill öppna den senare kan du antingen öppna webbadressen i webbläsaren eller starta den från Azure Cosmos-DB-emulatorn i Windows-ikonen i systemfältet som visas nedan.

![Startprogrammet för den lokala Azure Cosmos DB-emulatorn i Datautforskaren](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Leta efter uppdateringar
Datautforskaren anger om det finns en ny uppdatering som går att ladda ned. 

> [!NOTE]
> Det är inte garanterat att data som skapats i en version av Azure Cosmos DB-emulatorn går att komma åt när en annan version används. Om du behöver spara dina data på lång sikt rekommenderar vi att du lagrar dessa data på ett Azure Cosmos DB-konto istället för i Azure Cosmos DB-emulatorn. 

## <a name="authenticating-requests"></a>Autentisera förfrågningar
Precis som när du använder Azure Cosmos DB i molnet måste varje begäran du gör i Azure Cosmos DB-emulatorn autentiseras. Azure Cosmos DB-emulatorn stöder endast ett enda fast konto och en välkänd autentiseringsnyckel för autentisering med huvudnyckel. Kontot och nyckeln är de enda autentiseringsuppgifter som tillåts för användning med Azure Cosmos DB-emulatorn. De är:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Huvudnyckeln som stöds av Azure Cosmos DB-emulatorn är endast avsedd att användas med emulatorn. Du kan inte använda ditt Azure Cosmos DB-produktionskonto och -nyckel med Azure Cosmos DB-emulatorn. 

> [!NOTE] 
> Om du har startat emulatorn med /-tangentalternativet använder du den genererade nyckeln istället för ”C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==”

Precis som Azure Cosmos DB-tjänsten stöder Azure Cosmos DB-emulator endast säker kommunikation via SSL.

## <a name="running-on-a-local-network"></a>Köra på ett lokalt nätverk

Du kan köra emulatorn på ett lokalt nätverk. För att aktivera nätverksåtkomst anger du alternativet /AllowNetworkAccess på [kommandoraden](#command-line-syntax), vilket också kräver att du anger /Key=key_string eller /KeyFile=file_name. Du kan generera en fil med en slumpmässig nyckel med /GenKeyFile=file_name.  Du kan sedan skicka den till /KeyFile=file_name eller /Key=contents_of_file.

För att aktivera nätverksåtkomst för första gången ska användaren stänga emulatorn och ta bort emulatorns datakatalog (C:\Users\user_name\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Utveckla med emulatorn
När du kör Azure Cosmos DB-emulatorn på datorn kan du använda valfri [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) som stöds eller [REST API för Azure Cosmos DB](/rest/api/cosmos-db/) till att interagera med emulatorn. Azure Cosmos DB-emulatorn innehåller även en inbyggda Datautforskare som du kan använda till att skapa samlingar för API:er för SQL och MongoDB, och visa och redigera dokument utan att skriva någon kod.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Om du använder [Azure Cosmos DB-protokollstöd för MongoDB](mongodb-introduction.md) använder du följande anslutningssträng:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Du kan använda befintliga verktyg som [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) för att ansluta till Azure Cosmos DB-emulatorn. Du kan också migrera data mellan Azure Cosmos DB-emulatorn och Azure Cosmos DB-tjänsten med [datamigreringsverktyget i Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE] 
> Om du har startat emulatorn med /-tangentalternativet använder du den genererade nyckeln istället för ”C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==”

Med Azure Cosmos DB-emulatorn kan du som standard skapa upp till 25 samlingar med en enda partition eller 1 partitionerad samling. Mer information om att ändra värdet finns i [Setting the PartitionCount value](#set-partitioncount) (Ange PartitionCount-värdet).

## <a name="export-the-ssl-certificate"></a>Exportera SSL-certifikatet

.NET-språk och -körning använder Windows Certificate Store för att säkert ansluta till den lokala Azure Cosmos DB-emulatorn. Andra språk har sin egen metod för att hantera och använda certifikat. Java använder sitt eget [certifikatarkiv](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) medan Python använder [socketomslutningar](https://docs.python.org/2/library/ssl.html).

För att hämta ett certifikat som ska användas med språk och körningar som inte integrerar med Windows Certificate Store måste du exportera det med Windows Certificate Manager. Du kan starta det genom att köra certlm.msc eller följa de stegvisa instruktionerna i [Exportera Azure Cosmos DB emulatorcertifikat](./local-emulator-export-ssl-certificates.md). När certifikathanteraren körs öppnar du personliga certifikat som du ser nedan och exporterar certifikatet med det egna namnet ”DocumentDBEmulatorCertificate” som en BASE-64-kodad X.509-fil (.cer).

![SSL-certifikat för den lokala Azure DB Cosmos DB-emulatorn](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Följ instruktionerna i avsnittet [om att lägga till ett certifikat i Java CA-certifikatarkiv](https://docs.microsoft.com/azure/java-add-certificate-ca-store) för att importera X.509-certifikatet till standardcertifikatarkivet för Java. När certifikatet har importerats till certifikatarkivet kan Java- och MongoDB-program ansluta till Azure Cosmos DB-emulatorn.

SSL-verifieringen inaktiveras när du ansluter till emulatorn från Python- och Node.js-SDK:er.

## <a id="command-line"></a>Kommandoradsverktygsreferens
Du kan använda kommandoraden från installationsplatsen för att starta och stoppa emulatorn, konfigurera alternativ och utföra andra åtgärder.

### <a name="command-line-syntax"></a>Syntax för kommandorad

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Om du vill visa en lista över alternativ skriver du `CosmosDB.Emulator.exe /?` i kommandotolken.

<table>
<tr>
  <td><strong>Alternativ</strong></td>
  <td><strong>Beskrivning</strong></td>
  <td><strong>Kommando</strong></td>
  <td><strong>Argument</strong></td>
</tr>
<tr>
  <td>[Inga argument]</td>
  <td>Startar Azure Cosmos DB-emulatorn med standardinställningar.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Hjälp]</td>
  <td>Visar en lista över kommandoradsargument som stöds.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>GetStatus</td>
  <td>Laddar ned status för Azure Cosmos DB-emulatorn. Statusen visas med slutkoden: 1 = Startar, 2 = Körs, 3 = Stoppad. En negativ slutkod anger att ett fel har uppstått. Inga andra utdata produceras.</td>
  <td>CosmosDB.Emulator.exe /GetStatus</td>
  <td></td>
<tr>
  <td>Avstängning</td>
  <td>Stänger Azure Cosmos DB-emulatorn.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Anger den sökväg där du kan lagra filer. Standardinställningen är %LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: En åtkomlig sökväg</td>
</tr>
<tr>
  <td>Port</td>
  <td>Anger det portnummer som ska användas för emulatorn.  Standardvärdet är 8081.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: Enskilt portnummer</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Anger det portnummer som ska användas för API för MongoDB-kompatibilitet. Standardvärdet är 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: Enskilt portnumber</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Anger portarna som ska användas för direktanslutning. Standardvärdena är 10251, 10252, 10253, 10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: Kommaavgränsad lista över 4 portar</td>
</tr>
<tr>
  <td>Nyckel</td>
  <td>Auktoriseringsnyckel för emulatorn. Nyckeln måste vara en base-64-kodning av en 64 bytes vektor.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;: Nyckeln måste vara en base-64-kodning av en 64 bytes vektor</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Anger att begränsande beteende för förfrågningsfrekvens är aktiverat.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Anger att begränsande beteende för förfrågningsfrekvens är inaktiverat.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Visa inte emulatorns användargränssnitt.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Visa inte datautforskaren vid start.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Anger det högsta antalet partitionerade samlingar. Läs [Ändra antalet samlingar](#set-partitioncount) för mer information.</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: Det högsta antalet tillåtna enskilda partitionssamlingar. Standardvärdet är 25. Maxvärdet är 250 GB.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>Anger standardantalet för partitioner för en partitionerad samling.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; Standardvärdet är 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Ger åtkomst till emulatorn över ett nätverk. Du måste även skicka /Key=&lt;key_string&gt; eller /KeyFile=&lt;file_name&gt; för att aktivera nätverksåtkomst.</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;key_string&gt;<br><br>eller<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>Justera inga brandväggsregler när /AllowNetworkAccess används.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Generera en ny auktoriseringsnyckel och spara den i den angivna filen. Den genererade nyckeln kan användas med alternativen /Key eller /KeyFile.</td>
  <td>CosmosDB.Emulator.exe  /GenKeyFile=&lt;sökväg till nyckelfilen&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Konsekvens</td>
  <td>Ställ in konsekvensnivå för kontot.</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;consistency&gt;</td>
  <td>&lt;consistency&gt;: Värdet måste ha någon av följande [konsekvensnivåer](consistency-levels.md): session, stark, eventuell eller BoundedStaleness.  Standardvärdet är Session.</td>
</tr>
<tr>
  <td>?</td>
  <td>Visa hjälpmeddelandet.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>Ändra antalet samlingar

Med Azure Cosmos DB-emulatorn kan du som standard skapa upp till 25 samlingar med en enda partition eller 1 partitionerad samling. Genom att ändra **PartitionCount**-värdet kan du skapa upp till 250 samlingar med en enskild partition eller 10 partitionerade samlingar, eller en kombination av de två alternativen som inte överskrider 250 enskilda partitioner (där en partitionerad samling = 25 samlingar med en enskild partition).

Om du försöker skapa en samling när det aktuella partitionsantalet har överskridits utlöser emulatorn ett ServiceUnavailable-undantag med följande meddelande.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email askcosmosdb@microsoft.com at any time or
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Om du vill ändra antalet tillgängliga samlingar för Azure Cosmos DB-emulatorn gör du följande:

1. Ta bort alla lokala Azure Cosmos DB-emulatordata genom att högerklicka på ikonen för **Azure Cosmos DB-emulatorn** i meddelandefältet och sedan klicka på **Återställ data...**
2. Ta bort alla emulatordata i den här mappen C:\Users\user_name\AppData\Local\CosmosDBEmulator.
3. Avsluta alla öppna instanser genom att högerklicka på ikonen för **Azure Cosmos DB-emulator** i meddelandefältet och klicka sedan på **Avsluta**. Det kan ta någon minut för alla instanser att avslutas.
4. Installera den senaste versionen av [Azure Cosmos DB-emulatorn](https://aka.ms/cosmosdb-emulator).
5. Starta emulatorn med PartitionCount-flaggan genom att ställa in ett värde <= 250. Till exempel: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Kontrollera emulatorn

Emulatorn innehåller en PowerShell-modul som kan användas till att starta, stoppa, avinstallera och hämta status för tjänsten. Så här använder du den:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

eller placera `PSModules`-katalogen på din `PSModulesPath` och importerar den så här:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Här följer en sammanfattning av kommandon för att styra emulatorn från PowerShell:

### `Get-CosmosDbEmulatorStatus`

#### <a name="syntax"></a>Syntax

`Get-CosmosDbEmulatorStatus`

#### <a name="remarks"></a>Kommentarer

Returnerar någon av dessa ServiceControllerStatus-värden: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running eller ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>Syntax

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>]  [<CommonParameters>]`

#### <a name="remarks"></a>Kommentarer

Startar emulatorn. Som standard väntar kommandot till emulatorn är redo att ta emot begäranden. Använd alternativet -NoWait om du vill att cmdleten ska returneras så fort den startar emulatorn.

### `Stop-CosmosDbEmulator`

#### <a name="syntax"></a>Syntax

 `Stop-CosmosDbEmulator [-NoWait]`

#### <a name="remarks"></a>Kommentarer

Stoppar emulatorn. Som standard väntar kommandot tills emulatorn är helt avstängd. Använd alternativet -NoWait om du vill att cmdleten ska returneras så fort emulatorn börjar stängas av.

### `Uninstall-CosmosDbEmulator`

#### <a name="syntax"></a>Syntax

`Uninstall-CosmosDbEmulator [-RemoveData]`

#### <a name="remarks"></a>Kommentarer

Avinstallerar emulatorn och tar eventuellt bort allt innehåll i $env:LOCALAPPDATA\CosmosDbEmulator.
Cmdleten garanterar att emulatorn stoppas innan den avinstalleras.

## <a name="running-on-docker"></a>Köra på Docker

Azure Cosmos DB-emulatorn kan köras på Docker för Windows. Emulatorn fungerar inte i Docker för Oracle Linux.

När du har installerat [Docker för Windows](https://www.docker.com/docker-windows) växlar du till Windows-containrar genom att högerklicka på Docker-ikonen i verktygsfältet och välja **Växla till Windows-containrar**.

Hämta därefter emulatoravbildningen från Docker-hubben genom att köra följande kommando i ditt favoritgränssnitt.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
Starta avbildningen genom att köra följande kommandon.

Från kommandoraden:
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

Från PowerShell:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

Svaret ser ut ungefär så här:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Använd nu slutpunkten och huvudnyckeln från svaret i din klient och importera SSL-certifikatet till din värd. För att importera SSL-certifikatet gör du följande från en kommandotolk för administratör:

Från kommandoraden:
```cmd 
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

Från PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

Om du stänger det interaktiva gränssnittet när emulatorn har startats stängs emulatorns container.

Öppna Datautforskaren genom att gå till följande URL i webbläsaren. Emulatorns slutpunkt finns i svarsmeddelandet ovan.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Felsökning

Ta hjälp av följande tips när du ska felsöka problem du stöter på med Azure Cosmos DB-emulatorn:

- Om du har installerat en ny version av emulatorn och fel uppstår ska du återställa dina data. Du kan återställa dina data genom att högerklicka på ikonen för Azure Cosmos DB-emulator i meddelandefältet och sedan klicka på Återställ data... Om det inte åtgärdar felen kan du avinstallera och installera om appen. I [Avinstallera den lokala emulatorn](#uninstall) finns instruktioner.

- Om Azure Cosmos DB-emulatorn kraschar ska du samla in kraschdumpfiler från mappen c:\Users\<användarnamn>\AppData\Local\CrashDumps, komprimera dem och bifoga dem i ett e-postmeddelande till [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Om det uppstår krascher i CosmosDB.StartupEntryPoint.exe kör du följande kommando från en kommandotolk för administratör: `lodctr /R` 

- Om du stöter på ett anslutningsfel ska du [samla in spårningsfiler](#trace-files), komprimera dem och bifoga dem i ett e-postmeddelande till [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Om du ser ett meddelande om att **tjänsten inte är tillgänglig** kanske emulatorn misslyckas med att initiera nätverksstacken. Se efter om du har Pulse Secure-klienten eller Juniper-nätverksklienten installerad, eftersom deras nätverksfilterdrivrutiner kan orsaka problemet. Avinstallation av nätverksfilterdrivrutiner från tredje part åtgärdar vanligen problemet.

- Om datorn försätts i viloläge eller om operativsystemet uppdateras när emulatorn körs kan du se meddelandet **Tjänsten är inte tillgänglig just nu**. Återställ emulatorn genom att högerklicka på ikonen som visas i meddelandefältet i Windows och välja **Återställ data**.

### <a id="trace-files"></a>Samla in spårningsfiler

För att samla in felsökningsspårningar kör du följande kommandon från en administrativ kommandotolk:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Titta på systemfältet att kontrollera att programmet har avslutats. Det kan ta ett tag. Du kan också klicka på **Avsluta** i Azure Cosmos DB-emulatorns användargränssnitt.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Återskapa problemet. Om Datautforskaren inte fungerar behöver du vara vänta tills webbläsaren öppnas i några sekunder för att upptäcka felet.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Gå till `%ProgramFiles%\Azure Cosmos DB Emulator` och leta rätt på filen docdbemulator_000001.etl.
7. Skicka .etl-filen tillsammans med reproduktionssteg till [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) för felsökning.

### <a id="uninstall"></a>Avinstallera den lokala emulatorn

1. Avsluta alla öppna instanser av den lokala emulatorn genom att högerklicka på ikonen för Azure Cosmos DB-emulatorn i meddelandefältet och sedan klicka på Avsluta. Det kan ta någon minut för alla instanser att avslutas.
2. I Windows-sökrutan skriver du **Appar och funktioner** och klickar på resultatet för **Appar och funktioner (systeminställningar)**.
3. I listan över appar bläddrar du till **Azure Cosmos DB-emulatorn**, väljer den, klickar på **Avinstallera** och bekräftar sedan och klickar på **Avinstallera** igen.
4. När appen är avinstallerad navigerar du till `C:\Users\<user>\AppData\Local\CosmosDBEmulator` och tar bort mappen. 

## <a name="change-list"></a>Ändringslista

Du kan kontrollera versionsnummer genom att högerklicka på ikonen för den lokala emulatorn i aktivitetsfältet och klicka på Om-menyalternativet.

### <a name="1220-released-on-april-20-2018"></a>1.22.0. Gavs ut den 20 april 2018

Förutom att uppdatera emulatortjänsterna för paritet med Cosmos DB-molntjänsterna så har vi lagt till bättre PowerShell-dokumentation och några olika felkorrigeringar.

### <a name="12106-released-on-march-27-2018"></a>1.21.0.6 Gavs ut den 27 mars 2018

Förutom att uppdatera emulatortjänsterna för paritet med Cosmos DB-molntjänsterna så har vi lagt till en ny funktion och två felkorrigeringar i den här versionen.

#### <a name="features"></a>Funktioner

1. Kommandot Start-CosmosDbEmulator innehåller nu startalternativ.

#### <a name="bug-fixes"></a>Felkorrigeringar

1. PowerShell-modulen Microsoft.Azure.CosmosDB.Emulator ser nu till att `ServiceControllerStatus`-uppräkningen läses in.

2. PowerShell-modulen Microsoft.Azure.CosmosDB.Emulator innehåller nu ett manifest som utelämnades i den första versionen.

### <a name="1201084-released-on-february-14-2018"></a>1.20.108.4 Släpptes den 14 februari 2018

Det finns en ny funktion och två felkorrigeringar i den här versionen. Vi tackar kunderna som har hjälpt oss att hitta och åtgärda problemen.

#### <a name="bug-fixes"></a>Felkorrigeringar

1. Emulatorn fungerar nu på datorer med 1 eller 2 kärnor (eller virtuella processorer)

   Cosmos DB allokerar uppgifter för att utföra olika tjänster. Antalet allokerade uppgifter är en multipel av antalet kärnor på en värd. Standardmultipeln fungerar bra i produktionsmiljöer där antalet kärnor är stort. Men på datorer med 1 eller 2 processorer allokeras inga uppgifter för att utföra dessa tjänster när den här multipeln används.

   Vi åtgärdade detta genom att lägga till en konfigurationsåsidosättning för emulatorn. Vi kan nu använda en multipel på 1. Antalet uppgifter som allokeras för att utföra diverse tjänster motsvarar nu antalet kärnor hos en värd.

   Det här problemet prioriterades främst för den här versionen. Vi ser att många utvecklings-/testmiljöer med emulatorn har 1 eller 2 kärnor.

2. Emulatorn kräver inte längre att Microsoft Visual C++ 2015 Redistributable ska vara installerat.

   Vi upptäckte att nya installationer av Windows (skrivbords- och serverversioner) inte innehåller det här distributionspaketet. Därför paketerar vi nu de omdistribuerbara binärfilerna med emulatorn.

#### <a name="features"></a>Funktioner

Många av de kunder vi har talat med har sagt att det vore bra om det gick att skriva skript för emulatorn. Vi har därför lagt till vissa skriptmöjligheter i den här versionen. Emulatorn innehåller nu en PowerShell-modul för att starta, stoppa, hämta status och avinstallera sig själv: `Microsoft.Azure.CosmosDB.Emulator`. 

### <a name="120911-released-on-january-26-2018"></a>1.20.91.1 Gavs ut den 26 januari 2018

* Aktiverade MongoDB-sammansättningspipeline som standard.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Installerade den lokala emulatorn
> * Körde emulatorn på Docker för Windows
> * Autentiserade begäranden
> * Använde datautforskaren i emulatorn
> * Exporterade SSL-certifikat
> * Anropade emulatorn från kommandoraden
> * Samlade in spårningsfiler

I den här självstudien har du lärt dig att använda den lokala emulatorn för kostnadsfri lokal utveckling. Du kan nu fortsätta till nästa självstudie och lära dig att exportera SSL-certifikat för emulatorn. 

> [!div class="nextstepaction"]
> [Exportera Azure Cosmos DB emulatorcertifikat](local-emulator-export-ssl-certificates.md)
