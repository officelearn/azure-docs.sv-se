---
title: AzCopy ta bort | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195911"
---
# <a name="azcopy-remove"></a>ta bort AzCopy

Tar bort entiteter från Azure Storage Blob, fil och Azure Data Lake Storage Gen2.

## <a name="synopsis"></a>Sammanfattning

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>Exempel

Ta bort en enskild BLOB med SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Ta bort en hel virtuell katalog med en SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Ta endast bort de översta Blobbarna i en virtuell katalog, men inte dess under kataloger:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Ta bort en delmängd av blobbar i en virtuell katalog (till exempel: endast jpg-och PDF-filer, eller om BLOB-namnet är "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Ta bort en hel virtuell katalog, men exkludera vissa blobbar från omfånget (till exempel: varje blob som börjar med foo eller slutar med bar):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Ta bort en enskild fil från Data Lake Storage Gen2 (inkludera och exkludera stöds inte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Ta bort en enskild katalog från Data Lake Storage Gen2 (inkludera och exkludera stöds inte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|--Exkludera sträng|exkludera filer där namnet matchar mönster listan. Till exempel: *. jpg;* . PDF; exactName|
|-h,--hjälp|Visa hjälp innehåll för kommandot Remove.|
|--inkludera sträng|Inkludera bara filer där namnet matchar mönster listan. Till exempel: *. jpg;* . PDF; exactName|
|--sträng på loggnings nivå|Definiera loggens utförlighet för logg filen. Tillgängliga nivåer är: INFORMATION (alla begär Anden/svar), varning (långsamma svar), fel (endast misslyckade förfrågningar) och ingen (inga utgående loggar). (standard information)|
|--rekursivt|Titta i under kataloger rekursivt vid synkronisering mellan kataloger.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se också

- [AzCopy](storage-ref-azcopy.md)
