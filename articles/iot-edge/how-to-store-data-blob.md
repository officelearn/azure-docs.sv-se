---
title: Lagra blockblobar på enheter – Azure IoT Edge | Microsoft-dokument
description: Förstå nivåindelning och time-to-live-funktioner, se blob-lagringsåtgärder som stöds och anslut till ditt blob-lagringskonto.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509826"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Lagra data på gränsen med Azure Blob Storage på IoT Edge

Azure Blob Storage på IoT Edge tillhandahåller en [blockblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) och [tillägg av blob-lagringslösning](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) på kanten. En blob-lagringsmodul på din IoT Edge-enhet fungerar som en Azure-blob-tjänst, förutom att blobbar lagras lokalt på din IoT Edge-enhet. Du kan komma åt dina blobbar med samma Azure-lagring SDK-metoder eller blob API-anrop som du redan är van vid. I den här artikeln beskrivs begreppen relaterad till Azure Blob Storage på IoT Edge-behållare som kör en blob-tjänst på din IoT Edge-enhet.

Den här modulen är användbar i scenarier:

* där data måste lagras lokalt tills de kan bearbetas eller överföras till molnet. Dessa data kan vara videor, bilder, ekonomidata, sjukhusdata eller andra ostrukturerade data.
* när enheterna finns på en plats med begränsad anslutning.
* när du vill bearbeta data lokalt för att få låg latensåtkomst till data, så att du kan svara på nödsituationer så snabbt som möjligt.
* när du vill minska bandbreddskostnaderna och undvika att överföra terabyte data till molnet. Du kan bearbeta data lokalt och skicka endast bearbetade data till molnet.

Titta på videon för snabb introduktion
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Den här modulen levereras med **deviceToCloudUpload-** och **deviceAutoDelete-funktioner.**

**deviceToCloudUpload** är en konfigurerbar funktion. Den här funktionen överför automatiskt data från din lokala blob-lagring till Azure med intermittent stöd för internetanslutning. Det gör att du kan:

* Aktivera/stänga av funktionen deviceToCloudUpload.
* Välj i vilken ordning data kopieras till Azure som NewestFirst eller OldestFirst.
* Ange det Azure Storage-konto som du vill att dina data ska överföras till.
* Ange de behållare som du vill överföra till Azure. Med den här modulen kan du ange både käll- och målbehållarenamn.
* Välj möjligheten att ta bort blobbar direkt, när överföringen till molnlagring är klar
* Gör fullständig blob-uppladdning (med hjälp `Put Blob` av åtgärd) och blocknivåöverföring (med hjälp `Put Block`av och `Put Block List` `Append Block` åtgärder).

Den här modulen använder blocknivåuppladdning när din blob består av block. Här är några av de vanligaste scenarierna:

* Ditt program uppdaterar några block av en tidigare uppladdad block blob eller lägger till nya block till en tillägg blob, laddar denna modul bara uppdaterade block och inte hela blob.
* Modulen laddar upp blob och internetuppkoppling går bort, när anslutningen är tillbaka igen det laddar upp bara de återstående blocken och inte hela blob.

Om en oväntad processavslutning (som strömavbrott) inträffar under en blob-uppladdning, kommer alla block som skulle ha överförts för uppladdningen att laddas upp igen när modulen är online igen.

**deviceAutoDelete** är en konfigurerbar funktion. Den här funktionen tar automatiskt bort blobbar från det lokala lagringsutrymmet när den angivna varaktigheten (mätt i minuter) upphör att gälla. Det gör att du kan:

* Slå på/av enhetenAutoDelete-funktionen.
* Ange den tid i minuter (deleteAfterMinutes) varefter blobbar ska tas bort automatiskt.
* Välj möjligheten att behålla blobben medan den laddar upp om värdet deleteAfterMinutes upphör att gälla.

## <a name="prerequisites"></a>Krav

En Azure IoT Edge-enhet:

* Du kan använda din utvecklingsdator eller en virtuell dator som en IoT Edge-enhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).

* Se [Azure IoT Edge-stödda system](support.md#operating-systems) för en lista över operativsystem och arkitekturer som stöds. Azure Blob Storage på IoT Edge-modulen stöder följande arkitekturer:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (förhandsgranskning)

Molnresurser:

En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload och deviceAutoDelete egenskaper

Använd modulens önskade egenskaper för att ställa in **deviceToCloudUploadProperties** och **deviceAutoDeleteProperties**. Önskade egenskaper kan ställas in under distributionen eller ändras senare genom att redigera modultvillingen utan att behöva distribuera om. Vi rekommenderar att du kontrollerar "Modultvilling" för `reported configuration` och `configurationValidation` för att se till att värdena sprids korrekt.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Namnet på den `deviceToCloudUploadProperties`här inställningen är . Om du använder IoT Edge-simulatorn anger du värdena på relaterade miljövariabler för dessa egenskaper, som du hittar i förklaringsavsnittet.

| Egenskap | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| uploadOn | SANT, FALSKT | Ställ `false` in på som standard. Om du vill aktivera funktionen ställer du `true`in det här fältet på . <br><br> Miljövariabel:`deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NyasteFörsta, ÄldstaFörsta | Gör att du kan välja i vilken ordning data kopieras till Azure. Ställ `OldestFirst` in på som standard. Ordern bestäms av den senast ändrade tiden för Blob. <br><br> Miljövariabel:`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`är en anslutningssträng som gör att du kan ange det lagringskonto som du vill att dina data ska överföras till. Ange `Azure Storage Account Name` `Azure Storage Account Key`, `End point suffix`, . Lägg till lämpliga EndpointSuffix för Azure där data kommer att överföras, det varierar för Global Azure, Government Azure och Microsoft Azure Stack. <br><br> Du kan välja att ange Azure Storage SAS-anslutningssträng här. Men du måste uppdatera den här egenskapen när den går ut. <br><br> Miljövariabel:`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| lagringContainersFörfördlagring | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Gör att du kan ange de behållarnamn som du vill överföra till Azure. Med den här modulen kan du ange både käll- och målbehållarenamn. Om du inte anger målbehållarens namn tilldelas behållarnamnet `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`automatiskt som . Du kan skapa mallsträngar för målbehållarens namn, kolla in kolumnen Möjliga värden. <br>* %h -> IoT Hub Namn (3-50 tecken). <br>* %d -> IoT Edge Device ID (1 till 129 tecken). <br>* %m -> modulnamn (1 till 64 tecken). <br>* %c -> Källbehållarens namn (3 till 63 tecken). <br><br>Maximal storlek på behållarnamnet är 63 tecken, medan målbehållarens namn automatiskt tilldelas om behållarens storlek överstiger 63 tecken trimmas varje avsnitt (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) till 15 Tecken. <br><br> Miljövariabel:`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | SANT, FALSKT | Ställ `false` in på som standard. När den är `true`inställd på tas data automatiskt bort när överföringen till molnlagring är klar. <br><br> **VARNING:** Om du använder tilläggsblobar tas tilläggsblobbar bort från lokal lagring efter en lyckad överföring, och eventuella framtida Tilläggsblockåtgärder till dessa blobbar misslyckas. Använd den här inställningen med försiktighet, aktivera inte detta om ditt program inte gör ovanliga tilläggsåtgärder eller inte stöder kontinuerliga tilläggsåtgärder<br><br> Miljövariabel: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`. |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Namnet på den `deviceAutoDeleteProperties`här inställningen är . Om du använder IoT Edge-simulatorn anger du värdena på relaterade miljövariabler för dessa egenskaper, som du hittar i förklaringsavsnittet.

| Egenskap | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| deleteOn (på) | SANT, FALSKT | Ställ `false` in på som standard. Om du vill aktivera funktionen ställer du `true`in det här fältet på . <br><br> Miljövariabel:`deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Ange tid i minuter. Modulen tar automatiskt bort dina blobbar från lokal lagring när det här värdet upphör att gälla. <br><br> Miljövariabel:`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | SANT, FALSKT | Som standard är `true`den inställd på , och den behåller bloben medan den överför till molnlagring om deleteAfterMinutes upphör att gälla. Du kan ställa `false` in den till och det kommer att ta bort data så snart deleteAfterMinutes löper ut. Obs: För den här egenskapen att arbeta uploadOn bör ställas in på true.  <br><br> **VARNING:** Om du använder tilläggsblobar tas tilläggsblobbar bort från lokal lagring när värdet upphör att gälla, och eventuella framtida Tilläggsblockåtgärder till dessa blobbar misslyckas. Du kanske vill kontrollera att utgångsvärdet är tillräckligt stort för den förväntade frekvensen av tilläggsåtgärder som utförs av ditt program.<br><br> Miljövariabel:`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Använda SMB-resurs som lokal lagring

Du kan ange SMB-resurs som din lokala lagringssökväg när du distribuerar Windows-behållaren för den här modulen på Windows-värden.

Kontrollera att SMB-resursen och IoT-enheten finns i ömsesidigt betrodda domäner.

Du kan `New-SmbGlobalMapping` köra PowerShell-kommandot för att mappa SMB-resursen lokalt på IoT-enheten som kör Windows.

Nedan följer konfigurationsstegen:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Ett exempel:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Det här kommandot använder autentiseringsuppgifterna för att autentisera med fjärr-SMB-servern. Mappa sedan fjärrdelningssökvägen till G: enhetsbeteckning (kan vara vilken annan enhetsbeteckning som helst). IoT-enheten har nu datavolymen mappad till en sökväg på G:-enheten.

Kontrollera att användaren i IoT-enheten kan läsa/skriva till fjärrresursen för SMB.Make sure the user in IoT device can read/write to the remote SMB share.

För distributionen `<storage mount>` kan värdet för vara **G:/ContainerData:C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Bevilja katalogåtkomst till behållaranvändare på Linux

Om du har använt [volymmontering](https://docs.docker.com/storage/volumes/) för lagring i dina skapa alternativ för Linux-behållare behöver du inte göra några extra steg, men om du använde [bind montera](https://docs.docker.com/storage/bind-mounts/) då dessa steg krävs för att köra tjänsten korrekt.

Enligt principen om lägsta behörighet att begränsa åtkomsträttigheter för användare till minsta möjliga behörigheter som de behöver för att utföra sitt arbete, innehåller den här modulen en användare (namn: absie, ID: 11000) och en användargrupp (namn: absie, ID: 11000). Om behållaren startas som **root** (standardanvändare är **root)** startas vår tjänst som **absie-användare** med låg behörighet.

Detta gör konfigurationen av behörigheterna för värdsökvägen binder avgörande för att tjänsten ska fungera korrekt, annars kraschar tjänsten med nekade fel. Sökvägen som används i katalogbindningen måste vara tillgänglig för behållaranvändaren (t.ex. absie 11000). Du kan bevilja behållaranvändaren åtkomst till katalogen genom att köra kommandona nedan på värden:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Ett exempel:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Om du behöver köra tjänsten som en annan användare än **absie**kan du ange ditt anpassade användar-ID i createOptions under egenskapen "Användare" i distributionsmanifestet. I sådana fall måste du använda standard- eller rotgrupp-ID `0`.

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Nu, bevilja behållaren användaren åtkomst till katalogen

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Konfigurera loggfiler

Information om hur du konfigurerar loggfiler för modulen finns i de [här metodtipsen](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)för produktion .

## <a name="connect-to-your-blob-storage-module"></a>Ansluta till din blob-lagringsmodul

Du kan använda kontonamnet och kontonyckeln som du har konfigurerat för din modul för att komma åt blob-lagringen på din IoT Edge-enhet.

Ange din IoT Edge-enhet som blob-slutpunkt för alla lagringsbegäranden som du gör till den. Du kan [skapa en anslutningssträng för en explicit lagringsslutpunkt](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) med hjälp av IoT Edge-enhetsinformationen och kontonamnet som du har konfigurerat.

* För moduler som distribueras på samma enhet som där Azure Blob Storage på IoT Edge-modulen körs är blob-slutpunkten: `http://<module name>:11002/<account name>`.
* För moduler eller program som körs på en annan enhet måste du välja rätt slutpunkt för nätverket. Beroende på nätverkskonfigurationen väljer du ett slutpunktsformat så att datatrafiken från den externa modulen eller programmet kan nå enheten som kör Azure Blob Storage på IoT Edge-modulen. Blob-slutpunkten för det här scenariot är en av:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Snabbstartsexempel för Azure Blob Storage

Azure Blob Storage-dokumentationen innehåller snabbstartsprovkod på flera språk. Du kan köra dessa exempel för att testa Azure Blob Storage på IoT Edge genom att ändra blob-slutpunkten för att ansluta till din lokala blob storage-modul.

Följande snabbstartsexempel använder språk som också stöds av IoT Edge, så att du kan distribuera dem som IoT Edge-moduler tillsammans med blob-lagringsmodulen:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Versioner före V2.1 av Python SDK har ett känt problem där modulen inte returnerar blob skapande tid. På grund av det problemet fungerar inte vissa metoder som listblobar. Som en lösning anger du uttryckligen API-versionen på blob-klienten till '2017-04-17'. Exempel:`block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Exempel på lägger till blob](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Kör](../storage/blobs/storage-quickstart-blobs-go.md)
* [Php](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Ansluta till ditt lokala lagringsutrymme med Azure Storage Explorer

Du kan använda [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ansluta till ditt lokala lagringskonto.

1. Ladda ned och installera Azure Storage Explorer

1. Ansluta till Azure Storage med en anslutningssträng

1. Ange anslutningssträng:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Gå igenom stegen för att ansluta.

1. Skapa behållare i ditt lokala lagringskonto

1. Börja ladda upp filer som Block blobbar eller Lägg till Blobbar.
   > [!NOTE]
   > Den här modulen stöder inte sidblobar.

1. Du kan också välja att ansluta dina Azure-lagringskonton i Storage Explorer. Den här konfigurationen ger dig en enda vy för både ditt lokala lagringskonto och Azure-lagringskonto

## <a name="supported-storage-operations"></a>Lagringsåtgärder som stöds

Blob-lagringsmoduler på IoT Edge använder Azure Storage SDK:er och överensstämmer med 2017-04-17-versionen av Azure Storage API för blockblobbslutpunkter.

Eftersom inte alla Azure Blob Storage-åtgärder stöds av Azure Blob Storage på IoT Edge, visar det här avsnittet status för varje.

### <a name="account"></a>Konto

Stöds:

* Visa en lista med containrar

Unsupported:

* Hämta och ange egenskaper för blob-tjänst
* Preflight blob-begäran
* Få statistik över blob-tjänst
* Hämta kontoinformation

### <a name="containers"></a>Containrar

Stöds:

* Skapa och ta bort behållare
* Hämta behållaregenskaper och metadata
* Lista blobar
* Hämta och ställ in behållaren ACL
* Ange behållarmetadata

Unsupported:

* Lånebehållare

### <a name="blobs"></a>Blobar

Stöds:

* Sätta, hämta och ta bort blob
* Hämta och ange blob-egenskaper
* Hämta och ange blobmetadata

Unsupported:

* Leasa blob
* Blob för ögonblicksbilder
* Kopiera och avbryta kopieringsblobb
* Undelete blob
* Ange blob-nivå

### <a name="block-blobs"></a>Blockblobar

Stöds:

* Placera block
* Placera och hämta blocklista

Unsupported:

* Placera block från URL

### <a name="append-blobs"></a>Tilläggsblobar

Stöds:

* Lägg till block

Unsupported:

* Lägg till block från URL

## <a name="event-grid-on-iot-edge-integration"></a>Händelserutnät för IoT Edge-integrering

> [!CAUTION]
> Integreringen med Event Grid på IoT Edge är i förhandsversion

Den här Azure Blob Storage på IoT Edge-modulen ger nu integrering med Event Grid på IoT Edge. Detaljerad information om den här integreringen finns i [självstudien för att distribuera modulerna, publicera händelser och verifiera händelseleverans](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Viktig information

Här är [viktig information i docker hub](https://hub.docker.com/_/microsoft-azure-blob-storage) för den här modulen

## <a name="feedback"></a>Feedback

Din feedback är viktig för oss för att göra denna modul och dess funktioner användbara och lätta att använda. Vänligen dela din feedback och låt oss veta hur vi kan förbättra.

Du kan nå oss påabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar Azure Blob Storage på IoT Edge](how-to-deploy-blob.md)

Håll dig uppdaterad med de senaste uppdateringarna och meddelandena i [Azure Blob Storage på IoT Edge-bloggen](https://aka.ms/abs-iot-blogpost)
