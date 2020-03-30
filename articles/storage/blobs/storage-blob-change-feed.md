---
title: Ändra feed i Azure Blob Storage (förhandsversion) | Microsoft-dokument
description: Läs mer om ändringsfeedloggar i Azure Blob Storage och hur du använder dem.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536895"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Ändra feedstöd i Azure Blob Storage (förhandsversion)

Syftet med ändringsflödet är att tillhandahålla transaktionsloggar över alla ändringar som sker i blobbar och blobmetadata i ditt lagringskonto. Ändringsflödet ger **ordnad**, **garanterad,** **hållbar,** **oföränderlig,** **skrivskyddad** logg över dessa ändringar. Klientprogram kan läsa dessa loggar när som helst, antingen i direktuppspelning eller i batch-läge. Med ändringsflödet kan du skapa effektiva och skalbara lösningar som bearbetar händelser som inträffar i ditt Blob Storage-konto till en låg kostnad.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Ändringsflödet lagras som [blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) i en särskild behållare i ditt lagringskonto till [standardpriskostnad för blobpris.](https://azure.microsoft.com/pricing/details/storage/blobs/) Du kan styra lagringsperioden för dessa filer baserat på dina krav (Se [villkoren](#conditions) för den aktuella versionen). Ändringshändelser läggs till i ändringsflödet som poster i Apache Avro-formatspecifikationen: ett kompakt, snabbt binärt format som ger omfattande datastrukturer med infogat schema. [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) Det här formatet används ofta i Hadoop-ekosystemet, Stream Analytics och Azure Data Factory.

Du kan bearbeta dessa loggar asynkront, stegvis eller i sin helhet. Valfritt antal klientprogram kan självständigt läsa ändringsflödet, parallellt och i sin egen takt. Analytics-program som [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) eller [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) kan använda loggar direkt som Avro-filer, vilket gör att du kan bearbeta dem till en låg kostnad, med hög bandbredd och utan att behöva skriva ett anpassat program.

Stöd för ändringsfeed är väl lämpat för scenarier som bearbetar data baserat på objekt som har ändrats. Program kan till exempel:

  - Uppdatera ett sekundärt index, synkronisera med en cache, sökmotor eller andra scenarier för innehållshantering.
  
  - Extrahera insikter och mått för affärsanalyser, baserat på ändringar som sker i dina objekt, antingen i direktuppspelning eller batch-läge.
  
  - Lagra, granska och analysera ändringar i dina objekt under en viss tidsperiod, för säkerhet, efterlevnad eller intelligens för enterprise data management.

  - Skapa lösningar för säkerhetskopiering, spegling eller replikera objekttillstånd i ditt konto för katastrofhantering eller efterlevnad.

  - Skapa anslutna programpipelpipel som reagerar på ändringshändelser eller schemalägg körningar baserat på skapade eller ändrade objekt.

> [!NOTE]
> Ändringsfeeden ger en hållbar, ordnad loggmodell av de ändringar som sker i en blob. Ändringar skrivs och görs tillgängliga i ändringsflödesloggen inom några minuter efter ändringen. Om ditt program måste reagera på händelser mycket snabbare än så kan du använda [Blob Storage-händelser](storage-blob-event-overview.md) i stället. [Blob Storage Events](storage-blob-event-overview.md) ger engångshändelser i realtid som gör att dina Azure-funktioner eller program snabbt kan reagera på ändringar som inträffar på en blob. 

## <a name="enable-and-disable-the-change-feed"></a>Aktivera och inaktivera ändringsflödet

Du måste aktivera ändringsflödet på ditt lagringskonto för att kunna börja samla in och registrera ändringar. Inaktivera ändringsflödet för att sluta fånga ändringar. Du kan aktivera och inaktivera ändringar med hjälp av Azure Resource Manager-mallar på Portal eller Powershell.

Här är några saker att tänka på när du aktiverar ändringsflödet.

- Det finns bara en ändringsfeed för blob-tjänsten i **$blobchangefeed** varje lagringskonto och lagras i $blobchangefeed-behållaren.

- Skapa, uppdatera och ta bort ändringar fångas bara på blob-tjänstnivå.

- Ändringsflödet samlar in *alla* ändringar för alla tillgängliga händelser som inträffar på kontot. Klientprogram kan filtrera bort händelsetyper efter behov. (Se [villkoren](#conditions) för den aktuella versionen).

- Endast GPv2- och Blob-lagringskonton kan aktivera Ändra feed. Premium BlockBlobStorage-konton och hierarkiska namnområdesaktiverade konton stöds för närvarande inte. GPv1-lagringskonton stöds inte men kan uppgraderas till GPv2 utan driftstopp, se [Uppgradera till ett GPv2-lagringskonto](../common/storage-account-upgrade.md) för mer information.

> [!IMPORTANT]
> Ändringsflödet finns i den offentliga förhandsversionen och finns i **regionerna Westcentralus** och **Westus2.** Se [avsnittet villkor](#conditions) i den här artikeln. Information om hur du registrerar dig i förhandsversionen finns i avsnittet [Registrera din prenumeration](#register) i den här artikeln. Du måste registrera prenumerationen innan du kan aktivera ändringsflödet på dina lagringskonton.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aktivera ändringsflödet för ditt lagringskonto med hjälp av Azure Portal:

1. Välj ditt lagringskonto i [Azure-portalen.](https://portal.azure.com/) 

2. Navigera till alternativet **Dataskydd** under **Blob-tjänst**.

3. Klicka på Aktiverad under **Blob ändringsflöde** **Enabled**

4. Välj knappen **Spara** för att bekräfta dina dataskyddsinställningar

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aktivera ändringsflödet med PowerShell:

1. Installera den senaste PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Stäng och öppna sedan Powershell-konsolen igen.

3. Installera förhandsversionen av **Az.Storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Logga in på din `Connect-AzAccount` Azure-prenumeration med kommandot och följ anvisningarna på skärmen för att autentisera.

   ```powershell
   Connect-AzAccount
   ```

5. Aktivera ändringsflödet för ditt lagringskonto.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Mall](#tab/template)
Använd en Azure Resource Manager-mall för att aktivera Ändringsfeed på ditt befintliga lagringskonto via Azure-portalen:

1. I Azure-portalen väljer du **Skapa en resurs**.

2. I **Sök på Marketplace** skriver du **malldistribution** och trycker sedan på **RETUR**.

3. Välj **[Distribuera en anpassad mall](https://portal.azure.com/#create/Microsoft.Template)** och välj sedan Skapa en egen mall i **redigeraren**.

4. Klistra in följande json i mallredigeraren. Ersätt platshållaren `<accountName>` med namnet på ditt lagringskonto.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Välj knappen **Spara,** ange kontots resursgrupp och välj sedan knappen **Inköp** för att distribuera mallen och aktivera ändringsflödet.

---

## <a name="consume-the-change-feed"></a>Förbruka ändringsflödet

Ändringsflödet producerar flera metadata och loggfiler. Dessa filer finns i **$blobchangefeed** behållaren för lagringskontot. 

> [!NOTE]
> I den aktuella **$blobchangefeed** versionen visas inte $blobchangefeed-behållaren i Azure Storage Explorer eller Azure-portalen. Du kan för närvarande inte se $blobchangefeed behållaren när du anropar ListContainers API, men du kan anropa ListBlobs API direkt på behållaren för att se blobbar.

Dina klientprogram kan använda ändringsflödet med hjälp av blob change feed processor-biblioteket som medföljer SDK för ändringsfeedprocessorn. 

Se [Bearbeta ändringsfeedloggar i Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Förstå organisationen för ändringsflöde

<a id="segment-index"></a>

### <a name="segments"></a>Segment

Ändringsflödet är en logg med ändringar som är ordnade i **timsegment** *segments* men som läggs till och uppdateras med några minuters mellanrum. Dessa segment skapas endast när det finns blob-ändringshändelser som inträffar i den timmen. Detta gör det möjligt för klientprogrammet att använda ändringar som sker inom specifika tidsperioder utan att behöva söka igenom hela loggen. Mer information finns i [specifikationerna](#specifications).

Ett tillgängligt timsegment i ändringsflödet beskrivs i en manifestfil som anger sökvägarna till ändringsfeedfilerna för det segmentet. Listan över `$blobchangefeed/idx/segments/` den virtuella katalogen visar dessa segment som beställts efter tid. Segmentets sökväg beskriver början på det timtidsintervall som segmentet representerar. Du kan använda den listan för att filtrera bort segmenten av loggar som är intresserade av dig.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> Den `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` skapas automatiskt när du aktiverar ändringsflödet. Du kan ignorera den här filen på ett säkert sätt. Det är en alltid tom initieringsfil. 

Segmentmanifestfilen`meta.json`( ) visar sökvägen till ändringsfeedfilerna för segmentet i egenskapen. `chunkFilePaths` Här är ett exempel på en segmentmanifestfil.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Behållaren `$blobchangefeed` visas först när du har aktiverat funktionen för ändringsflödet på ditt konto. Du måste vänta några minuter efter att du har aktiverat ändringsflödet innan du kan lista blobbar i behållaren. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Ändra händelseposter

Ändringsfeedfilerna innehåller en serie ändringshändelseposter. Varje ändringshändelsepost motsvarar en ändring av en enskild blob. Posterna serialiseras och skrivs till filen med hjälp av Apache Avro-formatspecifikationen. [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) Posterna kan läsas med hjälp av Avro filformat specifikation. Det finns flera bibliotek tillgängliga för att bearbeta filer i det formatet.

Ändringsfeedfiler lagras `$blobchangefeed/log/` i den virtuella katalogen som [tilläggsblobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Den första ändringsfeedfilen under `00000` varje sökväg kommer `00000.avro`att ha i filnamnet (till exempel ). Namnet på varje efterföljande loggfil som läggs till i `00001.avro`sökvägen öka med 1 (till exempel: ).

Här är ett exempel på ändringshändelsepost från ändringsflödesfil som konverterats till Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

En beskrivning av varje egenskap finns i [Azure Event Grid-händelseschema för Blob Storage](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> Ändringsfeedfilerna för ett segment visas inte omedelbart när ett segment har skapats. Fördröjningens längd ligger inom det normala intervallet för publiceringsfördröjningen för ändringsflödet som ligger inom några minuter efter ändringen.

<a id="specifications"></a>

## <a name="specifications"></a>Specifikationer

- Ändringshändelser läggs bara till i ändringsflödet. När dessa poster har bifogats är de oföränderliga och postpositionen är stabil. Klientprogram kan behålla sin egen kontrollpunkt på ändringsfeedens läsposition.

- Ändringshändelseposter läggs till i en ordning på några minuter efter ändringen. Klientprogram kan välja att använda poster när de läggs till för direktuppspelningsåtkomst eller i grupp när som helst.

- Ändringshändelseposter ordnas efter ändringsorder **per blob**. Ordning på ändringar över blobbar är odefinierad i Azure Blob Storage. Alla förändringar i ett tidigare segment sker före eventuella förändringar i efterföljande segment.

- Ändra händelseposter serialiseras i loggfilen med hjälp av [Apache Avro 1.8.2-formatspecifikationen.](https://avro.apache.org/docs/1.8.2/spec.html)

- Ändra händelseposter `eventType` där värdet `Control` är interna systemposter och återspeglar inte en ändring av objekt i ditt konto. Du kan ignorera dessa poster på ett säkert sätt.

- Värdena `storageDiagnonstics` i egenskapspåsen är endast avsedda för internt bruk och är inte avsedda att användas av ditt program. Dina program bör inte ha ett avtalsenligt beroende av dessa data. Du kan ignorera dessa egenskaper på ett säkert sätt.

- Tiden som representeras av segmentet är **ungefärlig** med bindningar på 15 minuter. Så för att säkerställa förbrukningen av alla poster inom en viss tid, förbrukar det föregående och nästa timme segmentet i följd.

- Varje segment kan ha `chunkFilePaths` olika antal på grund av intern partitionering av loggströmmen för att hantera publiceringsdataflöde. Loggfilerna i `chunkFilePath` var och en är garanterade att innehålla ömsesidigt uteslutande blobbar och kan konsumeras och bearbetas parallellt utan att bryta mot ordningen på ändringar per blob under iterationen.

- Segmenten börjar i `Publishing` status. När lägger till posterna till segmentet är `Finalized`klar blir det . Loggfiler i alla segment som är `LastConsumable` daterade `$blobchangefeed/meta/Segments.json` efter datumet för egenskapen i filen, bör inte förbrukas av ditt program. Här är ett exempel `LastConsumable`på `$blobchangefeed/meta/Segments.json` egenskapen i en fil:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Registrera din prenumeration (Förhandsgranska)

Eftersom ändringsflödet bara är i offentlig förhandsversion måste du registrera prenumerationen för att kunna använda funktionen.

### <a name="register-by-using-powershell"></a>Registrera med PowerShell

I en PowerShell-konsol kör du följande kommandon:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Registrera dig med Hjälp av Azure CLI

Kör dessa kommandon i Azure Cloud Shell:

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Villkor och kända problem (förhandsversion)

I det här avsnittet beskrivs kända problem och villkor i den aktuella offentliga förhandsversionen av ändringsflödet. 
- För förhandsgranskning måste du först [registrera prenumerationen](#register) innan du kan aktivera ändringsflödet för ditt lagringskonto i westcentralus- eller westus2-regionerna. 
- Ändringsflödet samlar bara in åtgärder för att skapa, uppdatera, ta bort och kopiera. Metadatauppdateringar hämtas för närvarande inte i förhandsgranskningen.
- Ändra händelseposter för en enskild ändring kan visas mer än en gång i ändringsflödet.
- Du kan ännu inte hantera livstiden för ändringsflödesloggfiler genom att ange tidsbaserad bevarandeprincip på dem och du kan inte ta bort blobbar 
- Egenskapen `url` för loggfilen är alltid tom.
- Egenskapen `LastConsumable` för filen segments.json listar inte det allra första segment som ändringsflödet slutför. Det här problemet uppstår först när det första segmentet har slutförts. Alla efterföljande segment efter den första timmen `LastConsumable` fångas upp korrekt i egenskapen.
- Du kan för närvarande inte se **$blobchangefeed** behållaren när du anropar ListContainers API och behållaren visas inte på Azure Portal eller Storage Explorer
- Lagringskonton som tidigare har initierat en [kontoundans](../common/storage-disaster-recovery-guidance.md) kan ha problem med att loggfilen inte visas. Eventuella framtida kontoundansväxlingar kan också påverka loggfilen under förhandsgranskningen.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Vad är skillnaden mellan Loggning av Ändringsflöde och Lagringsanalys?
Analytics-loggar har poster för alla läs-, skriv-, list- och borttagningsåtgärder med lyckade och misslyckade begäranden i alla åtgärder. Analysloggar är bäst och ingen beställning garanteras.

Ändringsfeed är en lösning som tillhandahåller transaktionslogg över lyckade mutationer eller ändringar i ditt konto, till exempel blob-skapande, modifiering och borttagningar. Ändra feed garanterar alla händelser som ska registreras och visas i den ordning som lyckat ändringar per blob, vilket innebär att du inte behöver filtrera bort brus från en stor mängd läsåtgärder eller misslyckade begäranden. Ändringsmatningen är i grunden utformad och optimerad för applikationsutveckling som kräver vissa garantier.

### <a name="should-i-use-change-feed-or-storage-events"></a>Ska jag använda Ändra feed- eller lagringshändelser?
Du kan utnyttja båda funktionerna som Ändra feed och [Blob lagringshändelser](storage-blob-event-overview.md) ger samma information med samma leveranssäkerhetsgaranti, med den största skillnaden är svarstiden, beställningen och lagringen av händelseposter. Ändringsflödet publicerar poster i loggen inom några minuter efter ändringen och garanterar även ordningen för ändringsåtgärder per blob. Lagringshändelser skjuts i realtid och kanske inte beställs. Ändringsflödeshändelser lagras på ett sätt i ditt lagringskonto som skrivskyddade stabila loggar med din egen definierade kvarhållning, medan lagringshändelser är tillfälliga för att förbrukas av händelsehanteraren om du inte uttryckligen lagrar dem. Med Ändra feed kan valfritt antal av dina program använda loggarna på egen hand med hjälp av blob API:er eller SDK:er. 

## <a name="next-steps"></a>Nästa steg

- Se ett exempel på hur du läser ändringsflödet med hjälp av ett .NET-klientprogram. Se [Bearbeta ändringsfeedloggar i Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Läs mer om hur du reagerar på händelser i realtid. Se [Reagera på Blob Storage-händelser](storage-blob-event-overview.md)
- Läs mer om detaljerad loggningsinformation för både lyckade och misslyckade åtgärder för alla begäranden. Se [Loggning av Azure Storage-analys](../common/storage-analytics-logging.md)
