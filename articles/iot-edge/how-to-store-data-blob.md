---
title: Store blockblob-objekt på enheter – Azure IoT Edge | Microsoft Docs
description: Förstå lagringsnivåer och time-to-live-funktioner, visa stöds blob storage-åtgärder och ansluta till ditt blob storage-konto.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f70ca550f1688551abb94bb30ba4f76eb3c36404
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303968"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store data på gränsen med Azure Blob Storage på IoT Edge (förhandsversion)

Azure Blob Storage på IoT Edge innehåller en [blockblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) lagringslösning kant. Ett blob storage-modulen på din IoT Edge-enhet som fungerar som en tjänst för Azure block blob, men blockblob-objekt lagras lokalt på din IoT Edge-enhet. Du kan få åtkomst till dina blobar på samma sätt i Azure storage SDK eller blockera blob API-anrop som du redan är van att.

Den här modulen som levereras med **deviceToCloudUpload** och **deviceAutoDelete** funktioner.
> [!NOTE]
> Azure Blob Storage på IoT Edge är i [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Titta på videon och få snabb introduktion
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload** är en konfigurerbar funktioner, där du kan automatiskt ladda upp data från din lokala blob storage till Azure med stöd för tillfälliga internet-anslutning. På så sätt kan du:

- Aktivera på/av funktionen deviceToCloudUpload.
- Välj den ordning i vilken data kopieras till Azure som NewestFirst eller OldestFirst.
- Ange Azure Storage-konto som du vill att dina data som överförs.
- Ange de behållare som du vill överföra till Azure. Den här modulen kan du ange behållarnamn för både källa och mål.
- Välj möjlighet att ta bort blobar omedelbart efter överföringen till molnlagring har slutförts
- Hela ladda upp blob (med hjälp av `Put Blob` åtgärden) och blockera på överföring (med hjälp av `Put Block` och `Put Block List` operations).

Den här modulen använder blockera på överföring, när din blob består av block. Här följer några vanliga scenarier:

- Programmet uppdateras vissa block med en tidigare överförda blob, den här modulen Överför bara uppdaterade block och inte hela blob.
- Modulen överför blob och internet-anslutning är borta, när anslutningen är tillbaka igen överförs endast de återstående block och inte hela blob.

Om en oväntat avslut (till exempel strömavbrott) inträffar under en blob-överföringen kan kommer alla block som var dags att överföringen att överföras igen, när modulen är online igen.

**deviceAutoDelete** är en konfigurerbar funktionalitet där modulen tar automatiskt bort dina blobar från lokal lagring när den angivna varaktigheten (mätt i minuter) upphör att gälla. På så sätt kan du:

- Aktivera på/av funktionen deviceAutoDelete.
- Ange tiden i minuter (deleteAfterMinutes) efter vilket blobbarna tas automatiskt bort.
- Välj möjlighet att behålla blob när den överförs om deleteAfterMinutes värdet upphör att gälla.

Scenarier där data som videor, bilder, ekonomi, sjukhus data eller data som behöver lagras lokalt, senare som kan bearbetas lokalt eller överförs till molnet är bra exempel att använda den här modulen.

Den här artikeln förklarar begrepp relaterade till Azure Blob Storage på IoT Edge-behållaren som kör en blob-tjänst på din IoT Edge-enhet.

## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

- Du kan använda utvecklingsdatorn eller en virtuell dator som en IoT Edge-enhet genom att följa stegen i snabbstarten för [Linux](quickstart-linux.md) eller [Windows-enheter](quickstart.md).

- Azure Blob Storage på IoT Edge-modul har stöd för följande enhetskonfigurationer:

  | Operativsystem | Arkitektur |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Enterprise | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian stretch | ARM32 |

Molnresurser:

En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Egenskaper för deviceToCloudUpload och deviceAutoDelete

Använd önskade egenskaper för att ange deviceToCloudUploadProperties och deviceAutoDeleteProperties. De kan ange under distributionen eller ändras senare genom att redigera modultvilling utan att behöva distribuera om. Vi rekommenderar att du kontrollerar ”Modultvilling” för `reported configuration` och `configurationValidation` att kontrollera att värden korrekt har spridits.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Namnet på den här inställningen är `deviceToCloudUploadProperties`

| Fält | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| uploadOn | true, false | Som standard anges till `false`, om du vill aktivera det på ställas `true`|
| uploadOrder | NewestFirst OldestFirst | Kan du välja den ordning i vilken data kopieras till Azure. Som standard anges till `OldestFirst`. Ordningen som bestäms av tid för senaste ändring av Blob |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` en anslutningssträng som låter dig ange Azure Storage-konto som du vill att dina data laddas upp. Ange `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Lägg till lämpliga EndpointSuffix Azure där data ska överföras, det varierar för Global Azure, Azure Government och Microsoft Azure Stack. |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Låter dig ange behållarnamn som du vill överföra till Azure. Den här modulen kan du ange behållarnamn för både källa och mål. Om du inte anger namnet på Målbehållaren, tilldelas den automatiskt behållarnamn som `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Du kan skapa mallen strängar för mål-behållarnamn, Kolla in kolumnen möjliga värden. <br>* %h -> IoT Hub-namn (3 – 50 tecken). <br>* %d -> IoT Edge enhets-ID (1 till 129 tecken). <br>* %m -> Modulnamn (1 till 64 tecken). <br>* %c -> Källbehållarnamn (3 till 63 tecken). <br><br>Maxstorleken för behållarens namn är 63 tecken, samtidigt som automatiskt tilldelas namnet på Målbehållaren om storleken på behållare överskrider 63 tecken det beskära varje avsnitt (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) 15 tecken. |
| deleteAfterUpload | true, false | Som standard anges till `false`. När den är inställd på `true`, data tas bort automatiskt när du överför till molnarkivet är klar |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Namnet på den här inställningen är `deviceAutoDeleteProperties`

| Fält | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| deleteOn | true, false | Som standard anges till `false`, om du vill aktivera det på ställas `true`|
| deleteAfterMinutes | `<minutes>` | Ange tiden i minuter. Modulen tas bort automatiskt dina blobar från lokal lagring när det här värdet upphör att gälla |
| retainWhileUploading | true, false | Som standard anges till `true`, och eventuell blob när den överförs till molnlagring om deleteAfterMinutes upphör att gälla. Du kan ange den till `false` och data tas bort när deleteAfterMinutes upphör att gälla. Obs! För den här egenskapen ska fungera uploadOn ska anges till true|

## <a name="configure-log-files"></a>Konfigurera loggfiler

Information om hur du konfigurerar loggfiler för finns i dessa [Metodtips för produktion](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Ansluta till ditt blob storage-modulen

Du kan använda kontonamnet och nyckeln för att du har konfigurerat att få åtkomst till bloblagring på IoT Edge-enhet.

Ange din IoT Edge-enhet som blob-slutpunkt för lagring av alla begäranden som du gör. Du kan [skapa en anslutningssträng för en slutpunkt för lagring av explicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) med hjälp av informationen i IoT Edge och kontonamnet som du har konfigurerat.

- För moduler som har distribuerats på samma enhet som där Azure Blob Storage på IoT Edge-modulen körs, blob-slutpunkten är: `http://<module name>:11002/<account name>`.
- För externa moduler eller program som körs på en annan enhet än där Azure Blob Storage på IoT Edge-modul som körs sedan beroende på din konfiguration för nätverk, så att trafiken data från din externa modulen eller ditt program kan nå din enhet Kör Azure Blob Storage på IoT Edge-modul, är blob-slutpunkten en av:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage-quickstart-exempel

Azure Blob Storage-dokumentationen innehåller Snabbstart exempelkoden på flera språk. Du kan köra de här exemplen för att testa Azure Blob Storage på IoT Edge genom att ändra blob-slutpunkt för att ansluta till din lokala blob storage-modulen.

Följande exempel i snabbstarten använda språk som stöds också av IoT Edge, så att du kan distribuera dem som IoT Edge-moduler tillsammans med blob storage-modulen:

- [NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Ansluta till din lokala lagring med Azure Storage Explorer

Du kan använda [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) att ansluta till ditt konto för lokal lagring.

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

## <a name="release-notes"></a>Viktig information

Här är den [viktig information i docker hub](https://hub.docker.com/_/microsoft-azure-blob-storage) för den här modulen

## <a name="feedback"></a>Feedback

Din feedback är viktig för oss att göra den här modulen och dess funktioner praktiskt och enkelt att använda. Dela gärna din feedback och berätta för oss hur vi kan förbättra.

Du kan nå oss på absiotfeedback@microsoft.com

## <a name="next-steps"></a>Nästa steg

Läs mer om [distribuera Azure Blob Storage på IoT Edge](how-to-deploy-blob.md)
