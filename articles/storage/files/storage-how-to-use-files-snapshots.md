---
title: "Arbeta med ögonblicksbilder för resursen (förhandsversion) | Microsoft Docs"
description: "En ögonblicksbild av en resurs är en skrivskyddad version av en resurs i Azure-filer som hämtas vid en tidpunkt som ett sätt att säkerhetskopiera resursen."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: c4a5f7d28601867c383b8b348568e4bb580a81eb
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="work-with-share-snapshots-preview"></a>Arbeta med ögonblicksbilder för resursen (förhandsgranskning)
En ögonblicksbild av en resurs (förhandsversion) är en skrivskyddad version av en resurs i Azure-filer som hämtas vid en tidpunkt. När en ögonblicksbild av en resurs har skapats kan kan den läsa, kopieras, eller tas bort, men inte har ändrats. En ögonblicksbild av en resurs kan du säkerhetskopiera resursen som det visas vid en tidpunkt. 

I den här artikeln lär du dig att skapa, hantera och ta bort ögonblicksbilder för resursen. Mer information finns i [dela ögonblicksbild översikt](storage-snapshots-files.md) eller [ögonblicksbild vanliga frågor och svar](storage-files-faq.md).

## <a name="create-a-share-snapshot"></a>Skapa en ögonblicksbild av en resurs

Du kan skapa en ögonblicksbild av en resurs med hjälp av Azure portal, PowerShell, Azure CLI, REST-API eller någon Azure Storage SDK: N. I följande avsnitt beskrivs hur du skapar en ögonblicksbild av en resurs med hjälp av portalen, CLI och PowerShell. 

Du kan ta en ögonblicksbild av en resurs på en filresurs när den används. Dela ögonblicksbilder avbilda dock endast data som har redan skrivits till en filresurs vid den tidpunkt då dela ögonblicksbild kommandot har utfärdats. Detta kan utesluta alla data som har cachelagrats genom alla program eller av operativsystemet.

### <a name="create-a-share-snapshot-by-using-the-portal"></a>Skapa en ögonblicksbild av en resurs med hjälp av portalen  
För att skapa en ögonblicksbild av en tidpunkt i resursen, gå till din fil delar i portalen och väljer **skapa en ögonblicksbild**.

>   ![Snapshot-menyn i portalen](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>Skapa en ögonblicksbild av en resurs med hjälp av Azure CLI 2.0
Du kan skapa en ögonblicksbild av en resurs med hjälp av den `az storage share snapshot` kommando:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Exempel på utdata:
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-a-share-snapshot-by-using-powershell"></a>Skapa en ögonblicksbild av en resurs med hjälp av PowerShell
Du kan skapa en ögonblicksbild av en resurs med hjälp av den `$share.Snapshot()` kommando:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>Utföra vanliga åtgärder för ögonblicksbild av resursen

Du kan räkna upp resursen ögonblicksbilderna som är kopplade till din filresurs med hjälp av den **tidigare versioner** fliken i Windows via REST, klientbiblioteket, PowerShell och portalen. När filresursen är monterad, du kan visa alla tidigare versioner av filen med hjälp av den **tidigare versioner** fliken i Windows. 

I följande avsnitt beskrivs hur du använder Azure-portalen, Windows och Azure CLI 2.0 för att, bläddra till och återställa resursen ögonblicksbilder.

### <a name="share-snapshot-operations-in-the-portal"></a>Dela ögonblicksbild åtgärder i portalen

Du kan titta på alla dina resurs ögonblicksbilder för en fil delar i portalen och bläddra till en resurs ögonblicksbild för att visa dess innehåll.

#### <a name="view-a-share-snapshot"></a>Visa en ögonblicksbild av en resurs
På din filresurs under **ögonblicksbild**väljer **Visa ögonblicksbilder**.

![”Visa ögonblicksbilder” kommandot i portalen](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>Lista och Bläddra för att dela ögonblicksbild innehåll
Visa en lista över resursen ögonblicksbilder och bläddra sedan till innehållet i en ögonblicksbild direkt genom att välja önskad tidsstämpeln.

![Valda ögonblicksbild i listan över tidsstämplar](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

Du kan också välja den **Anslut** knappen i listan ögonblicksbild vyn för att hämta den `net use` kommandot och sökvägen till en viss resurs-ögonblicksbild. Du kan sedan bläddra till den ögonblicksbilden direkt.


![Ansluta rutan med kommandot](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>Hämta eller återställa från en ögonblicksbild av en resurs
Ladda ned från portalen, eller återställa en fil från en ögonblicksbild med hjälp av den **hämta** eller **återställa** knappen respektive.

![Hämta och Återställ knappar](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Dela ögonblicksbild åtgärder i Windows
När du har redan vidtagit resursen ögonblicksbilder av din filresurs, kan du visa tidigare versioner av en resurs, en katalog eller en viss fil från monterade filresursen på Windows. T.ex är här hur du kan använda funktionen tidigare versioner för att visa och återställa en tidigare version av en katalog i Windows.

> [!Note]  
> Du kan utföra samma åtgärder på resursen nivå och filnivå. Endast den version som innehåller ändringar för den katalog eller fil visas i listan. Om en viss katalog eller fil inte har ändrats mellan två resursen ögonblicksbilder visas dela ögonblicksbild i listan resursnivå tidigare version, men inte i katalogen eller filen tidigare versionslista.

#### <a name="mount-a-file-share"></a>Montera en filresurs
Först montera filresursen med hjälp av den `net use` kommando.

#### <a name="open-a-mounted-share-in-file-explorer"></a>Öppna en monterade resursen i Utforskaren
Gå till Utforskaren och Sök efter den monterade resursen.

![Monterade resursen i Utforskaren](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Lista över tidigare versioner
Bläddra till artikeln eller överordnade som ska återställas. Dubbelklicka om du vill gå till den önskade katalogen. Högerklicka och välj **egenskaper** på menyn.

![Högerklicka på menyn för den valda katalogen](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Välj **tidigare versioner** att se en lista över resursen ögonblicksbilder för den här katalogen. Listan kan ta några sekunder att läsa in beroende på nätverkets hastighet och antalet ögonblicksbilder för resursen i katalogen.

![Fliken tidigare versioner](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Du kan välja **öppna** att öppna en viss ögonblicksbild. 

![Öppna ögonblicksbild](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Återställa från en tidigare version
Välj **återställa** att kopiera innehållet i hela katalogen rekursivt vid tiden för skapandet av resursen ögonblicksbild till den ursprungliga platsen.
 ![Återställa knapp i varningsmeddelandet](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Dela ögonblicksbild åtgärder i Azure CLI 2.0
Du kan använda Azure CLI 2.0 för att utföra åtgärder som att lista resursen ögonblicksbilder webbläsare om du vill dela ögonblicksbild innehåll, återställa eller hämta filer från resursen ögonblicksbilder eller ta bort delar ögonblicksbilder.

#### <a name="list-share-snapshots"></a>Lista resursen ögonblicksbilder

Du kan visa en lista med ögonblicksbilder av en viss resurs med hjälp av [ `az storage share list` ](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) med `--include-snapshots`:

```azurecli-interactive 
az storage share list --include-snapshots
```

Kommandot visas en lista över resursen ögonblicksbilder, tillsammans med alla associerade egenskaper. Följande utdata är ett exempel:

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-to-a-share-snapshot"></a>Bläddra till en ögonblicksbild av en resurs
Du kan bläddra till en viss resurs ögonblicksbild och visa dess innehåll med hjälp av [ `az storage file list` ](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Ange resursnamnet och tidsstämpeln som du vill bläddra till, enligt följande exempel:

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

Du ser att innehållet i resursen ögonblicksbilden är identisk med innehållet i utdata, resursen vid den tidpunkt som resursen ögonblicksbild skapades:

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-a-share-snapshot"></a>Återställa från en ögonblicksbild av en resurs

Du kan återställa en fil genom att kopiera eller ladda ned den från dela ögonblicksbild. Använd den `az storage file download` kommandot som visas i följande exempel:

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

Utdata och ser du att innehållet i den hämta filen och dess egenskaper är identiska till innehåll och egenskaper vid den tidpunkt som delar ögonblicksbild skapades:

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

<<<<<<< HEAD
### <a name="file-share-snapshot-operations-in-azure-powershell"></a>Filresurs ögonblicksbild åtgärder i Azure PowerShell
Du kan använda Azure Powershell för att utföra samma åtgärder som lista resursen ögonblicksbilder surfning dela ögonblicksbild innehåll, återställa eller hämta filer från resursen ögonblicksbild eller ta bort ögonblicksbilder för resursen.

#### <a name="list-share-snapshots"></a>Lista resursen ögonblicksbilder

Du kan visa resursen ögonblicksbilder av en viss filresurs med hjälp av`Get-AzureStorageShare`

```powershell
Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
```

#### <a name="browse-share-snapshots"></a>Bläddra resursen ögonblicksbilder
Du kan även bläddra i en viss resurs ögonblicksbild för att visa dess innehåll med hjälp av `Get-AzureStorageFile` med värdet för `-Share` pekar till viss ögonblicksbild

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
Get-AzureStorageFile -Share $snapshot
```

#### <a name="restore-from-share-snapshots"></a>Återställa från resursen ögonblicksbilder

Du kan återställa en fil genom att kopiera eller ladda ned en fil från resursen med verktyget `Get-AzureStorageFileContent` kommando

```powershell
$download='C:\Temp\Download'
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $download
```

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
$directory = Get-AzureStorageFile -ShareName "ContosoShare06" -Path "ContosoWorkingFolder" | Get-AzureStorageFile
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $directory
```


## <a name="delete-azure-files-share-snapshot"></a>Ta bort Azure-filer dela ögonblicksbild
=======
## <a name="delete-a-share-snapshot"></a>Ta bort en ögonblicksbild av en resurs
>>>>>>> 6a1833e10031fbf1ab204bb1f30cb54cf5fbcada

Du kan ta bort resursen ögonblicksbilder med hjälp av Azure portal, PowerShell, CLI, REST-API eller alla Storage SDK: N. I följande avsnitt beskrivs hur du tar bort filresursen ögonblicksbilder med hjälp av Azure-portalen, CLI och PowerShell.

Du kan bläddra om du vill dela ögonblicksbilder och visa skillnader mellan två ögonblicksbilder med hjälp av ett verktyg för jämförelse. Sedan kan du bestämma vilken resurs ögonblicksbild som du vill ta bort. 

Du kan inte ta bort en resurs som har en resurs ögonblicksbild. För att kunna ta bort resursen, måste du först ta bort alla resursen ögonblicksbilder.

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>Ta bort en ögonblicksbild av en resurs med hjälp av portalen  
I portalen kan du gå till din filresursen bladet och använda den **ta bort** för att ta bort en eller flera ögonblicksbilder för resursen.

>   ![Knappen Ta bort](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>Ta bort en ögonblicksbild av en resurs med hjälp av Azure CLI 2.0
Du kan ta bort en ögonblicksbild av en resurs med hjälp av den `[az storage share delete]` kommando. Använda resursen snapshot-tidsstämpel för den `--snapshot '2017-10-04T23:28:35.0000000Z' ` parameter i följande exempel:

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Exempel på utdata:
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>Ta bort en ögonblicksbild av en resurs med hjälp av PowerShell
Du kan ta bort en ögonblicksbild av en resurs med hjälp av den `Remove-AzureStorageShare -Share` kommando:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Nästa steg
* [Ögonblicksbilder: översikt](storage-snapshots-files.md)
* [Ögonblicksbild vanliga frågor och svar](storage-files-faq.md)
