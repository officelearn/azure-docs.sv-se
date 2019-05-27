---
title: Store blockblob-objekt på enheter – Azure IoT Edge | Microsoft Docs
description: Förstå lagringsnivåer och time-to-live-funktioner, visa stöds blob storage-åtgärder och ansluta till ditt blob storage-konto.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 396af2dfd9fc53c080163a27e376328c1369d5e1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991474"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store data på gränsen med Azure Blob Storage på IoT Edge (förhandsversion)

Azure Blob Storage på IoT Edge innehåller en [blockblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) lagringslösning kant. Ett blob storage-modulen på din IoT Edge-enhet som fungerar som en tjänst för Azure block blob, men blockblob-objekt lagras lokalt på din IoT Edge-enhet. Du kan få åtkomst till dina blobar på samma sätt i Azure storage SDK eller blockera blob API-anrop som du redan är van att.

> [!NOTE]
> Azure Blob Storage på IoT Edge är i [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här modulen som levereras med **lagringsnivåer** och **time-to-live** funktioner.

> [!NOTE]
> För närvarande lagringsnivåer och time-to-live-funktioner är endast tillgängliga i Linux AMD64- och Linux ARM32.

**Lagringsnivåer** är en konfigurerbar funktioner, där du kan automatiskt ladda upp data från din lokala blob storage till Azure med stöd för tillfälliga internet-anslutning. På så sätt kan du:

- Aktivera på/av funktionen lagringsnivåer
- Välj den ordning i vilken data kopieras till Azure som NewestFirst eller OldestFirst
- Ange Azure Storage-konto som du vill att dina data som överförs.
- Ange de behållare som du vill överföra till Azure. Den här modulen kan du ange behållarnamn för både källa och mål.
- Hela nivåindelning (med hjälp av `Put Blob` åtgärden) och blockera nivå lagringsnivåer (med hjälp av `Put Block` och `Put Block List` operations).

Den här modulen använder block på lagringsnivåer, när din blob består av block. Här följer några vanliga scenarier:

- Programmet uppdateras vissa block med en tidigare överförda blob, den här modulen Överför bara uppdaterade block och inte hela blob.
- Modulen överför blob och internet-anslutning är borta, när anslutningen är tillbaka igen överförs endast de återstående block och inte hela blob.

Om en oväntat avslut (till exempel strömavbrott) inträffar under en blob-överföringen kan kommer alla block som var dags att överföringen att överföras igen, när modulen är online igen.

**Time-to-live** (TTL) är en konfigurerbar funktionalitet där modulen tar automatiskt bort dina blobar från lokal lagring när den angivna varaktigheten (mätt i minuter) upphör att gälla. TTL-värde kan du:

- Aktivera på/av funktionen lagringsnivåer
- Ange TTL-värdet i minuter

Scenarier där data som videor, bilder, ekonomi, sjukhus data eller data som behöver lagras lokalt, senare som kan bearbetas lokalt eller överförs till molnet är bra exempel att använda den här modulen.

Den här artikeln innehåller instruktioner för att distribuera en Azure Blob Storage på IoT Edge-behållaren som kör en blob-tjänst på din IoT Edge-enhet.

> [!NOTE]
> De termer som ”automatisk lagringsnivåer” och ”automatisk förfallodatum” används i videon har ersatts av ”lagringsnivåer” och ”time-to-live”.

Titta på videon och få snabb introduktion
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Förutsättningar

En Azure IoT Edge-enhet:

- Du kan använda utvecklingsdatorn eller en virtuell dator som en IoT Edge-enhet genom att följa stegen i snabbstarten för [Linux](quickstart-linux.md) eller [Windows-enheter](quickstart.md).

- Azure Blob Storage på IoT Edge-modul har stöd för följande enhetskonfigurationer:

  | Operativsystem | Arkitektur |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Core (oktober uppdatering) | AMD64 |
  | Windows 10 IoT Enterprise (oktober uppdatering) | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian stretch | ARM32 |

Molnresurser:

En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure.

## <a name="tiering-and-time-to-live-properties"></a>Egenskaper för nivåindelning och time to live

Använd önskade egenskaper för att ange lagringsnivåer och time to live. De kan ange under distributionen eller ändras senare genom att redigera modultvilling utan att behöva distribuera om. Vi rekommenderar att du kontrollerar ”Modultvilling” för `reported configuration` och `configurationValidation` att kontrollera att värden korrekt har spridits.

### <a name="tiering-properties"></a>Egenskaper för lagringsnivåer

Namnet på den här inställningen är `tieringSettings`

| Fält | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| tieringOn | SANT, FALSKT | Som standard anges till `false`, om du vill aktivera det på ställas `true`|
| backlogPolicy | NewestFirst OldestFirst | Kan du välja den ordning i vilken data kopieras till Azure. Som standard anges till `OldestFirst`. Ordningen som bestäms av tid för senaste ändring av Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` en anslutningssträng som låter dig ange Azure Storage-konto som du vill att dina data laddas upp. Ange `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Lägg till lämpliga EndpointSuffix Azure där data ska överföras, det varierar för Global Azure, Azure Government och Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Låter dig ange behållarnamn som du vill överföra till Azure. Den här modulen kan du ange behållarnamn för både källa och mål. Om du inte anger namnet på Målbehållaren, tilldelas den automatiskt behållarnamn som `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Du kan skapa mallen strängar för mål-behållarnamn, Kolla in kolumnen möjliga värden. <br>* %h -> IoT Hub-namn (3 – 50 tecken). <br>* %d -> IoT enhets-ID (1 till 129 tecken). <br>* %m -> Modulnamn (1 till 64 tecken). <br>* %c -> Källbehållarnamn (3 till 63 tecken). <br><br>Maxstorleken för behållarens namn är 63 tecken, samtidigt som automatiskt tilldelas namnet på Målbehållaren om storleken på behållare överskrider 63 tecken det beskära varje avsnitt (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) 15 tecken. |

### <a name="time-to-live-properties"></a>Egenskaper för Time to live

Namnet på den här inställningen är `ttlSettings`

| Fält | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| ttlOn | SANT, FALSKT | Som standard anges till `false`, om du vill aktivera det på ställas `true`|
| timeToLiveInMinutes | `<minutes>` | Ange TTL-värdet i minuter. Modulen tas bort automatiskt dina blobar från lokal lagring när TTL upphör att gälla |

## <a name="configure-log-files"></a>Konfigurera loggfiler

Information om hur du konfigurerar loggfiler för finns i dessa [Metodtips för produktion](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Ansluta till ditt blob storage-modulen

Du kan använda kontonamnet och nyckeln för att du har konfigurerat att få åtkomst till bloblagring på IoT Edge-enhet.

Ange din IoT Edge-enhet som blob-slutpunkt för lagring av alla begäranden som du gör. Du kan [skapa en anslutningssträng för en slutpunkt för lagring av explicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) med hjälp av informationen i IoT Edge och kontonamnet som du har konfigurerat.

- För moduler som har distribuerats på samma enhet som där Azure Blob Storage på IoT Edge-modulen körs, blob-slutpunkten är: `http://<module name>:11002/<account name>`.
- För moduler som har distribuerats på en annan enhet än där Azure Blob Storage på IoT Edge-modul som körs sedan beroende på din konfiguration av blobben är slutpunkten en av:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage-quickstart-exempel

Azure Blob Storage-dokumentationen innehåller snabbstarter som innehåller exempelkoden på flera språk. Du kan köra de här exemplen för att testa Azure Blob Storage på IoT Edge genom att ändra blob-slutpunkt för att ansluta till ditt blob storage-modulen.

Följande snabbstarter använder språk som stöds också av IoT Edge, så att du kan distribuera dem som IoT Edge-moduler tillsammans med blob storage-modulen:

- [NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Ansluta till din lokala lagring med Azure Storage Explorer

Du kan använda **Azure Storage Explorer** att ansluta till ditt konto för lokal lagring. Detta är endast tillgängligt med [Azure Storage Explorer version 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0).

> [!NOTE]
> Du kan stöta på fel när du utför följande steg, till exempel lägger till en anslutning till ett konto för lokal lagring eller skapa behållare i lokala storage-konto. Ignorera och uppdatera.

1. Ladda ned och installera Azure Storage Explorer

1. Ansluta till Azure Storage med hjälp av en anslutningssträng

1. Ange anslutningssträng: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Gå igenom stegen för att ansluta.

1. Skapa behållare i ditt konto för lokal lagring

1. Starta överföring av filer som blockblobar.
   > [!NOTE]
   > Den här modulen stöder inte sidblobar.

1. Du kan välja att ansluta dina Azure storage-konton där du laddar upp data. Det ger dig en enda vy för dina lokala storage-konto och ett Azure storage-konto

## <a name="supported-storage-operations"></a>Stöds lagringsåtgärder

BLOB storage-moduler på IoT Edge använder samma Azure Storage SDK och stämmer överens med den 2017-04-17-versionen av Azure Storage-API för block blob-slutpunkter. Senare versioner är beroende av kundernas behov.

Eftersom inte alla åtgärder i Azure Blob Storage stöds av Azure Blob Storage på IoT Edge kan innehåller det här avsnittet statusen för var och en.

### <a name="account"></a>Konto

Stöds:

- Visa en lista med containrar

Stöds inte:

- Hämta och ange egenskaper för blob
- Preliminära blob-begäran
- Hämta statistik för blob-tjänsten
- Hämta kontoinformation

### <a name="containers"></a>Containrar

Stöds:

- Skapa och ta bort behållare
- Hämta egenskaper för behållare och metadata
- Lista blobar
- Hämta och ange behållar-ACL
- Ställ in metadata för behållaren

Stöds inte:

- Lånet behållare

### <a name="blobs"></a>Blobar

Stöds:

- Placera, hämta och ta bort blob
- Hämta och ange egenskaper för blob
- Hämta och ange blob-metadata

Stöds inte:

- Lånet blob
- Ta ögonblicksbild av blob
- Kopiera och avbryta kopiering av blob
- Ångra borttagningen av bloben
- Ställ in blobbnivå

### <a name="block-blobs"></a>Blockblob-objekt

Stöds:

- Placera block
- Använda och få Blockeringslista

Stöds inte:

- Placera block från URL

## <a name="feedback"></a>Feedback

Din feedback är viktig för oss att göra den här modulen och dess funktioner praktiskt och enkelt att använda. Dela gärna din feedback och berätta för oss hur vi kan förbättra.

Du kan nå oss på absiotfeedback@microsoft.com

## <a name="next-steps"></a>Nästa steg

Läs mer om [distribuera Azure Blob Storage på IoT Edge](how-to-deploy-blob.md)
