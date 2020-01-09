---
title: AzCopy-synkronisering | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 6ecba85a859e902922dfa2b7563a3ceb96a9ef4d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457452"
---
# <a name="azcopy-sync"></a>azcopy synkronisering

Replikerar käll platsen till mål platsen.

## <a name="synopsis"></a>Sammanfattning

De senast ändrade tiderna används för jämförelse. Filen hoppas över om den senaste ändrings tiden i målet är nyare.

De par som stöds är:

- lokal <-> Azure-Blob (SAS-eller OAuth-autentisering kan användas)
- Azure Blob <-> Azure-Blob (källan måste innehålla en SAS eller är offentligt tillgänglig, antingen SAS-eller OAuth-autentisering kan användas för mål)
- Azure File <-> Azure-fil (källan måste innehålla en SAS eller är offentligt tillgänglig. SAS-autentisering ska användas för mål)

Kommandot Sync skiljer sig från kommandot Copy på flera sätt:

1. Som standard är den rekursiva flaggan True och Sync kopierar alla under kataloger. Sync kopierar endast filer på den översta nivån i en katalog om den rekursiva flaggan är falsk.
2. När du synkroniserar mellan virtuella kataloger lägger du till ett avslutande snedstreck till sökvägen (se exemplen) om det finns en blob med samma namn som en av de virtuella katalogerna.
3. Om flaggan ' deleteDestination ' har angetts till true eller prompt, kommer synkronisering att ta bort filer och blobbar på det mål som inte finns på källan.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Advanced

Om du inte anger något fil namns tillägg identifierar AzCopy automatiskt filernas innehålls typ vid överföring från den lokala disken, baserat på fil namns tillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslags tabellen är liten, men på UNIX är den förstärkt av det lokala systemets MIME. typ fil (er) om de är tillgängliga under ett eller flera av följande namn:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

I Windows extraheras MIME-typer från registret.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Exempel

Synkronisera en enskild fil:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Samma som ovan, men den här gången kan du också beräkna MD5-hashen för fil innehållet och spara den som blobens Content-MD5-egenskap:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synkronisera en hel katalog inklusive dess under kataloger (Observera att rekursivt är aktiverat som standard):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

eller

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synkronisera enbart de översta filerna i en katalog, men inte dess under kataloger:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synkronisera en delmängd av filer i en katalog (till exempel: endast jpg-och PDF-filer, eller om fil namnet är "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Synkronisera en hel katalog, men undanta vissa filer från omfånget (till exempel: varje fil som börjar med foo eller slutar med bar):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Synkronisera en enskild BLOB:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synkronisera en virtuell katalog:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synkronisera en virtuell katalog som har samma namn som en BLOB (Lägg till ett avslutande snedstreck till sökvägen i ordningen till disambiguate):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synkronisera en Azure-fil katalog (samma syntax som BLOB):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Om inkludera/utelämna-flaggor används tillsammans, kommer endast filer som matchar include-mönster att titta på, men de som matchar de uteslutna mönstren skulle alltid ignoreras.

## <a name="options"></a>Alternativ

**--block-size-MB** float Använd den här block storleken (anges i MIB) vid överföring till Azure Storage eller nedladdning från Azure Storage. Standardvärdet beräknas automatiskt baserat på fil storlek. Decimal tal tillåts (till exempel: 0,25).

**--kontrol lera-MD5-** sträng anger hur strikt MD5-hashar ska verifieras vid hämtning. Det här alternativet är endast tillgängligt vid hämtning. Tillgängliga värden är: nocheck, inloggning, FailIfDifferent, FailIfDifferentOrMissing. (standard ' FailIfDifferent '). (standard "FailIfDifferent")

**--Delete-destinations** sträng anger om du vill ta bort extra filer från målet som inte finns på källan. Kan anges till true, false eller prompt. Om alternativet är inställt uppmanas användaren att ange en fråga innan de schemalägger filer och blobbar för borttagning. (standard är falskt). (standard "falskt")

**--sträng för exkludera attribut** (endast Windows) Uteslut filer vars attribut matchar attributlistan. Till exempel: A; Na R

**--sträng för exkluderings Sök väg** undantar dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg (till exempel: min mapp, mappen subDirName/File. pdf). När de används i kombination med konto Traversal inkluderar inte sökvägar namnet på behållaren.

**--exkludera-mönster** sträng exkludera filer där namnet matchar mönster listan. Till exempel: *. jpg;* . PDF; exactName

**-h,--hjälp** hjälp för synkronisering

**--include-attribut** sträng (endast Windows) inkludera endast filer vars attribut stämmer överens med attributlistan. Till exempel: A; Na R

**--Inkludera-mönster** sträng inkludera bara filer där namnet matchar mönster listan. Till exempel: *. jpg;* . PDF; exactName

**--sträng på loggnivå** definierar loggens utförlighet för logg filen, tillgängliga nivåer: info (alla begär Anden och svar), varning (långsamma svar), fel (endast misslyckade förfrågningar) och ingen (inga utgående loggar). (standard information). (standard information)

**--Skicka-MD5**                     Skapa en MD5-hash av varje fil och spara hashen som Content-MD5-egenskapen för Målmatrisen eller-filen. (Som standard skapas inte hashen.) Endast tillgängligt vid uppladdning.

**--rekursivt**                   Sant som standard tittar du på under kataloger rekursivt vid synkronisering mellan kataloger. (standard är sant). (standard sant)

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se också

- [AzCopy](storage-ref-azcopy.md)
