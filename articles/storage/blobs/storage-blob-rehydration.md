---
title: Rehydrera blobdata från arkivnivån
description: Rehydrera dina blobbar från arkivlagring så att du kan komma åt data.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 0a7012d9daa808933a51ac05862a8a9aa4cfcf77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614803"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Rehydrera blobdata från arkivnivån

Medan en blob finns på arkivåtkomstnivån betraktas den som offline och kan inte läsas eller ändras. Blobmetadata är fortfarande online och tillgängliga, så att du kan lista blob och dess egenskaper. Läsa och ändra blob-data är endast tillgängligt med onlinenivåer som heta eller svala. Det finns två alternativ för att hämta och komma åt data som lagras på arkivåtkomstnivån.

1. [Rehydrera en arkiverad blob till en online-nivå](#rehydrate-an-archived-blob-to-an-online-tier) - Rehydrera en arkivblob för att vara varm eller cool genom att ändra sin nivå med hjälp av åtgärden [Ange Blob-nivå.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)
2. [Kopiera en arkiverad blob till en onlinenivå](#copy-an-archived-blob-to-an-online-tier) – Skapa en ny kopia av en arkivblob med hjälp av åtgärden [Kopiera blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Ange ett annat blob-namn och en målnivå för frekvent eller cool.

 Mer information om nivåer finns i [Azure Blob storage: hot, cool och archive access tiers](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Rehydrera en arkiverad blob till en onlinenivå

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopiera en arkiverad blob till onlinenivå

Om du inte vill rehydrera arkivbloben kan du välja att utföra en [kopiera blob-åtgärd.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Den ursprungliga blobben förblir oförändrad i arkivet medan en ny blob skapas på den aktiva eller svala nivån online som du kan arbeta med. I åtgärden Kopiera blob kan du också ange egenskapen *x-ms-rehydrate-priority* till Standard eller Hög (förhandsversion) för att ange den prioritet som du vill att blob-kopian ska skapas.

Arkivblobar kan bara kopieras till målnivåer online inom samma lagringskonto. Det går inte att kopiera en arkivblob till en annan arkivblob.

Det kan ta timmar att kopiera en blob från arkivet beroende på vilken bördiga prioritet som valts. Bakom kulisserna läser **åtgärden Kopiera blob** din arkivkällblob för att skapa en ny onlineblob på den valda målnivån. Den nya blobben kan vara synlig när du listar blobbar men data är inte tillgängliga förrän läsningen från källarkivets blob är klar och data skrivs till den nya onlinemålblobben. Den nya blobben är som en oberoende kopia och alla ändringar eller borttagningar av den påverkar inte källarkivets blob.

## <a name="pricing-and-billing"></a>Priser och fakturering

Rehydrating blobbar från arkivet till heta eller kalla nivåer debiteras som läsåtgärder och datahämtning. Med hög prioritet (förhandsversion) har högre drift- och datahämtningskostnader jämfört med standardprioritet. Hög prioritet rehydrering dyker upp som en separat rad post på din faktura. Om en begäran med hög prioritet om att returnera en arkivblob på några gigabyte tar över 5 timmar debiteras du inte den högprioriterade hämtningshastigheten. Standardhämtningshastigheter gäller dock fortfarande eftersom rehydrering prioriterades framför andra begäranden.

Kopiering av blobbar från arkiv till heta eller kalla nivåer debiteras som läsåtgärder och datahämtning. En skrivåtgärd debiteras för att skapa den nya blob-kopian. Avgifter för tidig borttagning gäller inte när du kopierar till en onlineblolob eftersom källbloben förblir oförändrad på arkivnivån. Avgifter för hämtning med hög prioritet tillkommer om det här alternativet är markerat.

Blobbar i arkivnivån bör lagras i minst 180 dagar. Om du tar bort eller återfuktar arkiverade blobbar före 180 dagar kommer avgifter för tidig borttagning att tas bort.

> [!NOTE]
> Mer information om priser för blockblobar och rehydrering av data finns i [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/). Mer information om utgående dataöverföringsavgifter finns i [Informationsöverföringar Prisinformation](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Rehydrera en arkivblob till en onlinenivå
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj **Alla resurser**i Azure-portalen .

1. Välj ditt lagringskonto.

1. Markera din behållare och välj sedan din blob.

1. Välj **Ändra nivå**i **Blob-egenskaperna**.

1. Välj nivån **Hot** eller **Cool** access. 

1. Välj en rehydrerad prioritet för **standard** eller **hög**.

1. Välj **Spara** längst ned.

![Ändra lagringskontonivå](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Följande PowerShell-skript kan användas för att ändra blob-nivån för en arkivblob. Variabeln `$rgName` måste initieras med resursgruppsnamnet. Variabeln `$accountName` måste initieras med ditt lagringskontonamn. Variabeln `$containerName` måste initieras med ditt behållarnamn. Variabeln `$blobName` måste initieras med blobnamnet. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Kopiera en arkivblob till en ny blob med en onlinenivå
Följande PowerShell-skript kan användas för att kopiera en arkivblob till en ny blob i samma lagringskonto. Variabeln `$rgName` måste initieras med resursgruppsnamnet. Variabeln `$accountName` måste initieras med ditt lagringskontonamn. `$srcContainerName` Variablerna `$destContainerName` och måste initieras med behållarnamnen. `$srcBlobName` Variablerna `$destBlobName` och måste initieras med blobnamnen. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Efterföljande moment

* [Läs mer om Blob Storage-nivåer](storage-blob-storage-tiers.md)
* [Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)
* [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)
* [Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
