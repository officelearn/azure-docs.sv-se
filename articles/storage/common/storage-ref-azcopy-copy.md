---
title: AzCopy-kopia | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195742"
---
# <a name="azcopy-copy"></a>AzCopy kopia

Kopierar käll data till en målplats.

## <a name="synopsis"></a>Sammanfattning

De vägvisningar som stöds är:

- lokal <-> Azure-Blob (SAS eller OAuth-autentisering)
- lokal <-> Azure-fil (autentisering med resurs/katalog-SAS)
- lokal <-> ADLS gen 2 (SAS, OAuth eller SharedKey-autentisering)
- Azure Blob (SAS eller offentlig) <-> Azure Blob (SAS eller OAuth-autentisering)
- Azure-fil (SAS) – > Azure Block Blob (SAS eller OAuth-autentisering)
- AWS S3 (åtkomst nyckel) – > Azure Block Blob (SAS eller OAuth-autentisering)

Mer information finns i exemplen.

### <a name="advanced"></a>Avancerat

AzCopy identifierar automatiskt filernas innehålls typ vid överföring från den lokala disken, baserat på fil namns tillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslags tabellen är liten, men på UNIX utökas den av det lokala systemets MIME. typ fil (er) om de är tillgängliga under ett eller flera av följande namn:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

I Windows extraheras MIME-typer från registret. Den här funktionen kan inaktive ras med hjälp av en flagga. Läs avsnittet flagga.

> [!IMPORTANT]
> Om du anger en miljö variabel med hjälp av kommando raden, kommer den variabeln att läsas i kommando rads historiken. Överväg att ta bort variabler som innehåller autentiseringsuppgifter från din kommando rads historik. Om du vill att variablerna ska visas i historiken kan du använda ett skript för att uppmana användaren att ange sina autentiseringsuppgifter och ange miljövariabeln.

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exempel

Ladda upp en enstaka fil med OAuth-autentisering.

Om du ännu inte har loggat in på AzCopy bör `azcopy login` du använda kommandot innan du kör följande kommando.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Samma som ovan, men den här gången beräknar även MD5-hashen av fil innehållet och sparar den som blobens Content-MD5-egenskap:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Ladda upp en enstaka fil med en SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Ladda upp en enstaka fil med en SAS med hjälp av rör (endast block-blobbar):

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Ladda upp en hel katalog med en SAS:

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

eller

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

Ladda upp en uppsättning filer med en SAS med jokertecken:

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Ladda upp filer och kataloger med en SAS med jokertecken:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Hämta en enskild fil med OAuth-autentisering.

Om du ännu inte har loggat in på AzCopy bör `azcopy login` du använda kommandot innan du kör följande kommando.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Hämta en enda fil med en SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Hämta en enskild fil med en SAS med hjälp av rör (endast block-blobbar):

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

Ladda ned en hel katalog med en SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

Hämta en uppsättning filer med en SAS med jokertecken:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

Ladda ned filer och kataloger med en SAS med jokertecken:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

Kopiera en enda BLOB med SAS till en annan BLOB med SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopiera en enda BLOB med SAS till en annan BLOB med OAuth-token.

Om du ännu inte har loggat in på AzCopy bör `azcopy login` du använda kommandot innan du kör följande kommando. OAuth-token används för att komma åt mål lagrings kontot.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Kopiera en hel katalog från virtuell BLOB-katalog med SAS till en annan virtuell BLOB-katalog med SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopiera en hel konto data från ett BLOB-konto med SAS till ett annat BLOB-konto med SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Kopiera ett enskilt objekt från S3 med åtkomst nyckel till blob med SAS:

Ställ in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för S3-källan.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopiera en hel katalog från S3 med åtkomst nyckeln till BLOB-virtuell katalog med SAS:

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

I https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html lär du dig mer om vad [Folder] innebär för S3. Ställ in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för S3-källan.

Kopiera alla buckets i S3-tjänsten med åtkomst nyckeln till BLOB-kontot med SAS:

Ställ in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för S3-källan.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Kopiera alla buckets i en S3-region med åtkomst nyckel till BLOB-konto med SAS:

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Ställ in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för S3-källan.

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|--Blob-typ sträng|Definierar typ av BLOB vid målet. Detta används för att ladda upp blobbar och när du kopierar mellan konton (standard ingen).|
|--Block-Blob-Tier sträng|Ladda upp block-blob till Azure Storage med denna BLOB-nivå. (standard "ingen").|
|--block-size-MB Float |Använd den här block storleken (anges i MiB) vid överföring till Azure Storage och nedladdning från Azure Storage. Standardvärdet beräknas automatiskt baserat på fil storlek. Decimal tal tillåts (till exempel: 0,25).|
|--Cache-Control-sträng|Ange Cache-Control-huvudet. Returnerades vid nedladdning.|
|--kontrol lera-MD5-sträng|Anger hur strikta MD5-hashar ska val IDE ras vid hämtning. Endast tillgängligt vid hämtning. Tillgängliga alternativ: Nocheck, inloggning, FailIfDifferent, FailIfDifferentOrMissing. (standard "FailIfDifferent")|
|--innehålls-dispositions sträng|Ange huvudet för innehålls disposition. Returnerades vid nedladdning.|
|--innehålls kodnings sträng|Ange innehålls kodnings rubriken. Returnerades vid nedladdning.|
|--innehålls språk sträng|Ange rubrik för innehålls språk. Returnerades vid nedladdning.|
|--innehålls typ sträng |Anger filens innehålls typ. Innebär No-gissning-MIME-type. Returnerades vid nedladdning.|
|--Exkludera sträng|Exkludera dessa filer vid kopiering. Stöd för användning av *.|
|--Exkludera-Blob-typ sträng|Anger alternativt vilken typ av BLOB (BlockBlob/PageBlob/AppendBlob) som ska undantas när blobbar kopieras från behållaren eller kontot. Den här flaggan kan inte användas för att kopiera data från icke-Azure-tjänst till tjänst. Mer än en BLOB ska avgränsas med;.|
|--Följ-symlinks|Följ symboliska länkar när du laddar upp från lokalt fil system.|
|--från-till-sträng|Anger om käll målet ska kombineras. Till exempel: LocalBlob, BlobLocal, LocalBlobFS.|
|-h,--hjälp|Visar hjälp innehåll för kopierings kommandot. |
|--sträng på loggnings nivå|Definiera loggens utförlighet för logg filen, tillgängliga nivåer: INFORMATION (alla begär Anden/svar), varning (långsamma svar), fel (endast misslyckade förfrågningar) och ingen (inga utgående loggar). (standard information).|
|– metadata-sträng|Ladda upp till Azure Storage med dessa nyckel/värde-par som metadata.|
|--No-gissning-MIME-type|Förhindrar AzCopy från att identifiera innehålls typ baserat på fil namns tillägget eller innehållet i filen.|
|--Skriv över|Skriv över de motstridiga filerna/Blobbarna på målet om den här flaggan har angetts till true. (standard är sant).|
|--sid-BLOB-nivå sträng |Ladda upp sid-blob till Azure Storage med den här BLOB-nivån. (standard "ingen").|
|--bevara-senaste ändrings tid|Endast tillgängligt om målet är fil system.|
|--Skicka-MD5|skapa en MD5-hash av varje fil och spara hashen som Content-MD5-egenskapen för Målmatrisen eller-filen. (Som standard skapas inte hashen.) Endast tillgängligt vid uppladdning.|
|--rekursivt|Titta i under kataloger rekursivt vid överföring från lokalt fil system.|
|--S2S-detect-källa-ändrad|Kontrol lera om källan har ändrats efter uppräkningen. För S2S-kopiering, som källa är en fjär resurs, verifierar om källan har ändrats behöver ytterligare kostnader för begäran.|
|--S2S-handle-ogiltig-metadata-sträng |Anger hur ogiltiga nycklar för nycklar hanteras. Tillgängliga alternativ: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (standard "ExcludeIfInvalid").|
|--S2S-konserver-Access-Tier|Bevara åtkomst nivån under tjänst-till-tjänst-kopiering. Se [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../blobs/storage-blob-storage-tiers.md) lag rings nivåer för att säkerställa att mål lagrings kontot har stöd för att ange åtkomst nivå. I de fall då det inte finns stöd för att ange åtkomst nivå kan du använda s2sPreserveAccessTier = false för att kringgå kopiering av åtkomst nivå.  (standard är sant).|
|--S2S-bevara-egenskaper|Behåll fullständiga egenskaper under tjänst-till-tjänst-kopiering. För S3-och Azure-filer som inte är en enskild fil källa, returnerar inte den här List åtgärden fullständiga egenskaper för objekt och filer, för att bevara fullständiga egenskaper AzCopy måste skicka ytterligare en begäran per objekt och fil. (standard är sant).|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se också

- [AzCopy](storage-ref-azcopy.md)
