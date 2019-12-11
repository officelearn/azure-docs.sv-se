---
title: Ändra feed i Azure Blob Storage (förhands granskning) | Microsoft Docs
description: Lär dig mer om att ändra flödes loggar i Azure Blob Storage och hur du använder dem.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 19a65e688d66738db0b6e4dcca383c6e4abed262
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974417"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Ändra stöd för feed i Azure Blob Storage (för hands version)

Syftet med ändrings flödet är att tillhandahålla transaktions loggar för alla ändringar som sker i blobbar och blob-metadata i ditt lagrings konto. Ändrings flödet ger **beställd**, **garanterad**, **varaktig**, **oföränderlig**, **skrivskyddad** logg över dessa ändringar. Klient program kan läsa dessa loggar när som helst, antingen i strömning eller i batchläge. Med ändrings flödet kan du bygga effektiva och skalbara lösningar som bearbetar ändrings händelser som inträffar i ditt Blob Storage konto till en låg kostnad.

Ändrings flödet lagras som [blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) i en särskild behållare i ditt lagrings konto med standard [priset för BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) . Du kan styra Retentions perioden för de här filerna utifrån dina krav (se [villkoren](#conditions) i den aktuella versionen). Ändrings händelser läggs till i ändrings flödet som poster i [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) format specifikation: ett kompakt, fast binärformat som ger omfattande data strukturer med infogat schema. Det här formatet används ofta i Hadoop-ekosystemet, Stream Analytics och Azure Data Factory.

Du kan bearbeta loggarna asynkront, stegvis eller helt och hållet. Valfritt antal klient program kan samtidigt läsa ändrings flödet, parallellt och i sin egen takt. Analys program som [Apache-granskning](https://drill.apache.org/docs/querying-avro-files/) eller [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) kan använda loggar direkt som Avro-filer, vilket gör att du kan bearbeta dem till en låg kostnad, med hög bandbredd och utan att behöva skriva ett anpassat program.

Stöd för ändring av feed passar bra för scenarier som bearbetar data baserat på objekt som har ändrats. Till exempel kan program:

  - Uppdatera ett sekundärt index, synkronisera med en cache, en sökmotor eller andra scenarier för innehålls hantering.
  
  - Extrahera affärs analys insikter och mått baserat på ändringar som sker i dina objekt, antingen i ett direkt uppspelnings sätt eller i ett batch-läge.
  
  - Lagra, granska och analysera ändringar i dina objekt under en viss tids period, för säkerhet, efterlevnad eller information för företags data hantering.

  - Bygg lösningar för att säkerhetskopiera, spegla eller replikera objekt tillstånd i ditt konto för haveri hantering eller efterlevnad.

  - Skapa anslutna program pipelines som reagerar på ändrings händelser eller schema körningar baserat på skapade eller ändrade objekt.

> [!NOTE]
> Ändra feed tillhandahåller en varaktig, ordnad logg modell av de ändringar som görs i en blob. Ändringar skrivs och görs tillgängliga i din Change feed-logg inom en ordning med några minuter av ändringen. Om ditt program måste reagera på händelser mycket snabbare än så kan du överväga att använda [Blob Storage händelser](storage-blob-event-overview.md) i stället. [Blob Storage-händelser](storage-blob-event-overview.md) tillhandahåller engångs händelser i real tid som gör det möjligt för dina Azure Functions eller program att snabbt reagera på ändringar som görs i en blob. 

## <a name="enable-and-disable-the-change-feed"></a>Aktivera och inaktivera ändrings flödet

Du måste aktivera ändrings flödet på ditt lagrings konto för att kunna börja samla in och registrera ändringar. Inaktivera ändrings flödet om du vill stoppa insamlingen av ändringar. Du kan aktivera och inaktivera ändringar med Azure Resource Manager mallar på portalen eller PowerShell.

Här är några saker att tänka på när du aktiverar ändrings flödet.

- Det finns bara en ändra feed för Blob-tjänsten i varje lagrings konto och lagras i **$blobchangefeed** containern.

- Skapa, uppdatera och ta bort ändringar registreras bara på BLOB Service-nivån.

- Ändrings matningen fångar *alla* ändringar för alla tillgängliga händelser som inträffar på kontot. Klient program kan filtrera ut händelse typer efter behov. (Se [villkoren](#conditions) för den aktuella versionen).

- Endast GPv2-och Blob Storage-konton kan aktivera ändrings flöde. Premium BlockBlobStorage-konton och aktiverade konton för hierarkiskt namn område stöds inte för närvarande. GPv1 lagrings konton stöds inte, men kan uppgraderas till GPv2 utan avbrott, se [Uppgradera till ett GPv2 Storage-konto](../common/storage-account-upgrade.md) för mer information.

> [!IMPORTANT]
> Ändrings flödet finns i en offentlig för hands version och är tillgängligt i regionerna **westcentralus** och **westus2** . Se avsnittet [villkor](#conditions) i den här artikeln. Information om hur du registrerar i för hands versionen finns i avsnittet [Registrera prenumerationen](#register) i den här artikeln. Du måste registrera din prenumeration innan du kan aktivera ändra feed på dina lagrings konton.

### <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)

Aktivera ändra feed på ditt lagrings konto genom att använda Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto. 

2. Navigera till alternativet för **data skydd** under **BLOB service**.

3. Klicka på **aktive rad** under **BLOB Change feed**

4. Välj knappen **Spara** för att bekräfta dina data skydds inställningar

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Aktivera ändrings flöde med hjälp av PowerShell:

1. Installera den senaste PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Stäng och öppna sedan PowerShell-konsolen igen.

3. Installera **AZ. Storage** Preview-modulen.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Logga in på Azure-prenumerationen med den `Connect-AzAccount` och följer den på skärmen att autentisera.

   ```powershell
   Connect-AzAccount
   ```

5. Aktivera ändra feed för ditt lagrings konto.

   ```powershell
   Update-AzStorageBlobServiceProperty -ResourceGroupName -StorageAccountName -EnableChangeFeed $true
   ```

### <a name="templatetabtemplate"></a>[Mall](#tab/template)
Använd en Azure Resource Manager-mall för att aktivera ändra feed för ditt befintliga lagrings konto via Azure Portal:

1. I Azure Portal väljer du **skapa en resurs**.

2. I **Sök på Marketplace** skriver du **malldistribution** och trycker sedan på **RETUR**.

3. Välj **[distribuera en anpassad mall](https://portal.azure.com/#create/Microsoft.Template)** och välj sedan **Bygg en egen mall i redigeraren**.

4. I redigeraren för mallar klistrar du in följande JSON. Ersätt platshållaren `<accountName>` med namnet på ditt lagringskonto.

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
    
5. Välj knappen **Spara** , ange resurs grupp för kontot och välj sedan knappen **köp** för att distribuera mallen och aktivera ändrings flödet.

---

## <a name="consume-the-change-feed"></a>Använda ändrings flödet

Ändrings flödet genererar flera metadata och loggfiler. De här filerna finns i **$blobchangefeed** behållare för lagrings kontot. 

> [!NOTE]
> I den aktuella versionen visas inte **$blobchangefeed** containern i Azure Storage Explorer eller Azure Portal. Du kan för närvarande inte se $blobchangefeed-behållaren när du anropar ListContainers API, men du kan anropa ListBlobs-API: et direkt på behållaren för att se blobarna.

Dina klient program kan använda ändrings flödet med hjälp av processor biblioteket för BLOB Change-bearbetning som tillhandahålls med Change feed-SDK: n. 

Se [processen ändra flödes loggar i Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Förstå organisation för ändrings flöde

<a id="segment-index"></a>

### <a name="segments"></a>Segment

Ändrings flödet är en logg över ändringar som organiseras i **Tim** *segment* , men som läggs till och uppdateras med några minuter. Segmenten skapas endast när det finns BLOB Change-händelser som inträffar under den timmen. Detta gör att klient programmet kan använda ändringar som sker inom specifika tidsintervall utan att behöva söka igenom hela loggen. Mer information finns i [specifikationerna](#specifications).

Ett tillgängligt Tim segment i ändrings flödet beskrivs i en manifest fil som anger Sök vägarna till filerna för byte av byte för det segmentet. I listan över `$blobchangefeed/idx/segments/` virtuella katalogen visas de segment som sorteras efter tid. Segmentets sökväg beskriver starten av tids intervallet för varje timme som segmentet representerar. Du kan använda listan för att filtrera ut segmenten med loggar som är intressanta för dig.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` skapas automatiskt när du aktiverar ändrings flödet. Du kan ignorera den här filen på ett säkert sätt. Det är en initierings fil som alltid är tom. 

Segment manifest filen (`meta.json`) visar sökvägen till filerna för ändrings-feed för segmentet i `chunkFilePaths`-egenskapen. Här är ett exempel på en segment manifest fil.

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
> `$blobchangefeed` containern visas bara när du har aktiverat funktionen ändra feed på ditt konto. Du måste vänta några minuter efter att du aktiverat ändra feed innan du kan lista blobarna i behållaren. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Ändra händelse poster

Filerna för ändrings-feed innehåller en serie ändrings händelse poster. Varje ändrings händelse post motsvarar en ändring i en enskild blob. Posterna serialiseras och skrivs till filen med Avro-format specifikationen [Apache](https://avro.apache.org/docs/1.8.2/spec.html) . Posterna kan läsas med hjälp av specifikationen Avro File format. Det finns flera bibliotek som är tillgängliga för att bearbeta filer i det formatet.

Ändra feed-filer lagras i `$blobchangefeed/log/` virtuella katalogen som [bifogade blobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Den första ändrings matnings filen under varje sökväg kommer att ha `00000` i fil namnet (till exempel `00000.avro`). Namnet på varje efterföljande loggfil som läggs till i sökvägen ökar med 1 (till exempel: `00001.avro`).

Här är ett exempel på en ändrings händelse post från ändra feed-fil som konverterats till JSON.

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

En beskrivning av varje egenskap finns i [Azure Event Grid händelse schema för Blob Storage](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> Filerna för att ändra feeds för ett segment visas inte direkt efter att ett segment har skapats. Fördröjnings tiden ligger inom det normala intervallet för publicerings fördröjningen för ändrings flödet som är inom några minuter från ändringen.

<a id="specifications"></a>

## <a name="specifications"></a>Specifikationer

- Ändrings händelse poster läggs bara till i ändrings flödet. När dessa poster har lagts till är de oföränderliga och post-positionen stabil. Klient program kan upprätthålla sin egen kontroll punkt på den Läs positionen för ändrings flödet.

- Ändrings händelse poster läggs till i en ordning om några minuter av ändringen. Klient program kan välja att använda poster när de läggs till för direkt åtkomst eller i bulk vid en annan tidpunkt.

- Ändrings händelse poster sorteras efter ändrings ordning **per BLOB**. Ordningen på ändringar i blobbar är inte definierad i Azure Blob Storage. Alla ändringar i föregående segment är före eventuella ändringar i efterföljande segment.

- Ändrings händelse poster serialiseras till logg filen med hjälp av [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) format Specification.

- Ändra händelse poster där `eventType` har värdet `Control` är interna system poster och inte återspeglar en ändring av objekt i ditt konto. Du kan ignorera dessa poster på ett säkert sätt.

- Värden i `storageDiagnonstics` egenskaps uppsättningen är enbart avsedd för internt bruk och är inte avsedd att användas av ditt program. Dina program ska inte ha något avtals beroende av dessa data. Du kan ignorera dessa egenskaper på ett säkert sätt.

- Tiden som segmentet representerar är **Ungefärlig** med gränser på 15 minuter. För att se till att förbrukningen av alla poster inom en angiven tid används, förbrukar du föregående och nästa Tim segment.

- Varje segment kan ha olika antal `chunkFilePaths` på grund av intern partitionering av logg strömmen för att hantera publicerings data flödet. Loggfilerna i varje `chunkFilePath` garanterar att de innehåller ömsesidigt uteslutande blobbar och kan förbrukas och bearbetas parallellt utan att det bryter mot sortering av ändringar per BLOB under iterationen.

- Segmenten börjar `Publishing` status. När tillägget av posterna i segmentet har slutförts kommer det att `Finalized`. Loggfiler i alla segment som är daterade efter datumet för egenskapen `LastConsumable` i `$blobchangefeed/meta/Segments.json`-filen, bör inte användas av ditt program. Här är ett exempel på egenskapen `LastConsumable`i en `$blobchangefeed/meta/Segments.json`-fil:

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

## <a name="register-your-subscription-preview"></a>Registrera din prenumeration (för hands version)

Eftersom ändrings flödet endast finns i en offentlig för hands version måste du registrera din prenumeration för att använda funktionen.

### <a name="register-by-using-powershell"></a>Registrera med PowerShell

Kör följande kommandon i en PowerShell-konsol:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Registrera med Azure CLI

Kör följande kommandon i Azure Cloud Shell:

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Villkor och kända problem (förhands granskning)

I det här avsnittet beskrivs kända problem och villkor i den aktuella offentliga för hands versionen av ändrings flödet. 
- För för hands versionen måste du först [Registrera din prenumeration](#register) innan du kan aktivera ändra feed för ditt lagrings konto i westcentralus-eller westus2-regionerna. 
- För avbildningar av ändrings flöden skapas endast åtgärder för att skapa, uppdatera, ta bort och kopiera. Metadata-uppdateringar registreras för närvarande inte i för hands versionen.
- Ändrings händelse poster för en enskild ändring kan visas mer än en gång i din ändrings feed.
- Du kan ännu inte hantera livs längden för loggfiler för ändrings flöden genom att ange en tidsbaserad bevarande princip för dem och du kan inte ta bort Blobbarna 
- Logg filens `url` egenskap är för närvarande tom.
- Egenskapen `LastConsumable` för segment. JSON-filen listar inte det allra första segmentet som ändrings flödet Slutför. Det här problemet uppstår först när det första segmentet har slutförts. Alla efterföljande segment efter den första timmen registreras korrekt i `LastConsumable`-egenskapen.
- Du kan för närvarande inte se **$blobchangefeed** -behållaren när du anropar ListContainers API och behållaren inte visas på Azure Portal eller Storage Explorer
- Lagrings konton som tidigare har initierat en [konto redundansväxling](../common/storage-disaster-recovery-guidance.md) kan ha problem med logg filen som inte visas. Eventuella framtida fel i kontot kan också påverka logg filen under för hands versionen.

## <a name="faq"></a>FAQ

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Vad är skillnaden mellan ändrings flöde och Lagringsanalys loggning?
Analys loggar innehåller poster med alla Läs-, Skriv-, list-och borttagnings åtgärder med lyckade och misslyckade förfrågningar för alla åtgärder. Analys loggar är bästa möjliga och ingen beställning är garanterat.

Change feed är en lösning som ger transaktions logg över lyckade mutationer eller ändringar av ditt konto, till exempel skapande av BLOB, ändring och borttagning. Ändra feed garanterar att alla händelser registreras och visas i ordningen för lyckade ändringar per BLOB, så du behöver inte filtrera bort brus från en enorm volym Läs åtgärder eller misslyckade förfrågningar. Ändrings flödet är grundläggande utformat och optimerat för program utveckling som kräver vissa garantier.

### <a name="should-i-use-change-feed-or-storage-events"></a>Ska jag använda ändra feed eller lagrings händelser?
Du kan använda båda funktionerna som ändrings flöde och [Blob Storage-händelser](storage-blob-event-overview.md) ger samma information med samma leverans Tillförlitlighets garanti, och den största skillnaden är svars tid, beställning och lagring av händelse poster. Ändra feed publicerar poster till loggen inom några minuter efter ändringen och garanterar även ordningen för ändrings åtgärder per blob. Lagrings händelser överförs i real tid och kanske inte beställs. Ändra flödes händelser lagras varaktigt i ditt lagrings konto som skrivskyddade, stabila loggar med en egen definierad kvarhållning, medan lagrings händelser är tillfälliga att konsumeras av händelse hanteraren, om du inte uttryckligen lagrar dem. Med ändrings flöden kan valfritt antal program använda loggarna i sin egen bekvämlighet med hjälp av BLOB-API: er eller SDK: er. 

## <a name="next-steps"></a>Nästa steg

- Se ett exempel på hur du läser ändrings flödet med hjälp av ett .NET-klient program. Se [processen ändra flödes loggar i Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Lär dig mer om hur du reagerar på händelser i real tid. Se [reagera på att Blob Storage händelser](storage-blob-event-overview.md)
- Läs mer om detaljerad loggnings information för både lyckade och misslyckade åtgärder för alla begär Anden. Se [Azure Storage Analytics-loggning](../common/storage-analytics-logging.md)
