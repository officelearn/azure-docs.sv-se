---
title: azoskopi synkronisering | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för synkroniseringskommandot för akoposkopi.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1bff46c8584934ab8bcffce74763edc8363533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988251"
---
# <a name="azcopy-sync"></a>azcopy synkronisering

Replikerar källplatsen till målplatsen.

## <a name="synopsis"></a>Synopsis

De senaste ändrade tiderna används för jämförelse. Filen hoppas över om den senast ändrade tiden i målet är nyare.

Paren som stöds är:

- lokal <-> Azure Blob (antingen SAS- eller OAuth-autentisering kan användas)
- Azure Blob <-> Azure Blob (Källan måste innehålla en SAS eller är allmänt tillgänglig; antingen SAS- eller OAuth-autentisering kan användas för mål)
- Azure File <-> Azure File (Källan måste innehålla en SAS eller är allmänt tillgänglig; SAS-autentisering ska användas för destination)

Synkroniseringskommandot skiljer sig från kopieringskommandot på flera sätt:

1. Som standard är den rekursiva flaggan sann och synkroniserar alla underkataloger. Synkroniseringen kopierar bara filerna på den översta nivån i en katalog om den rekursiva flaggan är falsk.
2. När du synkroniserar mellan virtuella kataloger lägger du till ett avslutande snedstreck i sökvägen (referera till exempel) om det finns en blob med samma namn som en av de virtuella katalogerna.
3. Om flaggan DeleteDestination är inställd på true eller prompt tas filer och blobbar bort vid målet som inte finns vid källan.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avancerat

Om du inte anger något filnamnstillägg identifierar AzCopy automatiskt filernas innehållstyp när du laddar upp från den lokala disken, baserat på filtillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslagstabellen är liten, men på Unix utökas den med det lokala systemets mime.types-filer om det är tillgängligt under ett eller flera av dessa namn:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

I Windows extraheras MIME-typer från registret.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Exempel

Synkronisera en enda fil:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Samma som ovan, men den här gången, också beräkna MD5 hash av filinnehållet och spara den som blob's Content-MD5 egenskap:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synkronisera en hel katalog inklusive dess underkataloger (observera att rekursiv är aktiverat som standard):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

eller

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synkronisera bara de översta filerna i en katalog men inte dess underkataloger:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synkronisera en delmängd av filer i en katalog (till exempel: endast jpg- och pdf-filer, eller om filnamnet är "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Synkronisera en hel katalog, men utesluter vissa filer från omfånget (Till exempel: varje fil som börjar med foo eller slutar med stapel):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Synkronisera en enda blob:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synkronisera en virtuell katalog:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synkronisera en virtuell katalog med samma namn som en blob (lägg till ett avslutande snedstreck i sökvägen för att skiljas):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synkronisera en Azure-filkatalog (samma syntax som Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Om inkludera/exkluderar flaggor används tillsammans, skulle endast filer som matchar inkludera mönster ses över, men de som matchar undantagsmönstren ignoreras alltid.

## <a name="options"></a>Alternativ

**--block-size-mb** float Använd den här blockstorleken (angiven i MiB) när du laddar upp till Azure Storage eller hämtar från Azure Storage. Standard beräknas automatiskt baserat på filstorlek. Decimaltal är tillåtna (till exempel: 0,25).

**--check-md5** sträng Anger hur strikt MD5 hashar ska valideras vid nedladdning. Det här alternativet är bara tillgängligt när du hämtar. Tillgängliga värden är: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (standard 'FailIfDifferent'). (standard "FailIfDifferent")

**--delete-destination** string Definierar om du vill ta bort extra filer från målet som inte finns i källan. Kan ställas in på sant, falskt eller snabbt. Om den är inställd på att fråga, kommer användaren att få en fråga innan du schemalägger filer och blobbar för borttagning. (standard "falskt"). (standard "falskt")

**--exclude-attributes** string (endast Windows) Uteslut filer vars attribut matchar attributlistan. Till exempel: A; S; R

**--exclude-path string** Exkludera dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar relativa sökvägsprefix(Till exempel: myFolder;myFolder/subDirName/file.pdf). När det används i kombination med konto traversal, sökvägar inte innehåller behållarnamnet.

**--exclude-pattern** string Exkludera filer där namnet matchar mönsterlistan. Till exempel: \*.jpg; \*.pdf;exactName

**-h, --hjälp** hjälp för synkronisering

**--include-attributes** string (endast Windows) Inkludera endast filer vars attribut matchar attributlistan. Till exempel: A; S; R

**--include-pattern** string Inkludera endast filer där namnet matchar mönsterlistan. Till exempel: \*.jpg; \*.pdf;exactName

**--log-level** string Definiera loggvergialiteten för loggfilen, tillgängliga nivåer: INFO(alla begäranden och svar), VARNING(långsamma svar), FEL (endast misslyckade begäranden) och NONE(inga utdataloggar). (standardinfo). (standard "INFO")

**--put-md5**                     Skapa en MD5-hash för varje fil och spara hash-värdet som egenskapen Content-MD5 för målbloben eller -filen. (Som standard skapas INTE hash-värdet.) Endast tillgängligt vid uppladdning.

**--rekursiv**                   True som standard, titta på underkataloger rekursivt när du synkroniserar mellan kataloger. (standard sant). (standard sant)

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps uint32 --cap-mbps|Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.|
|--utdata-typ sträng|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [azcopy (azcopy)](storage-ref-azcopy.md)
