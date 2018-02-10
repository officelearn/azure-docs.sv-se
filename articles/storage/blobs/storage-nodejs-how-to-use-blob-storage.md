---
title: "Använda Blob storage från Node.js | Microsoft Docs"
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8b0df222-1ca8-4967-8248-6d6d720947b8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 52f1f1543fe0ef15cf71d2cf1f9a8bfeaae8933f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-use-blob-storage-from-nodejs"></a>Använda Blob Storage från Node.js
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Översikt
Den här artikeln visar hur du utför vanliga scenarier med Blob storage. Exemplen är skrivna via API för Node.js. Scenarier som tas upp är hur du överför, lista, hämta och ta bort blobbar.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program
Instruktioner om hur du skapar en Node.js-program finns i [skapa en Node.js-webbapp i Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [skapa och distribuera ett Node.js-program till en Azure-molntjänst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) --med Windows PowerShell eller [skapa och distribuera en Node.js-webbapp till Azure med hjälp av Web Matrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program åtkomst till lagring
Om du vill använda Azure storage, behöver du Azure Storage SDK: N för Node.js som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med storage REST-tjänster.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Använd noden Package Manager (NPM) för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac), eller **Bash** (Unix) att navigera till mappen där du skapade exempelprogrammet.
2. Typen **npm installera azure-lagring** i kommandofönstret. Utdata från kommandot liknar följande exempel.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. Du kan köra manuellt på **ls** kommando för att kontrollera att en **nod\_moduler** mappen har skapats. Inuti den mappen hitta den **azure storage** paket som innehåller de bibliotek som du behöver åtkomst till lagring.

### <a name="import-the-package"></a>Importera paketet
I anteckningar eller något annat textredigeringsprogram, Lägg till följande överst i den **server.js** filen för programmet som du tänker använda lagring:

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Skapa en Azure Storage-anslutning
Azure-modulen läses miljövariablerna `AZURE_STORAGE_ACCOUNT` och `AZURE_STORAGE_ACCESS_KEY`, eller `AZURE_STORAGE_CONNECTION_STRING`, information som krävs för att ansluta till Azure storage-konto. Om de här miljövariablerna inte har angetts måste du ange kontoinformationen vid anrop av **createBlobService**.

## <a name="create-a-container"></a>Skapa en behållare
Den **BlobService** objekt kan du arbeta med behållare och blobbar. Följande kod skapar en **BlobService** objekt. Lägg till följande längst upp i **server.js**:

```nodejs
var blobSvc = azure.createBlobService();
```

> [!NOTE]
> Du kan komma åt en blob anonymt med **createBlobServiceAnonymous** och ge värdadressen. Till exempel använda `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Använd för att skapa en ny behållare **createContainerIfNotExists**. Följande exempel skapar en ny behållare med namnet 'minbehållare':

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
    if(!error){
      // Container exists and is private
    }
});
```

Om behållaren nyligen har skapat `result.created` är true. Om behållaren redan `result.created` är false. `response`innehåller information om åtgärden, inklusive ETag-information för behållaren.

### <a name="container-security"></a>Behållarsäkerhet
Som standard nya behållare är privata och går inte att ansluta anonymt. Om du vill att behållaren offentliga så att du kan komma åt den anonymt, du kan ange behållarens åtkomst till **blob** eller **behållare**.

* **BLOB** -tillåter anonym läsbehörighet till blob-innehåll och metadata i den här behållaren, men inte i behållaren metadata, till exempel visar en lista över alla blobbar i en behållare
* **behållaren** -tillåter anonym läsbehörighet till blob-innehåll och metadata som metadata för behållaren

Följande kodexempel visar ställa in till **blob**:

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
    if(!error){
      // Container exists and allows
      // anonymous read access to blob
      // content and metadata within this container
    }
});
```

Du kan också ändra åtkomstnivån för en behållare med hjälp av **setContainerAcl** Ange åtkomstnivå. Följande kodexempel ändrar åtkomstnivå till behållare:

```nodejs
blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
  if(!error){
    // Container access level set to 'container'
  }
});
```

Innehåller information om operationen, inklusive aktuellt **ETag** för behållaren.

### <a name="filters"></a>Filter
Du kan använda valfri filtrering åtgärder till åtgärder som utförs med hjälp av **BlobService**. Filtrering operations kan innehålla loggning, försöker automatiskt igen och så vidare. Filtren är objekt som implementerar en metod med signaturen:

```nodejs
function handle (requestOptions, next)
```

När du har gjort dess förbearbetning på begäran alternativ måste metoden anropa ”next”, skicka ett återanrop med följande signatur:

```nodejs
function (returnObject, finalCallback, next)
```

I den här motringning och efter bearbetning returnObject (svar från begäran till servern), måste återanropet anropa sedan om den finns för att fortsätta att bearbeta filter eller helt enkelt anropa finalCallback för att avsluta tjänsten-anrop.

Två filter som implementerar logik som medföljer Azure SDK för Node.js, **ExponentialRetryPolicyFilter** och **LinearRetryPolicyFilter**. Följande kod skapar en **BlobService** objekt som använder den **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var blobSvc = azure.createBlobService().withFilter(retryOperations);
```

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blobb till en behållare
Det finns tre typer av blobbar: blockblobbar, sidblobbar och tilläggsblobbar. Blockblobbar kan du effektivt överför stora mängder data. Lägg till blobbar som är optimerade för tilläggsåtgärder. Sidblobbar är optimerade för läs-/ skrivåtgärder. Mer information finns i [förstå Blockblobbar, Tilläggsblobbar och Sidblobbar](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Blockblob-objekt
Om du vill överföra data till en blockblobb, använder du följande:

* **createBlockBlobFromLocalFile** – skapar en ny block-blob och överför innehållet i en fil
* **createBlockBlobFromStream** – skapar en ny block-blob och överför innehållet i en dataström
* **createBlockBlobFromText** – skapar en ny block-blob och överför innehållet i en sträng
* **createWriteStreamToBlockBlob** -ger en dataström med skrivning till en blockblobb

Följande kodexempel Överför innehållet i den **test.txt** filen till **minblobb**.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

Den `result` returneras av dessa metoder innehåller information om åtgärden som den **ETag** på blob.

### <a name="append-blobs"></a>Tilläggsblobbar
Om du vill överföra data till en ny tilläggsblobb, använder du följande:

* **createAppendBlobFromLocalFile** – skapar en ny tilläggsblobb och överför innehållet i en fil
* **createAppendBlobFromStream** – skapar en ny tilläggsblobb och överför innehållet i en dataström
* **createAppendBlobFromText** – skapar en ny tilläggsblobb och överför innehållet i en sträng
* **createWriteStreamToNewAppendBlob** – skapar en ny tilläggsblobb och ger en dataström med att skriva till den

Följande kodexempel Överför innehållet i den **test.txt** filen till **myappendblob**.

```nodejs
blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

Om du vill lägga till ett block till en befintlig tilläggsblobb, använder du följande:

* **appendFromLocalFile** -Lägg till innehållet i en fil till en befintlig tilläggsblobb
* **appendFromStream** -Lägg till innehållet i en dataström till en befintlig tilläggsblobb
* **appendFromText** -Lägg till innehållet i en sträng i en befintlig tilläggsblobb
* **appendBlockFromStream** -Lägg till innehållet i en dataström till en befintlig tilläggsblobb
* **appendBlockFromText** -Lägg till innehållet i en sträng i en befintlig tilläggsblobb

> [!NOTE]
> appendFromXXX API: er kommer att göra vissa klientsidan verifiering för att snabbt kunna undvika onödiga serveranrop. appendBlockFromXXX won't.
>
>

Följande kodexempel Överför innehållet i den **test.txt** filen till **myappendblob**.

```nodejs
blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
  if(!error){
    // text appended
  }
});
```

### <a name="page-blobs"></a>Sidblobbar
Om du vill överföra data till en sidblob, använder du följande:

* **createPageBlob** -skapar en ny sidblob för en viss längd
* **createPageBlobFromLocalFile** – skapar en ny sidblob och överför innehållet i en fil
* **createPageBlobFromStream** – skapar en ny sidblob och överför innehållet i en dataström
* **createWriteStreamToExistingPageBlob** -ger en dataström med skrivning till en befintlig sidblob
* **createWriteStreamToNewPageBlob** – skapar en ny sidblob och ger en dataström med att skriva till den

Följande kodexempel Överför innehållet i den **test.txt** filen till **mypageblob**.

```nodejs
blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

> [!NOTE]
> Sidblobbar består av 512 byte sidor. Du får ett fel när överföra data med en storlek som inte är en multipel av 512.
>
>

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare
Om du vill visa blobbar i en behållare använder den **listBlobsSegmented** metod. Om du vill returnera blobbar med ett specifikt prefix använda **listBlobsSegmentedWithPrefix**.

```nodejs
blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
  if(!error){
      // result.entries contains the entries
      // If not all blobs were returned, result.continuationToken has the continuation token.
  }
});
```

Den `result` innehåller en `entries` samling, som är en matris med objekt som beskriver varje blob. Om alla BLOB inte kan returneras den `result` innehåller också en `continuationToken`, som kan användas som den andra parametern för att hämta ytterligare poster.

## <a name="download-blobs"></a>Ladda ned blobbar
Om du vill hämta data från en blob, använder du följande:

* **getBlobToLocalFile** -skriver blobbinnehållet till filen
* **getBlobToStream** -skriver blobbinnehållet till en dataström
* **getBlobToText** -skriver blobbinnehållet till en sträng
* **createReadStream** -ger en dataström med att läsa från blob

I följande kodexempel visas hur du använder **getBlobToStream** att hämta innehållet i den **minblobb** blob och lagra den till den **output.txt** filen med hjälp av en dataström:

```nodejs
var fs = require('fs');
blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
  if(!error){
    // blob retrieved
  }
});
```

Den `result` innehåller information om blob, inklusive **ETag** information.

## <a name="delete-a-blob"></a>Ta bort en blob
Slutligen, om du vill ta bort en blobb anropa **deleteBlob**. Följande exempel tar bort blob med namnet **minblobb**.

```nodejs
blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
  if(!error){
    // Blob has been deleted
  }
});
```

## <a name="concurrent-access"></a>Samtidig åtkomst
Du kan använda för att stödja samtidig åtkomst till en blobb från flera klienter eller flera processinstanser, **ETags** eller **lån**.

* **ETag** -ger ett sätt att identifiera att blob eller behållare har ändrats av en annan process
* **Lånet** -ger ett sätt att få exklusiv, förnyas, skriva eller ta bort åtkomst till en blob för en viss tidsperiod

### <a name="etag"></a>ETag
Använd ETags om du vill tillåta flera klienter eller instanser att skriva till blocket Blob eller sidan Blob samtidigt. ETag kan du bestämma om behållare eller blobb har ändrats sedan du först läsa eller skapat, där du kan undvika att skriva över ändringarna av en annan klient eller process.

Du kan ange ETag villkor med hjälp av den valfria `options.accessConditions` parameter. I följande kod exempel endast överföringar av **test.txt** -filen om blob finns redan och ETag-värde som innehåller `etagToMatch`.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
    if(!error){
    // file uploaded
  }
});
```

När du använder ETags är allmänna mönster:

1. Hämta ETag som ett resultat av att skapa, listor eller hämta-åtgärden.
2. Utföra en åtgärd som kontrollerar ETag-värdet inte har ändrats.

Om värdet har ändrats, betyder det att en annan klient eller instans ändrats blob eller behållaren eftersom du har köpt ETag-värde.

### <a name="lease"></a>Lån
Du kan skaffa ett nytt lån med hjälp av den **acquireLease** -metoden anger blob eller behållare som du vill få ett lån på. Till exempel följande kod får ett lån på **minblobb**.

```nodejs
blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
  if(!error) {
    console.log('leaseId: ' + result.id);
  }
});
```

Efterföljande åtgärder på **minblobb** måste ange den `options.leaseId` parameter. Lånet ID returneras som `result.id` från **acquireLease**.

> [!NOTE]
> Som standard är oändlig lånetid. Du kan ange en icke oändliga varaktigheten (mellan 15 och 60 sekunder) genom att tillhandahålla den `options.leaseDuration` parameter.
>
>

Ta bort ett lån med **releaseLease**. Om du vill dela upp ett lån, men förhindra andra från att erhålla ett nytt lån till den ursprungliga varaktigheten har gått ut, Använd **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Arbeta med signaturer för delad åtkomst
Signaturer för delad åtkomst (SAS) är ett säkert sätt att tillhandahålla detaljerade åtkomst till blobbar och behållare utan att erbjuda dina lagringskontonamn eller nycklar. Signaturer för delad åtkomst används ofta för att ge begränsad åtkomst till dina data, till exempel att tillåta en mobil app åtkomst till blobbar.

> [!NOTE]
> Medan du kan också tillåta anonym åtkomst till blobbar, kan du ange mer kontrollerad åtkomst, som måste du generera SAS signaturer för delad åtkomst.
>
>

En betrodda program, till exempel en molnbaserad tjänst genererar signaturer för delad åtkomst med hjälp av den **generateSharedAccessSignature** av den **BlobService**, och ger den till ett program som inte är betrodd eller delvis betrodd, till exempel en mobil app. Delad åtkomst signaturer genereras med hjälp av en princip som beskriver start- och slutdatum då signaturer för delad åtkomst är giltiga, samt åtkomstnivån som beviljas åtkomst till delad åtkomst signaturer innehavaren.

Följande exempel skapar en ny princip för delad åtkomst som tillåter signaturer för delad åtkomst innehavaren utföra läsåtgärder på den **minblobb** blob och upphör att gälla 100 minuter efter den tidpunkt som den har skapats.

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
};

var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
var host = blobSvc.host;
```

Observera att information om värden måste anges, eftersom det är nödvändigt när delad åtkomst signaturer innehavaren försöker få åtkomst till behållaren.

Klientprogrammet sedan använder signaturer för delad åtkomst med **BlobServiceWithSAS** att utföra åtgärder mot blob. Följande hämtar information **minblobb**.

```nodejs
var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
  if(!error) {
    // retrieved info
  }
});
```

Eftersom signaturer för delad åtkomst har skapats med skrivskyddad åtkomst om ett försök görs att ändra blob, returneras ett fel.

### <a name="access-control-lists"></a>Listor för åtkomstkontroll
Du kan också använda en åtkomstkontrollista (ACL) för att ange åtkomstprincipen för SAS. Detta är användbart om du vill att flera klienter kan komma åt en behållare men ange olika principer för varje klient.

En ACL implementeras med hjälp av en matris med principer för åtkomst med ett ID som är associerade med varje princip. Följande exempel definierar två principer, en för 'Användare1' och en för 'användare2':

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Följande exempel hämtar den aktuella ACL för **minbehållare**, och sedan lägger till nya principer med hjälp av **setBlobAcl**. Den här metoden kan:

```nodejs
var extend = require('extend');
blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Du kan sedan skapa signaturer för delad åtkomst baserat på en princip-ID när Åtkomstkontrollistan har angetts. Följande exempel skapar nya signaturer för delad åtkomst för 'användare2':

```nodejs
blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser.

* [Azure Storage SDK för noden API-referens] [Azure Storage SDK för noden API-referens]  
* [Azure Storage-teamets blogg] [Azure Storage-teamets blogg]  
* [Azure Storage SDK: N för noden] [ Azure Storage SDK for Node] databasen på GitHub  
* [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/)  
* [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)  

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node  

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/  
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx  
[Azure portal]: https://portal.azure.com  
[Skapa och distribuera ett Node.js-program till en Azure-molntjänst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)  
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/  
[Azure Storage SDK för noden API-referens]: http://dl.windowsazure.com/nodestoragedocs/index.html  
