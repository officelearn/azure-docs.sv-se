---
title: azoskopi bort | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för kommandot azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033984"
---
# <a name="azcopy-remove"></a>azcopy ta bort

Ta bort blobbar eller filer från ett Azure-lagringskonto.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

Ta bort en enda blob med SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Ta bort en hel virtuell katalog med en SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Ta bara bort de översta blobbar i en virtuell katalog men inte dess underkataloger:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Ta bort en delmängd blobbar i en virtuell katalog (till exempel: endast jpg- och pdf-filer, eller om blobnamnet är "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Ta bort en hel virtuell katalog, men utesluter vissa blobbar från omfånget (Till exempel: varje blob som börjar med foo eller slutar med stapel):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Ta bort specifika blobbar och virtuella kataloger genom att placera deras relativa sökvägar (INTE URL-kodade) i en fil:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Ta bort en enskild fil från ett Blob Storage-konto som har ett hierarkiskt namnområde (inkludera/utesluter stöds inte).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Ta bort en enda katalog ett Blob Storage-konto som har ett hierarkiskt namnområde (inkludera/utesluter stöds inte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Alternativ

**--exclude-path sträng**      Uteslut dessa sökvägar när du tar bort. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefixet relativ sökväg. Till exempel: myFolder;myFolder/subDirName/file.pdf.

**--exclude-pattern** string Exkludera filer där namnet matchar mönsterlistan. Till exempel: *.jpg;*. pdf;exactName

**-h, --hjälp** hjälp för att ta bort

**--include-path string** Inkludera endast dessa banor när du tar bort. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefixet relativ sökväg. Till exempel: myFolder;myFolder/subDirName/file.pdf

**--include-pattern** string Inkludera endast filer där namnet matchar mönsterlistan. Till exempel: *.jpg;*. pdf;exactName

**--list-of-files string** Definierar platsen för en fil som innehåller listan över filer och kataloger som ska tas bort. De relativa sökvägarna ska avgränsas med radbrytningar och sökvägarna ska INTE URL-kodas.

**--log-nivå sträng** Definiera loggverositet för loggfilen. Tillgängliga nivåer är: INFO(alla begäranden/svar), VARNING(långsamma svar), FEL (endast misslyckade begäranden) och NONE(inga utdataloggar). (standard 'INFO') (standard "INFO")

**--rekursiv**                Titta på underkataloger rekursivt vid synkronisering mellan kataloger.

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps uint32 --cap-mbps|Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.|
|--utdata-typ sträng|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [azcopy (azcopy)](storage-ref-azcopy.md)
