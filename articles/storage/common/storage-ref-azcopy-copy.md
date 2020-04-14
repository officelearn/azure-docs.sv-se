---
title: azoskopikopia| Microsoft-dokument
description: Den här artikeln innehåller referensinformation för kommandot askakopkopiering.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0325a71fb069f3d96f05d106afac1639fc38fe42
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253347"
---
# <a name="azcopy-copy"></a>azcopy kopiera

Kopierar källdata till en målplats.

## <a name="synopsis"></a>Synopsis

Kopierar källdata till en målplats. Anvisningarna som stöds är:

  - lokal <-> Azure Blob (SAS- eller OAuth-autentisering)
  - lokala <-> Azure-filer (SAS-autentisering för delning/katalog)
  - lokal <-> ADLS Gen 2 (SAS-, OAuth- eller SharedKey-autentisering)
  - Azure Blob (SAS eller offentlig) -> Azure Blob (SAS- eller OAuth-autentisering)
  - Azure Blob (SAS eller offentlig) -> Azure Files (SAS)
  - Azure-filer (SAS) -> Azure-filer (SAS)
  - Azure Files (SAS) -> Azure Blob (SAS- eller OAuth-autentisering)
  - AWS S3 (Access Key) -> Azure Block Blob (SAS- eller OAuth-autentisering)

Se exemplen för mer information.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avancerat

AzCopy identifierar automatiskt innehållstypen för filerna när du laddar upp från den lokala disken, baserat på filtillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslagstabellen är liten, men på Unix utökas den av det lokala systemets mime.types-filer om det är tillgängligt under ett eller flera av dessa namn:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

I Windows extraheras MIME-typer från registret. Den här funktionen kan stängas av med hjälp av en flagga. Se flaggavsnittet.

Om du anger en miljövariabel med hjälp av kommandoraden kan variabeln läsas i kommandoradshistoriken. Överväg att rensa variabler som innehåller autentiseringsuppgifter från kommandoradshistoriken. Om du vill förhindra att variabler visas i historiken kan du använda ett skript för att fråga användaren om deras autentiseringsuppgifter och ange miljövariabeln.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exempel

Ladda upp en enda fil med OAuth-autentisering. Om du ännu inte har loggat in på AzCopy kör du inloggningskommandot för azkopia innan du kör följande kommando.

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

Samma som ovan, men den här gången också beräkna MD5 hash av filinnehållet och spara den som blob's Content-MD5 egenskap:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5

Ladda upp en enda fil med hjälp av en SAS-token:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Ladda upp en enda fil med hjälp av en SAS-token och rörledningar (endast blockblobar):
  
- katt "/sökväg/till/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Ladda upp en hel katalog med hjälp av en SAS-token:
  
- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekursiv=true

eller

- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekursiv=true --put-md5

Ladda upp en uppsättning filer med hjälp av en SAS-token och jokertecken (*) tecken:

- azcopy cp "/path/*foo/* bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]"

Ladda upp filer och kataloger med hjälp av en SAS-token och jokertecken (*) tecken:

- azcopy cp "/path/*foo/* bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekursiv=true

Ladda ned en enda fil med OAuth-autentisering. Om du ännu inte har loggat in på AzCopy kör du inloggningskommandot för azkopia innan du kör följande kommando.

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"

Ladda ned en enda fil med hjälp av en SAS-token:

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "/sökväg/till/file.txt"

Hämta en enda fil med hjälp av en SAS-token och sedan leda utdata till en fil (endast blockblobar):
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" > "/sökväg/till/file.txt"

Hämta en hel katalog med hjälp av en SAS-token:
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "/path/to/dir" --rekursiv=true

En anteckning om hur du använder ett jokertecken (*) i webbadresser:

Det finns bara två sätt att använda ett jokertecken i en URL. 

- Du kan använda en strax efter det sista snedstrecket (/) för en webbadress. Detta kopierar alla filer i en katalog direkt till målet utan att placera dem i en underkatalog.

- Du kan också använda en i namnet på en behållare så länge url:en endast refererar till en behållare och inte till en blob. Du kan använda den här metoden för att hämta filer från en delmängd av behållare.

Hämta innehållet i en katalog utan att kopiera själva den innehållande katalogen.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*? [SAS]" "/sökväg/till/dir"

Ladda ner ett helt lagringskonto.

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive

Hämta en delmängd av behållare i ett lagringskonto med hjälp av en jokerteckensymbol (*) i behållarnamnet.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive

Kopiera en enda blob till en annan blob med hjälp av en SAS-token.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Kopiera en enda blob till en annan blob med hjälp av en SAS-token och en OAuth-token. Du måste använda en SAS-token i slutet av källkontots URL, men målkontot behöver inte en om du loggar in på AzCopy med kommandot för akoposkopiinloggning. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"

Kopiera en virtuell blob-katalog till en annan med hjälp av en SAS-token:

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekursiv=true

Kopiera alla blob-behållare, kataloger och blobbar från lagringskontot till ett annat med hjälp av en SAS-token:

- azcopy cp "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" --rekursiv=true

Kopiera ett enda objekt till Blob Storage från Amazon Web Services (AWS) S3 med hjälp av en åtkomstnyckel och en SAS-token. Ange först miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.
  
- azcopy cphttps://s3.amazonaws.com/" [bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Kopiera en hel katalog till Blob Storage från AWS S3 med hjälp av en åtkomstnyckel och en SAS-token. Ange först miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.

- azcopy cphttps://s3.amazonaws.com/" [bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --rekursiv=true

Se till https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html att bättre förstå [mapp] platshållaren.

Kopiera alla buckets till Blob Storage från Amazon Web Services (AWS) med hjälp av en åtkomstnyckel och en SAS-token. Ange först miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.

- azcopy cphttps://s3.amazonaws.com/" " "https://[destaccount].blob.core.windows.net? [SAS]" --rekursiv=true

Kopiera alla buckets till Blob Storage från en AWS-region (Amazon Web Services) med hjälp av en åtkomstnyckel och en SAS-token. Ange först miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.

- azcopy cphttps://s3-" [region].amazonaws.com/" "https://[destaccount].blob.core.windows.net? [SAS]" --rekursiv=true

Kopiera en delmängd av buckets med hjälp av en jokerteckensymbol (*) i bucket-namnet. Precis som de tidigare exemplen behöver du en åtkomstnyckel och en SAS-token. Se till att ställa in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.

- azcopy cphttps://s3.amazonaws.com/" [bucket*name]/" "https://[destaccount].blob.core.windows.net? [SAS]" --rekursiv=true

## <a name="options"></a>Alternativ

**--backup**                               Aktiverar Windows SeBackupPrivilege för uppladdningar, eller SeRestorePrivilege för nedladdningar, så att AzCopy kan se läsa alla filer, oavsett deras filsystembehörigheter, och för att återställa alla behörigheter. Kräver att kontot som kör AzCopy redan har dessa behörigheter (t.ex. har administratörsrättigheter eller är medlem i gruppen "Säkerhetskopieringsoperatörer"). Allt den här flaggan gör är att aktivera privilegier som kontot redan har.

**--blob-typ** sträng Definierar typen av blob vid målet. Detta används för att ladda upp blobbar och vid kopiering mellan konton (standard "Identifiera"). Giltiga värden är "Detect", "BlockBlob", "PageBlob" och "AppendBlob". Vid kopiering mellan konton, ett värde av "Identifiera" orsakar AzCopy att använda den typ av källa blob för att bestämma vilken typ av mål blob. När du laddar upp en fil avgör "Detect" om filen är en VIRTUELLD eller en VHDX-fil baserat på filtillägget. Om filen är eter en VHD eller VHDX-fil, azcopy behandlar filen som en sida blob. (standard "Identifiera")

**--block-blob-tier** string Upload block blobbar direkt till [den åtkomstnivå](../blobs/storage-blob-storage-tiers.md) som du väljer. (standard "Ingen"). Giltiga värden är "Ingen", "Hot", "Cool" och "Arkiv". Om "Ingen" eller ingen nivå skickas, kommer bloben att ärva nivån för lagringskontot.

**--block-size-mb** float Använd den här blockstorleken (angiven i MiB) när du laddar upp till Azure Storage och hämtar från Azure Storage. Standardvärdet beräknas automatiskt baserat på filstorlek. Decimaltal är tillåtna (till exempel: 0,25).

**--cache-kontrollsträng** Ange cachekontrollhuvudet. Returneras vid nedladdning.

**--check-längd**                         Kontrollera längden på en fil på målet efter överföringen. Om det finns en obalans mellan källa och mål markeras överföringen som misslyckad. (standard sant)

**--check-md5** sträng Anger hur strikt MD5 hashar ska valideras vid nedladdning. Endast tillgängligt när du laddar ned. Tillgängliga alternativ: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (standard "FailIfDifferent")

**--content-disposition** sträng Ange innehåll-disposition header. Returneras vid nedladdning.

**--content-encoding** string Ange innehållskodningshuvudet. Returneras vid nedladdning.

**--content-language** string Ange innehållsspråksrubriken. Returneras vid nedladdning.

**--content-type** string Anger filens innehållstyp. Antyder ingen gissning-mim-typ. Returneras vid nedladdning.

**--expandera**                           Komprimera filer automatiskt när de hämtas, om deras innehållskodning indikerar att de är komprimerade. De innehållskodningsvärden som stöds är "gzip" och "deflate". Filtillägg av ".gz"/'.gzip' eller '.zz' är inte nödvändiga, men kommer att tas bort om sådan finns.

**--exclude-attributes** string (endast Windows) Uteslut filer vars attribut matchar attributlistan. Till exempel: A; S; R

**--exclude-blob-type** string Anger eventuellt vilken typ av blob (BlockBlob/ PageBlob/ AppendBlob) som ska uteslutas när blobbar kopieras från behållaren eller kontot. Användning av den här flaggan gäller inte för kopiering av data från icke azure-service till service. Mer än en blob bör avgränsas med ".".

**--exclude-path string** Exkludera dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar relativa sökvägsprefix(Till exempel: myFolder;myFolder/subDirName/file.pdf). När det används i kombination med konto traversal, sökvägar inte innehåller behållarnamnet.

**--exclude-pattern string** Exkludera dessa filer vid kopiering. Det här alternativet stöder jokertecken (*)

**--följ-symlinks**                      Följ symboliska länkar när du laddar upp från det lokala filsystemet.

**--from-to sträng** anger eventuellt källmålskombinationen. Till exempel: LocalBlob, BlobLocal, LocalBlobFS.

**-h, --hjälp** hjälp för kopiering

**--include-attributes** string (endast Windows) Inkludera filer vars attribut matchar attributlistan. Till exempel: A; S; R

**--include-path string** Inkludera endast dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar relativa sökvägsprefix (till exempel: myFolder;myFolder/subDirName/file.pdf).

**--include-pattern** string Inkludera endast dessa filer vid kopiering. Det här alternativet stöder jokertecken (*). Separata filer med hjälp av ett ";".

**--log-level** string Definiera loggvergialiteten för loggfilen, tillgängliga nivåer: INFO(alla begäranden/svar), VARNING(långsamma svar), FEL (endast misslyckade begäranden) och NONE(inga utdataloggar). (standard "INFO")

**--metadata** sträng Ladda upp till Azure Storage med dessa nyckel-värde par som metadata.

**--no-guess-mime-typ**                   Förhindrar att AzCopy identifierar innehållstypen baserat på filens tillägg eller innehåll.

**--skriv över** strängen Skriv över de filer och blobbar som står i konflikt vid målet om flaggan är inställd på true. Möjliga värden inkluderar "true", "false", "ifSourceNewer" och "prompt". (standard "sant")

**--page-blob-tier** string Upload page blob to Azure Storage using this blob tier. (standard "Ingen")

**--bevara-senast ändrade tid**          Endast tillgängligt när målet är filsystem.

**--preserve-smb-behörighetssträngen** Falskt som standard. Bevarar SMB-ACL:er mellan medvetna resurser (Windows och Azure-filer). För nedladdningar måste du också `--backup` använda flaggan för att återställa behörigheter där den nya ägaren inte kommer att vara den användare som kör AzCopy. Den här flaggan gäller både filer och mappar, såvida inte ett filfilter anges (t.ex. `include-pattern`

**--preserve-smb-info strängEn** Falskt som standard. Bevarar SMB-egenskapsinformation (senaste skrivtid, skapandetid, attributbitar) mellan SMB-medvetna resurser (Windows och Azure-filer). Endast attributbitarna som stöds av Azure Files överförs. alla andra kommer att ignoreras. Den här flaggan gäller både filer och mappar, såvida inte ett filfilter anges (t.ex. include-pattern). Informationen som överförs för mappar är densamma som för filer, med undantag för Senaste skrivtid som aldrig bevaras för mappar.

**--bevara-ägare**                       Har bara en effekt i när data `--preserve-smb-permissions` hämtas, och endast när den används. Om värdet är sant (standard) bevaras filens ägare och grupp i hämtningar. Om den här flaggan `--preserve-smb-permissions` är inställd på false, kommer fortfarande att bevara ACL men ägare och grupp kommer att baseras på användaren som kör AzCopy.

**--put-md5**                             Skapa en MD5-hash för varje fil och spara hash-värdet som egenskapen Content-MD5 för målbloben eller -filen. (Som standard skapas INTE hash-värdet.) Endast tillgängligt vid uppladdning.

**--rekursiv**                            Titta på underkataloger rekursivt när du laddar upp från lokala filsystem.

**--s2s-upptäcka-källa-förändrats**           Kontrollera om källan har ändrats efter uppräkning.

**--s2s-handle-invalid-metadata** sträng Anger hur ogiltiga metadatanycklar hanteras. Tillgängliga alternativ: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (standard "ExcludeIfInvalid")

**--s2s-bevara-åtkomst-nivå**             Bevara åtkomstnivån under service till servicekopia. Se [Azure Blob storage: hot, cool och archive access tiers](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) för att säkerställa att mållagringskontot stöder inställningsåtkomstnivå. I de fall som inställningsåtkomstnivå inte stöds använder du s2sPreserveAccessTier=false för att kringgå kopieringsåtkomstnivån. (standard sant)

**--s2s-bevara-egenskaper**              Bevara fullständiga egenskaper under service till servicekopian. För AWS S3 och Azure File icke-en filkälla returnerar liståtgärden inte fullständiga egenskaper för objekt och filer. För att bevara fullständiga egenskaper måste AzCopy skicka ytterligare en begäran per objekt eller fil. (standard sant)

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

**--cap-mbps uint32 --cap-mbps**      Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.

**--utdata-typ** sträng Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text". (standard "text")

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
