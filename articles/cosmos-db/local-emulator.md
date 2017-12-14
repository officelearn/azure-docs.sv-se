---
title: Utveckla lokalt med Azure Cosmos DB-emulatorn | Microsoft Docs
description: "Med Azure Cosmos DB-emulatorn kan du utveckla och testa programmet lokalt för gratis, utan att skapa en Azure-prenumeration."
services: cosmos-db
documentationcenter: 
keywords: Azure Cosmos DB-emulatorn
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2017
ms.author: arramac
ms.openlocfilehash: 69736670068479ce90cc346a163fe27b340cdb0a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Använd Azure Cosmos DB-emulatorn för lokal utveckling och testning

<table>
<tr>
  <td><strong>Binärfiler</strong></td>
  <td>[Ladda ned MSI](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker-hubb](https://hub.docker.com/r/microsoft/azure-documentdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Docker-källa</strong></td>
  <td>[Github](https://github.com/azure/azure-documentdb-emulator-docker)</td>
</tr>
</table>
  
Azure-emulatorn Cosmos DB tillhandahåller en lokal miljö som emulerar Azure DB som Cosmos-tjänsten för utveckling. Med Azure Cosmos DB-emulatorn kan du utveckla och testa programmet lokalt, utan att skapa en Azure-prenumeration eller kostnader. När du är nöjd med hur programmet fungerar i Azure Cosmos DB-emulatorn kan växla du till med ett Azure DB som Cosmos-konto i molnet.

Den här artikeln omfattar följande aktiviteter: 

> [!div class="checklist"]
> * Installerar emulatorn
> * Autentisering av förfrågningar
> * Med hjälp av Data Explorer i emulatorn
> * Exportera SSL-certifikat
> * Anropar emulatorn från kommandoraden
> * Kör emulatorn på Docker för Windows
> * Samla in spårningsfiler
> * Felsökning

Vi rekommenderar att komma igång med att titta på nedanstående video, där Kirill Gavrylyuk visar hur du kommer igång med Azure Cosmos DB-emulatorn. Observera att videon refererar till emulatorn som DocumentDB-emulatorn, men själva verktyget har bytt namn Azure Cosmos DB-emulatorn sedan in videon. All information i videon är korrekt för Azure Cosmos DB-emulatorn. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>Så här fungerar emulatorn
Azure-emulatorn Cosmos DB ger en hög återgivning emulering av tjänsten Azure Cosmos DB. Den stöder identiska funktioner som Azure Cosmos DB, inklusive stöd för att skapa och hämtning av JSON-dokument, etablering och skalning samlingar och köra lagrade procedurer och utlösare. Du kan utveckla och testa program med hjälp av Azure Cosmos DB emulatorn och distribuera dem till Azure på global nivå genom att bara göra en enda konfigurationen av anslutningens slutpunkt för Azure Cosmos DB.

Medan vi har skapat en lokal emulering hög återgivning av tjänsten faktiska Azure Cosmos DB är implementeringen av Azure Cosmos DB-emulatorn än tjänsten. Till exempel använder Azure Cosmos DB-emulatorn standard OS-komponenter, till exempel det lokala filsystemet för beständighet och HTTPS-protokoll-stacken för anslutning. Detta innebär att vissa funktioner som förlitar sig på Azure-infrastrukturen som globala replikering, en siffra millisekunds fördröjning för läsning/skrivning och justerbara konsekvensnivåer inte är tillgängliga via Azure Cosmos DB-emulatorn.

> [!NOTE]
> Data Explorer i emulatorn stöder endast skapandet av SQL API samlingar och MongoDB samlingar just nu. Data Explorer i emulatorn stöder för närvarande inte att skapa tabeller och diagram. 

## <a name="differences-between-the-emulator-and-the-service"></a>Skillnader mellan emulatorn och tjänsten 
Eftersom Azure Cosmos DB-emulatorn är en emulerade miljö som körs på en lokal developer-arbetsstation, finns det vissa skillnader i funktionalitet mellan emulatorn och ett Azure DB som Cosmos-konto i molnet:

* Azure-emulatorn Cosmos DB stöder bara ett fast konto och en välkänd huvudnyckel.  Det går inte att nyckeln återskapas i Azure Cosmos DB-emulatorn.
* Azure Cosmos DB-emulatorn är inte en skalbar tjänst och stöder inte ett stort antal samlingar.
* Azure Cosmos DB-emulatorn inte simulera olika [Azure Cosmos DB konsekvensnivåer](consistency-levels.md).
* Azure Cosmos DB-emulatorn inte simulera [flera regioner replikering](distribute-data-globally.md).
* Azure Cosmos DB-emulatorn har inte stöd för tjänsten kvoten åsidosättningar som är tillgängliga i Azure DB som Cosmos-tjänsten (t.ex. dokument storleksgränser, ökad partitionerad samling lagring).
* Som ditt exemplar av Azure Cosmos DB-emulatorn är inte eventuellt uppdaterad med de senaste ändringarna med tjänsten Azure Cosmos DB, ta [Azure Cosmos DB kapacitetsplaneringsverktyg](https://www.documentdb.com/capacityplanner) för att beräkna produktionsbehov genomströmning (RUs) för din programmet.

## <a name="system-requirements"></a>Systemkrav
Azure Cosmos DB-emulatorn har följande krav för maskinvara och programvara:

* Programvarukrav
  * Windows Server 2012 R2, Windows Server 2016 eller Windows 10
*   Minsta maskinvarukrav
  * 2 GB RAM-MINNE
  * 10 GB ledigt hårddiskutrymme

## <a name="installation"></a>Installation
Du kan hämta och installera Azure Cosmos-DB-emulatorn från den [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) eller så kan du köra emulatorn på Docker för Windows. Instruktioner om hur du använder emulatorn på Docker för Windows finns i [körs på Docker](#running-on-docker). 

> [!NOTE]
> Om du vill installera, konfigurera och köra Azure Cosmos DB-emulatorn, måste du ha administratörsbehörighet på datorn.

## <a name="running-on-windows"></a>Körs på Windows

Klicka på knappen Start eller tryck på Windows-tangenten för att starta Azure Cosmos DB-emulatorn. Börja skriva **Azure Cosmos DB emulatorn**, och välj emulatorn från listan med program. 

![Klicka på knappen Start eller tryck på Windows-tangenten, börja skriva ** Azure Cosmos DB emulatorn ** och välj emulator från listan med program](./media/local-emulator/database-local-emulator-start.png)

När emulatorn körs visas en ikon i meddelandefältet i Aktivitetsfältet. ![Azure DB Cosmos lokala emulatorn Aktivitetsfältsmeddelande](./media/local-emulator/database-local-emulator-taskbar.png)

Azure Cosmos-DB-emulatorn som standard körs på den lokala datorn (”localhost”) som lyssnar på port 8081.

Azure-emulatorn Cosmos DB installeras som standard till den `C:\Program Files\Azure Cosmos DB Emulator` directory. Du kan också starta och stoppa emulator från kommandoraden. Se [kommandoradsverktyget referens](#command-line) för mer information.

## <a name="start-data-explorer"></a>Starta Data Explorer

När Azure DB som Cosmos-emulatorn startar visas automatiskt Azure Cosmos DB Data Explorer i webbläsaren. Adressen visas som [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Om du stänger Utforskaren och vill öppna den igen senare, kan du öppna URL: en i webbläsaren eller starta från Azure Cosmos-DB-emulatorn i Windows-ikon som visas nedan.

![Azure DB Cosmos lokala emulatorn data explorer programstart](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Söker efter uppdateringar
Data Explorer anger om det finns en ny uppdatering tillgänglig för hämtning. 

> [!NOTE]
> Data som har skapats i en version av Azure Cosmos DB-emulatorn är inte säkert att vara tillgänglig när du använder en annan version. Om du behöver spara dina data på lång sikt rekommenderas att du lagrar data i ett Azure DB som Cosmos-konto i stället för Azure Cosmos DB-emulatorn. 

## <a name="authenticating-requests"></a>Autentisering av förfrågningar
Precis som med Azure Cosmos-DB i molnet, måste varje begäran som du gör mot Azure Cosmos DB-emulatorn autentiseras. Azure-emulatorn Cosmos DB stöder ett fast konto och en välkänd autentiseringsnyckel för huvudnyckeln för autentisering. Kontot och nyckeln är de bara autentiseringsuppgifter som tillåts för användning med Azure Cosmos DB-emulatorn. De är:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Huvudnyckeln stöds av Azure Cosmos DB-emulatorn är avsedd att användas endast med emulatorn. Du kan inte använda din produktion Azure Cosmos DB konto och nyckel med Azure Cosmos DB-emulatorn. 

> [!NOTE] 
> Om du har startat emulatorn med alternativet/Key kan sedan använda den genererade nyckeln i stället för ”C2y6yDjf5/R + ob0N8A7Cgv30VRDJIWEHLM + 4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw ==”

Dessutom precis som tjänsten Azure Cosmos DB Azure Cosmos DB-emulatorn stöder endast säker kommunikation via SSL.

## <a name="running-on-a-local-network"></a>Körs på ett lokalt nätverk

Du kan köra emulatorn i ett lokalt nätverk. Om du vill aktivera nätverksåtkomst, anger du alternativet /AllowNetworkAccess på den [kommandoraden](#command-line-syntax), vilket kräver att du anger/Key = key_string eller/KeyFile = filnamn. Du kan använda /GenKeyFile = filnamn för att generera en fil med en slumpmässig nyckel förskott.  Sedan kan du skicka att att/KeyFile = filnamn eller/Key = contents_of_file.

För att aktivera nätverksåtkomst för första gången användaren stänga emulatorn och ta bort den emulator datakatalog (C:\Users\user_name\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Utveckla med emulatorn
När du har Azure Cosmos DB emulatorn körs på datorn kan du använda någon stöds [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) eller [Azure Cosmos DB REST API](/rest/api/documentdb/) att interagera med emulatorn. Azure-emulatorn Cosmos DB innehåller också en inbyggd Data Explorer där du kan skapa samlingar för SQL och MongoDB APIs och visa och redigera dokument utan att skriva någon kod.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Om du använder [Azure Cosmos DB Protokollstöd för MongoDB](mongodb-introduction.md), Använd följande anslutningssträng:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Du kan använda befintliga verktyg [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) att ansluta till Azure Cosmos DB-emulatorn. Du kan också migrera data mellan Azure Cosmos DB-emulatorn och tjänsten Azure Cosmos DB använder den [Azure Cosmos DB Datamigreringsverktyg](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE] 
> Om du har startat emulatorn med alternativet/Key kan sedan använda den genererade nyckeln i stället för ”C2y6yDjf5/R + ob0N8A7Cgv30VRDJIWEHLM + 4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw ==”

Med hjälp av Azure DB som Cosmos-emulatorn som standard, kan du skapa upp till 25 enskilda partitionssamlingar eller 1 partitionerad samling. Mer information om hur du ändrar det här värdet finns [PartitionCount värdet](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Exportera SSL-certifikat

.NET-språk och körning kan du använda Windows certifikatarkiv på ett säkert sätt ansluta till den lokala Azure DB som Cosmos-emulatorn. Andra språk har sin egen metod för att hantera och använda certifikat. Java använder sin egen [certifikatarkiv](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) medan Python använder [socket omslutningar](https://docs.python.org/2/library/ssl.html).

För att få ett certifikat som ska användas med språk och körningar som inte integreras med Windows certifikatarkiv behöver du exportera den med hjälp av Windows Certificate Manager. Du kan starta den genom att köra certlm.msc eller Följ steg-för-steg-instruktioner i [exportera Azure Cosmos DB emulatorn certifikat](./local-emulator-export-ssl-certificates.md). När Certifikathanteraren körs kan öppna personliga certifikat som visas nedan och exportera certifikatet med namnet ”DocumentDBEmulatorCertificate” som en Base64-kodad X.509 (.cer)-fil.

![Azure DB Cosmos lokala emulatorn SSL-certifikat](./media/local-emulator/database-local-emulator-ssl_certificate.png)

X.509-certifikat kan importeras till certifikatarkivet Java genom att följa instruktionerna i [att lägga till ett certifikat i Java Certifikatutfärdarens certifikatarkivet](https://docs.microsoft.com/azure/java-add-certificate-ca-store). När du har importerat certifikatet till certifikatarkivet kommer Java och MongoDB program att kunna ansluta till Azure Cosmos DB-emulatorn.

När du ansluter till emulatorn från Python och Node.js SDK, är SSL-kontroll inaktiverad.

## <a id="command-line"></a>Kommandoradsverktyget referens
Installationsplatsen, kan du använda kommandoraden att starta och stoppa emulatorn, konfigurera alternativ och utföra andra åtgärder.

### <a name="command-line-syntax"></a>Kommandoradssyntaxen

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Om du vill visa listan över alternativ skriver `CosmosDB.Emulator.exe /?` i Kommandotolken.

<table>
<tr>
  <td><strong>Alternativet</strong></td>
  <td><strong>Beskrivning</strong></td>
  <td><strong>Kommandot</strong></td>
  <td><strong>Argument</strong></td>
</tr>
<tr>
  <td>[Inga argument]</td>
  <td>Startar Azure Cosmos-DB-emulatorn med standardinställningar.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Hjälp]</td>
  <td>Visar lista med kommandoradsargument som stöds.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Avstängning</td>
  <td>Stänger ned Azure Cosmos DB-emulatorn.</td>
  <td>CosmosDB.Emulator.exe Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Anger den sökväg där du kan lagra filer. Standardvärdet är % LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath =&lt;datapath&gt;</td>
  <td>&lt;DataPath&gt;: en tillgänglig sökväg</td>
</tr>
<tr>
  <td>Port</td>
  <td>Anger det portnummer som ska användas för emulatorn.  Standardvärdet är 8081.</td>
  <td>CosmosDB.Emulator.exe/port =&lt;port&gt;</td>
  <td>&lt;port&gt;: enskilda portnummer</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Anger det portnummer som ska användas för MongoDB kompatibilitet API. Standardvärdet är 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort =&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: enskilda portnummer</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Anger portarna som ska användas för direkt anslutning. Standardvärdena är 10251,10252,10253,10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: kommaavgränsad lista över 4 portar</td>
</tr>
<tr>
  <td>Nyckel</td>
  <td>Auktoriseringsnyckeln för emulatorn. Nyckeln måste vara Base64-kodning av en vector 64 byte.</td>
  <td>CosmosDB.Emulator.exe/Key:&lt;nyckel&gt;</td>
  <td>&lt;nyckeln&gt;: nyckeln måste vara Base64-kodning av en 64-byte-vektor</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Anger räntesatsen begäran att begränsa beteendet är aktiverad.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Anger räntesatsen begäran att begränsa beteendet är inaktiverad.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Visa inte emulatorn-användargränssnittet.</td>
  <td>CosmosDB.Emulator.exe/noui</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Visa inte dokumentutforskaren vid start.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Anger det maximala antalet partitionerade samlingar. Se [ändra antalet samlingar](#set-partitioncount) för mer information.</td>
  <td>CosmosDB.Emulator.exe /PartitionCount =&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: maximalt antal tillåtna enskilda partitionssamlingar. Standardvärdet är 25. Högsta tillåtna är 250.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>Anger antalet partitioner för en partitionerad samling standard.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount =&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; standardvärdet är 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Aktiverar åtkomst till emulatorn över ett nätverk. Du måste också ange/Key =&lt;key_string&gt; eller/KeyFile =&lt;file_name&gt; att aktivera nätverksåtkomst.</td>
  <td>CosmosDB.Emulator.exe AllowNetworkAccess /Key =&lt;key_string&gt;<br><br>eller<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess/KeyFile =&lt;filnamn&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>Justera inte brandväggsregler när /AllowNetworkAccess används.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Skapa en ny auktoriserings-nyckel och spara till den angivna filen. Genererad nyckel kan användas med alternativen/Key eller/KeyFile.</td>
  <td>CosmosDB.Emulator.exe /GenKeyFile =&lt;sökvägen till nyckelfilen&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Konsekvens</td>
  <td>Ange standardnivån för konsekvens för kontot.</td>
  <td>CosmosDB.Emulator.exe /Consistency =&lt;konsekvenskontroll&gt;</td>
  <td>&lt;konsekvenskontroll&gt;: värdet måste vara något av följande [konsekvensnivåer](consistency-levels.md): Session starka, Eventual eller BoundedStaleness.  Standardvärdet är Session.</td>
</tr>
<tr>
  <td>?</td>
  <td>Visa hjälpmeddelande.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>Ändra antalet samlingar

Du kan skapa upp till 25 enskilda partitionssamlingar eller 1 partitionerad samling med hjälp av Azure Cosmos DB emulatorn som standard. Genom att ändra den **PartitionCount** värde, som du kan skapa upp till 250 enskilda partitionssamlingar eller 10 partitionerade samlingar eller en kombination av båda som inte överstiger 250 enstaka partitioner (där 1 partitionerad samling = 25 enskild partition samling).

Om du försöker skapa en samling när det aktuella antalet partitioner har överskridits utlöser emulatorn ett undantag för ServiceUnavailable, med följande meddelande.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Om du vill ändra antalet samlingar som är tillgängliga att Azure Cosmos DB-emulatorn gör du följande:

1. Ta bort alla lokala data i Azure Cosmos DB-emulatorn genom att högerklicka på den **Azure Cosmos DB emulatorn** ikon i meddelandefältet och sedan klicka **återställa Data...** .
2. Ta bort alla emulatorn data i den här mappen C:\Users\user_name\AppData\Local\CosmosDBEmulator.
3. Avsluta alla öppna instanser genom att högerklicka på den **Azure Cosmos DB emulatorn** ikon i meddelandefältet och sedan klicka **avsluta**. Det kan ta ett tag för alla instanser att avsluta.
4. Installera den senaste versionen av den [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator).
5. Starta emulatorn med flaggan PartitionCount genom att ange ett värde < = 250. Till exempel: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="running-on-docker"></a>Körs på Docker

Azure Cosmos DB-emulatorn kan köras på Docker för Windows. Emulatorn fungerar inte på Docker för Oracle Linux.

När du har [Docker för Windows](https://www.docker.com/docker-windows) installerat kan växla till Windows-behållare genom att högerklicka på Docker-ikonen i verktygsfältet och välja **växla till Windows-behållare**.

Hämta emulatorn avbildningen från Docker-hubben genom att köra följande kommando från din favorit-gränssnittet.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
Kör följande kommandon för att starta avbildningen.

Från kommandoraden:
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

Från PowerShell:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

Svaret ser ut ungefär så här:

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Använd slutpunkt och huvudnyckeln i från svaret i din klient och importera SSL-certifikatet till värden. Om du vill importera SSL-certifikatet, gör du följande från en kommandotolk för administratörer:

```
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

Stänger det interaktiva gränssnittet när emulatorn har startat stängs av emulatorn behållare.

Om du vill öppna navigerar Data Explorer du till följande URL i webbläsaren. Emulatorn slutpunkten har angetts i svarsmeddelandet ovan.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Felsökning

Använd följande tips för att felsöka problem som kan uppstå med Azure DB som Cosmos-emulatorn:

- Om du har installerat en ny version av emulatorn och fel har uppstått, se till att du återställer data. Du kan återställa dina data genom att högerklicka på ikonen Azure Cosmos DB emulatorn i systemfältet och sedan klicka på Återställ Data... Om detta inte löser felen, kan du avinstallera och installera appen. Se [avinstallera lokala emulatorn](#uninstall) anvisningar.

- Om Azure DB som Cosmos-emulatorn kraschar samla in filer med felsökningsdumpar från c:\Users\user_name\AppData\Local\CrashDumps mappen komprimeras och kopplar dem till ett e-postmeddelande till [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Om det uppstår krascher i CosmosDB.StartupEntryPoint.exe, kör du följande kommando från en kommandotolk för administratörer:`lodctr /R` 

- Om du stöter på ett anslutningsproblem, [samla in spårningsfiler](#trace-files), komprimeras och koppla dem till ett e-postmeddelande till [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Om du får en **tjänsten är inte tillgänglig** meddelandet emulatorn kan inte initiera nätverksstacken. Kontrollera om du har Pulse secure klienten eller Juniper nätverk klienten är installerad, som filterdrivrutiner nätverket kan orsaka problem. Avinstallera drivrutiner från tredje part nätverket filter vanligtvis åtgärdar problemet.

### <a id="trace-files"></a>Samla in spårningsfiler

Om du vill samla in felsökning spårningar, kör du följande kommandon från en administrativ kommandotolk:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Titta på systemfältet att kontrollera att programmet har avslutats kan det ta ett tag. Du kan även klicka **avsluta** i användargränssnittet för Azure DB som Cosmos-emulatorn.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Återskapa problemet. Om Data Explorer inte fungerar, behöver du bara vänta tills webbläsaren att öppna under några sekunder att upptäcka fel.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Gå till `%ProgramFiles%\Azure Cosmos DB Emulator` och hitta docdbemulator_000001.etl-filen.
7. Etl-filen tillsammans med reproducera steg för att skicka [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) för felsökning.

### <a id="uninstall"></a>Avinstallera den lokala emulatorn

1. Avsluta alla öppna instanser av den lokala emulatorn genom att högerklicka på ikonen för Azure Cosmos DB emulatorn i systemfältet och sedan klicka på Avsluta. Det kan ta ett tag för alla instanser att avsluta.
2. Skriv i sökrutan Windows **appar och funktioner** och klicka på den **appar och funktioner (systeminställningar)** resultat.
3. Bläddra till i listan över appar **Azure Cosmos DB emulatorn**, markerar du den, klickar du på **avinstallera**, bekräfta och klickar på **avinstallera** igen.
4. När appen avinstalleras, navigera till C:\Users\<användare > \AppData\Local\CosmosDBEmulator och ta bort mappen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Installerat lokala emulatorn
> * SLUMP Emulator på Docker för Windows
> * Autentiserade begäranden
> * Används av Data Explorer i emulatorn
> * Exporterade SSL-certifikat
> * Kallas emulatorn från kommandoraden
> * Insamlade spårningsfiler

Du har lärt dig hur du använder lokala emulatorn för kostnadsfria lokal utveckling i de här självstudierna. Du kan nu gå vidare till nästa kurs och lär dig hur du exporterar emulatorn SSL-certifikat. 

> [!div class="nextstepaction"]
> [Exportera Azure Cosmos DB emulatorn certifikat](local-emulator-export-ssl-certificates.md)
