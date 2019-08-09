---
title: Store blockblob-objekt på enheter – Azure IoT Edge | Microsoft Docs
description: Förstå nivåer och tids till Live-funktioner finns i Blob Storage-åtgärder som stöds och Anslut till ditt Blob Storage-konto.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a40389ca378826aef1b6aa136f8f5d69783c638e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881222"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Lagra data på gränsen med Azure Blob Storage på IoT Edge

Azure Blob Storage på IoT Edge innehåller en [blockblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) lagringslösning kant. En Blob Storage-modul på din IoT Edge-enhet fungerar som en Azure Block Blob-tjänst, förutom att block-blobbar lagras lokalt på IoT Edge enheten. Du kan få åtkomst till dina blobar på samma sätt i Azure storage SDK eller blockera blob API-anrop som du redan är van att. I den här artikeln beskrivs begreppen för Azure Blob Storage på IoT Edge behållare som kör en blob-tjänst på din IoT Edge enhet.

Den här modulen är användbar i scenarier:
* var data måste lagras lokalt tills den kan bearbetas eller överföras till molnet. Dessa data kan vara videor, bilder, ekonomi data, sjukhus data eller andra ostrukturerade data.
* När enheterna finns på en plats med begränsad anslutning.
* När du effektivt vill bearbeta data lokalt för att få låg latens åtkomst till data, så att du kan reagera på nödfall så snabbt som möjligt.
* När du vill minska bandbredds kostnaderna och undvika att överföra terabyte data till molnet. Du kan bearbeta data lokalt och bara skicka bearbetade data till molnet.

Titta på videon för snabb introduktion
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

Den här modulen innehåller funktioner för **deviceToCloudUpload** och **deviceAutoDelete** .

**deviceToCloudUpload** är en konfigurerbar funktion. Den här funktionen överför automatiskt data från den lokala blob-lagringen till Azure med stöd för Internet anslutning utan avbrott. Det gör att du kan:

- Aktivera/inaktivera deviceToCloudUpload-funktionen.
- Välj i vilken ordning data ska kopieras till Azure som NewestFirst eller OldestFirst.
- Ange det Azure Storage konto som du vill att dina data ska överföras till.
- Ange de behållare som du vill överföra till Azure. Med den här modulen kan du ange namn på både käll-och mål behållare.
- Välj möjligheten att ta bort Blobbarna direkt efter att överföringen till moln lagringen är färdig
- Utför fullständig BLOB-uppladdning ( `Put Blob` med drift) och överföring på Block nivå `Put Block` ( `Put Block List` med och åtgärder).

I den här modulen används överföring på Block nivå, när blobben består av block. Här följer några vanliga scenarier:

- Programmet uppdaterar vissa block av en tidigare överförd blob. i den här modulen överförs bara de uppdaterade blocken och inte hela bloben.
- Modulen laddar upp blob och Internet-anslutningen stängs av när anslutningen är tillbaka och laddar bara om de återstående blocken och inte hela bloben.

Om en oväntad process terminering (t. ex. strömavbrott) sker under en BLOB-uppladdning, laddas alla block som har förfallit till uppladdningen igen när modulen är online igen.

**deviceAutoDelete** är en konfigurerbar funktion. Den här funktionen tar automatiskt bort blobbar från den lokala lagrings platsen när den angivna varaktigheten (mätt i minuter) går ut. Det gör att du kan:

- Aktivera/inaktivera deviceAutoDelete-funktionen.
- Ange tiden i minuter (deleteAfterMinutes) efter vilken Blobbarna ska tas bort automatiskt.
- Välj möjligheten att behålla blobben när den laddas upp om deleteAfterMinutes-värdet upphör att gälla.


## <a name="prerequisites"></a>Förutsättningar

En Azure IoT Edge-enhet:

- Du kan använda din utvecklings dator eller en virtuell dator som en IoT Edge enhet genom att följa stegen i snabb starten för [Linux](quickstart-linux.md) -eller [Windows-enheter](quickstart.md).

- Se [Azure IoT Edge system som stöds](support.md#operating-systems) för en lista över operativ system och arkitekturer som stöds. Azure-Blob Storage i IoT Edge-modulen stöder följande arkitekturer:
    - Windows AMD64
    - Linux AMD64
    - Linux ARM32
    - Linux-ARM64 (för hands version)

Molnresurser:

En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>egenskaper för deviceToCloudUpload och deviceAutoDelete

Använd modulens önskade egenskaper för att ange **deviceToCloudUploadProperties** och **deviceAutoDeleteProperties**. Du kan ställa in önskade egenskaper under distributionen eller ändra dem senare genom att redigera modulen dubbla utan att behöva distribuera om. Vi rekommenderar att du kontrollerar "modulin" `reported configuration` för `configurationValidation` och för att se till att värdena är korrekt spridda.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Namnet på den här inställningen är`deviceToCloudUploadProperties`

| Fält | Möjliga värden | Förklaring | Miljövariabel |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | Ange som `false` standard. Om du vill aktivera funktionen väljer du det här fältet till `true`. | `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Gör att du kan välja i vilken ordning data ska kopieras till Azure. Ange som `OldestFirst` standard. Ordningen bestäms efter tidpunkten för senaste ändring av Bloben | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`är en anslutnings sträng som gör att du kan ange det lagrings konto som du vill att dina data ska överföras till. Ange `Azure Storage Account Name`, `Azure Storage Account Key`, .`End point suffix` Lägg till lämpliga EndpointSuffix av Azure där data ska överföras, det varierar för Global Azure, Azure och Microsoft Azure Stack. <br><br> Du kan välja att ange Azure Storage SAS-anslutningssträng här. Men du måste uppdatera den här egenskapen när den upphör att gälla.  | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Gör att du kan ange de behållar namn som du vill överföra till Azure. Med den här modulen kan du ange namn på både käll-och mål behållare. Om du inte anger namnet på mål behållaren tilldelas behållar namnet automatiskt som `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Du kan skapa mall strängar för mål behållar namn, se kolumnen möjliga värden. <br>*% h – > IoT Hub namn (3-50 tecken). <br>*% d-> IoT Edge enhets-ID (1 till 129 tecken). <br>*% m – > modulens namn (1 till 64 tecken). <br>*% c – > käll behållar namn (3 till 63 tecken). <br><br>Den maximala storleken på behållar namnet är 63 tecken och tilldelar mål behållar namnet automatiskt om storleken på containern överskrider 63 tecken så trimmas varje avsnitt (IoTHubName, IotEdgeDeviceID, Modulnamn, SourceContainerName) till 15 tabbtecken. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Ange som `false` standard. När det är inställt på `true`tas data bort automatiskt när uppladdning till moln lagring är klart | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Namnet på den här inställningen är`deviceAutoDeleteProperties`

| Fält | Möjliga värden | Förklaring | Miljövariabel |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Ange som `false` standard. Om du vill aktivera funktionen väljer du det här fältet till `true`. | `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Ange tiden i minuter. Dina blobar tas bort automatiskt från den lokala lagrings platsen när det här värdet upphör att gälla | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Som standard är den inställd på `true`och den behåller blobben medan den laddas upp till moln lagring om deleteAfterMinutes upphör att gälla. Du kan ställa in det `false` på så att det tar bort data så snart deleteAfterMinutes går ut. Anteckning: För att den här egenskapen ska fungera måste uploadOn anges till sant| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="using-smb-share-as-your-local-storage"></a>Använda SMB-resurs som lokal lagring
Du kan ange SMB-resurs som din lokala lagrings Sök väg när du distribuerar Windows-behållare för den här modulen på Windows-värden.

Kontrol lera att SMB-resursen och IoT-enheten finns i ömsesidigt betrodda domäner.

Du kan köra `New-SmbGlobalMapping` PowerShell-kommandot för att mappa SMB-resursen lokalt på IoT-enheten som kör Windows.

Nedan visas konfigurations stegen:
```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```
Exempel: <br>
`$creds = Get-Credential` <br>
`New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G: `

Det här kommandot använder autentiseringsuppgifterna för att autentisera med fjärr-SMB-servern. Mappa sedan sökvägen till fjär resursen till G: enhets beteckningen (kan vara en annan tillgänglig enhets beteckning). IoT-enheten har nu data volymen mappad till en sökväg på enheten G:. 

Se till att användaren i IoT-enheten kan läsa och skriva till fjärr-SMB-resursen.

För distributionen `<storage mount>` kan värdet vara **G:/ContainerData: C:/BlobRoot**. 

## <a name="granting-directory-access-to-container-user-on-linux"></a>Bevilja katalog åtkomst till behållar användare i Linux
Om du har använt [volym montering](https://docs.docker.com/storage/volumes/) för lagring i dina alternativ för att skapa en Linux-behållare behöver du inte göra några ytterligare steg, men om du använde [BIND Mount](https://docs.docker.com/storage/bind-mounts/) krävs dessa steg för att köra tjänsten på rätt sätt.

Enligt principen om minsta behörighet för att begränsa åtkomst rättigheterna för användare till minimalt minimal behörighet som de behöver för att utföra sitt arbete, innehåller den här modulen en användare (namn: absie, ID: 11000) och en användar grupp (namn: absie, ID: 11000). Om behållaren har startats som **rot** (standard användare är **rot**), startas tjänsten som **absie** användare med låg behörighet. 

Det här beteendet gör att konfigurationen av behörigheterna för värdsökväg binder är avgörande för att tjänsten ska fungera korrekt, annars kraschar tjänsten med åtkomst nekade fel. Den sökväg som används i katalog bindningen måste vara tillgänglig för behållar användaren (exempel: absie 11000). Du kan bevilja behållar användaren åtkomst till katalogen genom att köra kommandona nedan på värden:

```terminal
sudo chown -R 11000:11000 <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

Exempel:<br>
`sudo chown -R 11000:11000 /srv/containerdata` <br>
`sudo chmod -R 700 /srv/containerdata `


Om du behöver köra tjänsten som en annan användare än **absie**kan du ange ditt anpassade användar-ID i createOptions under egenskapen User i distributions manifestet. I så fall måste du använda standard-eller rot grupps `0`-ID: t.

```json
“createOptions”: { 
  “User”: “<custom user ID>:0” 
} 
```
Bevilja nu behållar användaren åtkomst till katalogen
```terminal
sudo chown -R <user ID>:<group ID> <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

## <a name="configure-log-files"></a>Konfigurera loggfiler

Information om hur du konfigurerar loggfiler för modulen finns i [metod tips för produktion](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Ansluta till ditt blob storage-modulen

Du kan använda kontonamnet och nyckeln för att du har konfigurerat att få åtkomst till bloblagring på IoT Edge-enhet.

Ange din IoT Edge-enhet som blob-slutpunkt för lagring av alla begäranden som du gör. Du kan [skapa en anslutningssträng för en slutpunkt för lagring av explicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) med hjälp av informationen i IoT Edge och kontonamnet som du har konfigurerat.

- För moduler som har distribuerats på samma enhet som Azure-Blob Storage på IoT Edge modul körs, är BLOB-slutpunkten: `http://<module name>:11002/<account name>`.
- För moduler eller program som körs på en annan enhet måste du välja rätt slut punkt för nätverket. Beroende på din nätverks konfiguration väljer du ett slut punkts format så att data trafiken från den externa modulen eller programmet kan komma åt enheten som kör Azure-Blob Storage i IoT Edge-modulen. BLOB-slutpunkten för det här scenariot är en av:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage snabb starts exempel

Azure Blob Storage-dokumentationen innehåller exempel kod för snabb start på flera språk. Du kan köra dessa exempel för att testa Azure Blob Storage på IoT Edge genom att ändra BLOB-slutpunkten för att ansluta till din lokala Blob Storage-modul.

Följande snabb starts exempel använder språk som också stöds av IoT Edge, så du kan distribuera dem som IoT Edge moduler tillsammans med Blob Storage-modulen:

- [NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java-v10.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-v10.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Ansluta till din lokala lagrings plats med Azure Storage Explorer

Du kan använda [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ansluta till ditt lokala lagrings konto.

1. Hämta och installera Azure Storage Explorer

1. Anslut till Azure Storage med hjälp av en anslutnings sträng

1. Ange anslutnings sträng:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Gå igenom stegen för att ansluta.

1. Skapa behållare inuti ditt lokala lagrings konto

1. Starta överföring av filer som block blobbar.
   > [!NOTE]
   > Den här modulen stöder inte sid-blobar.

1. Du kan också välja att ansluta dina Azure Storage-konton i Storage Explorer. Med den här konfigurationen får du en enda vy för både ditt lokala lagrings konto och ditt Azure Storage-konto

## <a name="supported-storage-operations"></a>Stöds lagringsåtgärder

Blob Storage-moduler på IoT Edge använda Azure Storage SDK: er och är konsekvent med 2017-04-17-versionen av Azure Storage API för Block-Blob-slutpunkter. 

Eftersom inte alla Azure Blob Storage-åtgärder stöds av Azure Blob Storage på IoT Edge, visar det här avsnittet status för var och en.

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

- Spärra block
- Använda och få Blockeringslista

Stöds inte:

- Placera block från URL

## <a name="release-notes"></a>Viktig information

Här är [viktig information i Docker Hub](https://hub.docker.com/_/microsoft-azure-blob-storage) för den här modulen

## <a name="feedback"></a>Feedback

Din feedback är viktig för oss att göra den här modulen och dess funktioner användbara och lätta att använda. Dela din feedback och berätta för oss hur vi kan förbättra.

Du kan kontakta oss påabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar Azure Blob Storage på IoT Edge](how-to-deploy-blob.md)

Håll dig uppdaterad med senaste uppdateringar och meddelande i [Azure Blob Storage på IoT Edge blogg](https://aka.ms/abs-iot-blogpost)
