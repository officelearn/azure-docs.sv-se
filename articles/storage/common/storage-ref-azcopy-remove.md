---
title: AzCopy ta bort | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033984"
---
# <a name="azcopy-remove"></a>azcopy ta bort

Ta bort blobbar eller filer från ett Azure Storage-konto.

## <a name="synopsis"></a>Sammanfattning

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

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

Ta bort vissa blobbar och virtuella kataloger genom att placera deras relativa sökvägar (inte URL-kodade) i en fil:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Ta bort en enskild fil från ett Blob Storage konto som har ett hierarkiskt namn område (inkludera/exkludera stöds inte).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Ta bort en enskild katalog Blob Storage konto som har ett hierarkiskt namn område (inkludera/exkludera stöds inte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Alternativ

**--exkludera-Sök vägs sträng**      Undanta de här Sök vägarna när du tar bort dem. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg. Till exempel: min mapp, mappen subDirName/File. pdf.

**--exkludera-mönster** sträng exkludera filer där namnet matchar mönster listan. Till exempel: *. jpg;* . PDF; exactName

**-h,--hjälp** för att ta bort

**--include-Path-** sträng innehåller bara dessa sökvägar när de tas bort. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg. Till exempel: min mapp, mappen subDirName/File. pdf

**--Inkludera-mönster** sträng inkludera bara filer där namnet matchar mönster listan. Till exempel: *. jpg;* . PDF; exactName

**--list-of-Files** -sträng definierar platsen för en fil som innehåller listan över filer och kataloger som ska tas bort. De relativa Sök vägarna ska avgränsas med rad brytningar och Sök vägarna får inte vara URL-kodade.

**--sträng för logg nivå** definierar loggens utförlighet för logg filen. Tillgängliga nivåer är: INFO (alla begär Anden/svar), varning (långsamma svar), fel (endast misslyckade förfrågningar) och ingen (inga utgående loggar). (standard information) (standard information)

**--rekursivt**                Titta i under kataloger rekursivt vid synkronisering mellan kataloger.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [AzCopy](storage-ref-azcopy.md)
