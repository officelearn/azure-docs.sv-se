---
title: AzCopy-synkronisering | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195768"
---
# <a name="azcopy-sync"></a>AzCopy-synkronisering

Replikerar käll platsen till mål platsen.

## <a name="synopsis"></a>Sammanfattning

De senast ändrade tiderna används för jämförelse. Filen hoppas över om den senaste ändrings tiden i målet är nyare.

De par som stöds är:

- lokal <-> Azure-Blob (SAS-eller OAuth-autentisering kan användas)

Kommandot Sync skiljer sig från kommandot Copy på flera sätt:

  1. Den rekursiva flaggan är aktive ras som standard.
  2. Källan och målet får inte innehålla mönster (till exempel * eller?).
  3. Flaggorna include och exclude kan vara en lista över mönster matchningar till fil namnen. Se avsnittet exempel för illustration.
  4. Om det finns filer eller blobbar på det mål som inte finns på källan, uppmanas användaren att ta bort dem.

     Den här prompten kan avvisas genom att använda motsvarande flaggor för att automatiskt besvara borttagnings frågan.

### <a name="advanced"></a>Avancerat

AzCopy identifierar automatiskt filernas innehålls typ vid överföring från den lokala disken, baserat på fil namns tillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslags tabellen är liten, men på UNIX är den förstärkt av det lokala systemets MIME. typ fil (er) om de är tillgängliga under ett eller flera av följande namn:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

I Windows extraheras MIME-typer från registret.

```azcopy
azcopy sync [flags]
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

> [!NOTE]
> om inkludera/utelämna-flaggor används tillsammans, kommer endast filer som matchar include-mönster att titta på, men de som matchar de uteslutna mönstren skulle alltid ignoreras.

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|--block-size-MB Float|Använd den här block storleken (anges i MiB) när du överför till Azure Storage eller laddar ned från Azure Storage. Standardvärdet beräknas automatiskt baserat på fil storlek. Decimal tal tillåts (till exempel: 0,25).|
|--kontrol lera-MD5-sträng|Anger hur strikta MD5-hashar ska val IDE ras vid hämtning. Det här alternativet är endast tillgängligt vid hämtning. Tillgängliga värden är: Nocheck, inloggning, FailIfDifferent, FailIfDifferentOrMissing. (standard "FailIfDifferent").|
|--Delete-destinations sträng|Anger om du vill ta bort extra filer från målet som inte finns på källan. Kan anges till true, false eller prompt. Om alternativet är inställt uppmanas användaren att ange en fråga innan de schemalägger filer och blobbar för borttagning. (standard "falskt").|
|--Exkludera sträng|Exkludera filer där namnet matchar mönster listan. Till exempel: *. jpg;* . PDF; exactName.|
|-h,--hjälp|Visa hjälp innehåll för Sync-kommandot.|
|--inkludera sträng|Inkludera endast filer där namnet matchar mönster listan. Till exempel: *. jpg;* . PDF; exactName.|
|--sträng på loggnings nivå|Definiera loggens utförlighet för logg filen, tillgängliga nivåer: INFORMATION (alla begär Anden/svar), varning (långsamma svar), fel (endast misslyckade förfrågningar) och ingen (inga utgående loggar). (standard information).|
|--Skicka-MD5|Skapa en MD5-hash av varje fil och spara hashen som Content-MD5-egenskapen för Målmatrisen eller-filen. (Som standard skapas inte hashen.) Endast tillgängligt vid uppladdning.|
|--rekursivt|Sant som standard tittar du på under kataloger rekursivt vid synkronisering mellan kataloger. (standard är sant).|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se också

- [AzCopy](storage-ref-azcopy.md)
