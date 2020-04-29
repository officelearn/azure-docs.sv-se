---
title: AzCopy-kopia | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0325a71fb069f3d96f05d106afac1639fc38fe42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253347"
---
# <a name="azcopy-copy"></a>azcopy kopiera

Kopierar käll data till en målplats.

## <a name="synopsis"></a>Sammanfattning

Kopierar käll data till en målplats. De vägvisningar som stöds är:

  - lokal <-> Azure-Blob (SAS eller OAuth-autentisering)
  - lokal <-> Azure Files (autentisering med ASSOCIATIONer för delade/kataloger)
  - lokal <-> ADLS gen 2 (SAS, OAuth eller SharedKey-autentisering)
  - Azure Blob (SAS eller offentlig) – > Azure Blob (SAS eller OAuth-autentisering)
  - Azure Blob (SAS eller offentlig) – > Azure Files (SAS)
  - Azure Files (SAS) – > Azure Files (SAS)
  - Azure Files (SAS) – > Azure Blob (SAS eller OAuth-autentisering)
  - AWS S3 (åtkomst nyckel) – > Azure Block Blob (SAS eller OAuth-autentisering)

Mer information finns i exemplen.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avancerat

AzCopy identifierar automatiskt filernas innehålls typ vid överföring från den lokala disken, baserat på fil namns tillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslags tabellen är liten, men på UNIX utökas den av det lokala systemets MIME. typ fil (er) om de är tillgängliga under ett eller flera av följande namn:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

I Windows extraheras MIME-typer från registret. Den här funktionen kan inaktive ras med hjälp av en flagga. Läs avsnittet flagga.

Om du anger en miljö variabel med hjälp av kommando raden, kommer den variabeln att läsas i kommando rads historiken. Överväg att ta bort variabler som innehåller autentiseringsuppgifter från din kommando rads historik. Om du vill att variablerna ska visas i historiken kan du använda ett skript för att uppmana användaren att ange sina autentiseringsuppgifter och ange miljövariabeln.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exempel

Överför en enda fil med OAuth-autentisering. Om du ännu inte har loggat in på AzCopy kör du kommandot AzCopy login innan du kör följande kommando.

- AzCopy CP "/path/to/File.txt" "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]"

Samma som ovan, men den här gången beräknar även MD5-hashen av fil innehållet och sparar den som blobens Content-MD5-egenskap:

- AzCopy CP "/path/to/File.txt" "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]--skicka-MD5

Ladda upp en enstaka fil med hjälp av en SAS-token:

- AzCopy CP "/path/to/File.txt" "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]? [SAS] "

Ladda upp en enstaka fil med hjälp av en SAS-token och-rör (endast block-blobbar):
  
- katt "/path/to/File.txt" | AzCopy CP "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]? [SAS] "

Ladda upp en hel katalog med hjälp av en SAS-token:
  
- AzCopy CP "/path/to/dir" "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/katalog]? [SAS] "--recursive = True

eller

- AzCopy CP "/path/to/dir" "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/katalog]? [SAS] "--recursive = True--skicka-MD5

Ladda upp en uppsättning filer med hjälp av en SAS-token och jokertecken (*):

- AzCopy CP "/Path/*foo/* bar/*. pdf" "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/katalog]? [SAS] "

Ladda upp filer och kataloger med hjälp av en SAS-token och jokertecken (*):

- AzCopy CP "/Path/*foo/* bar *" "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/katalog]? [SAS] "--recursive = True

Hämta en enda fil med hjälp av OAuth-autentisering. Om du ännu inte har loggat in på AzCopy kör du kommandot AzCopy login innan du kör följande kommando.

- AzCopy CP "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]" "/path/to/File.txt"

Hämta en enda fil med hjälp av en SAS-token:

- AzCopy CP "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]? [SAS] ""/path/to/File.txt "

Hämta en enda fil med hjälp av en SAS-token och sedan dirigera utdata till en fil (endast block-blobbar):
  
- AzCopy CP "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]? [SAS] ">"/path/to/File.txt "

Ladda ned en hel katalog med hjälp av en SAS-token:
  
- AzCopy CP "https://[Account]. blob. Core. Windows. net/[container]/[sökväg/till/katalog]? [SAS] ""/path/to/dir "--recursive = True

En kommentar om att använda jokertecken (*) i URL: er:

Det finns bara två sätt som stöds för att använda jokertecken i en URL. 

- Du kan använda en precis efter det sista snedstrecket (/) för en URL. Detta kopierar alla filer i en katalog direkt till målet utan att placera dem i en under katalog.

- Du kan också använda ett i namnet på en behållare så länge URL: en endast refererar till en behållare och inte till en blob. Du kan använda den här metoden för att hämta filer från en delmängd behållare.

Hämta innehållet i en katalog utan att kopiera den innehåller själva katalogen.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[container]/[sökväg/till/mapp]/*? [SAS] ""/path/to/dir "

Ladda ned ett helt lagrings konto.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/" "/path/to/dir"--recursive

Hämta en delmängd av behållare i ett lagrings konto med hjälp av en symbol för jokertecken (*) i behållar namnet.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[container * Name]" "/path/to/dir"--recursive

Kopiera en enskild blob till en annan BLOB med hjälp av en SAS-token.

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]? [SAS] "

Kopiera en enskild blob till en annan BLOB med hjälp av en SAS-token och en OAuth-token. Du måste använda en SAS-token i slutet av käll kontots URL, men mål kontot behöver inte vara ett om du loggar in i AzCopy med hjälp av kommandot AzCopy login. 

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB] "

Kopiera en virtuell BLOB-katalog till en annan med hjälp av en SAS-token:

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net/[container]/[sökväg/till/katalog]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[container]/[sökväg/till/katalog]? [SAS] "--recursive = True

Kopiera alla BLOB-behållare, kataloger och blobbar från lagrings kontot till ett annat med hjälp av en SAS-token:

- AzCopy CP "https://[srcaccount]. blob. Core. Windows. net? [SAS] "" https://[destaccount]. blob. Core. Windows. net? [SAS] "--recursive = True

Kopiera ett enskilt objekt till Blob Storage från Amazon Web Services (AWS) S3 med hjälp av en åtkomst nyckel och en SAS-token. Ställ först in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.
  
- AzCopy CP "https://s3.amazonaws.com/[Bucket]/[Object]" "https://[destaccount]. blob. Core. Windows. net/[container]/[sökväg/till/BLOB]? [SAS] "

Kopiera en hel katalog till Blob Storage från AWS S3 med hjälp av en åtkomst nyckel och en SAS-token. Ställ först in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.

- AzCopy CP "https://s3.amazonaws.com/[Bucket]/[mapp]" "https://[destaccount]. blob. Core. Windows. net/[container]/[sökväg/till/katalog]? [SAS] "--recursive = True

Se https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html för att bättre förstå [Folder]-plats hållaren.

Kopiera alla buckets till Blob Storage från Amazon Web Services (AWS) med hjälp av en åtkomst nyckel och en SAS-token. Ställ först in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.

- AzCopy CP "https://s3.amazonaws.com/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--recursive = True

Kopiera alla buckets till Blob Storage från ett Amazon Web Services (AWS)-region med hjälp av en åtkomst nyckel och en SAS-token. Ställ först in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.

- AzCopy CP "https://s3-[region]. amazonaws. com/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--recursive = True

Kopiera en delmängd av buckets med hjälp av en symbol för jokertecken (*) i Bucket-namnet. Precis som i föregående exempel behöver du en åtkomst nyckel och en SAS-token. Se till att ställa in miljövariabeln AWS_ACCESS_KEY_ID och AWS_SECRET_ACCESS_KEY för AWS S3-källa.

- AzCopy CP "https://s3.amazonaws.com/[Bucket * Name]/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--recursive = True

## <a name="options"></a>Alternativ

**--säkerhets kopiering**                               Aktiverar Windows-SeBackupPrivilege för uppladdningar eller SeRestorePrivilege för hämtningar för att tillåta AzCopy att se läsa alla filer, oavsett deras fil Systems behörigheter och för att återställa alla behörigheter. Kräver att kontot som kör AzCopy redan har dessa behörigheter (t. ex. har administratörs rättigheter eller är medlem i gruppen ansvariga för säkerhets kopiering). Alla den här flaggan aktiverar de behörigheter som kontot redan har.

**--BLOB-Type** -strängen definierar BLOB-typen vid målet. Detta används för att ladda upp blobar och när du kopierar mellan konton (standard identifiering). Giltiga värden är "identifiering", "BlockBlob", "PageBlob" och "AppendBlob". När du kopierar mellan konton gör värdet "identifiera" att AzCopy använder typen av käll-BLOB för att fastställa typen av BLOB för målet. När en fil laddas upp avgör "identifiera om filen är en VHD-eller VHDX-fil baserat på fil namns tillägget. Om filen är en VHD-eller VHDX-fil behandlar AzCopy filen som en Page blob. (standard "identifiering")

**--Block-Blob-Tier** sträng överföring block-blobar direkt till valfri [åtkomst nivå](../blobs/storage-blob-storage-tiers.md) . (standard saknas). Giltiga värden är "ingen", "Het", "sval" och "Arkiv". Om ingen eller ingen nivå skickas, kommer blobben att ärva lagrings kontots nivå.

**--block-size-MB** float Använd den här block storleken (anges i MIB) vid överföring till Azure Storage och nedladdning från Azure Storage. Standardvärdet beräknas automatiskt baserat på fil storlek. Decimal tal tillåts (till exempel: 0,25).

**--Cache-Control** -sträng ange Cache-Control-huvudet. Returnerades vid nedladdning.

**--kontrol lera längd**                         Kontrol lera längden på en fil på målet efter överföringen. Om det finns ett matchnings fel mellan källan och målet markeras överföringen som misslyckad. (standard sant)

**--kontrol lera-MD5-** sträng anger hur strikt MD5-hashar ska verifieras vid hämtning. Endast tillgängligt vid hämtning. Tillgängliga alternativ: nocheck, inloggning, FailIfDifferent, FailIfDifferentOrMissing. (standard "FailIfDifferent")

**--innehålls-dispositions** sträng ange innehålls-dispositions rubriken. Returnerades vid nedladdning.

**--innehålls kodnings** sträng anger innehålls kodnings huvudet. Returnerades vid nedladdning.

**--innehålls språk** sträng anger innehålls-språkets rubrik. Returnerades vid nedladdning.

**--innehålls typ** sträng anger filens innehålls typ. Innebär No-gissning-MIME-type. Returnerades vid nedladdning.

**--Decompress**                           Expandera automatiskt filer vid hämtning, om deras innehålls kodning visar att de är komprimerade. Innehålls kodnings värden som stöds är gzip och DEFLATE. Fil namns tilläggen ". gz"/". gzip" eller ". zz" är inte nödvändiga, men kommer att tas bort om det är tillgängligt.

**--sträng för exkludera attribut** (endast Windows) Uteslut filer vars attribut matchar attributlistan. Till exempel: A; Na R

**--exkludera-Blob-typ** sträng anger alternativt vilken typ av BLOB (BlockBlob/PageBlob/AppendBlob) som ska undantas vid kopiering av blobbar från behållaren eller kontot. Den här flaggan kan inte användas för att kopiera data från icke-Azure-tjänst till tjänst. Mer än en BLOB ska avgränsas med;.

**--sträng för exkluderings Sök väg** undantar dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg (till exempel: min mapp, mappen subDirName/File. pdf). När de används i kombination med konto Traversal inkluderar inte sökvägar namnet på behållaren.

**--exkludera-Pattern-** sträng exkludera dessa filer vid kopiering. Det här alternativet stöder jokertecken (*)

**--Följ-symlinks**                      Följ symboliska länkar när du laddar upp från lokalt fil system.

**--från-till-** sträng (valfritt) anger källans mål kombination. Till exempel: LocalBlob, BlobLocal, LocalBlobFS.

**-h,--hjälp** för att kopiera

**--include-attribut** sträng (endast Windows) inkludera filer vars attribut stämmer överens med attributlistan. Till exempel: A; Na R

**--include-Path-** sträng innehåller bara dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg (till exempel: min mapp, mappen subDirName/File. pdf).

**--Inkludera-mönster** sträng inkludera endast dessa filer vid kopiering. Det här alternativet stöder jokertecken (*). Separera filer med hjälp av en ";".

**--sträng på loggnivå** definierar loggens utförlighet för logg filen, tillgängliga nivåer: info (alla begär Anden/svar), varning (långsamma svar), fel (endast misslyckade förfrågningar) och ingen (inga utgående loggar). (standard information)

**--** sträng överföring av metadata till Azure Storage med dessa nyckel/värde-par som metadata.

**--no-gissning-MIME-type**                   Förhindrar AzCopy från att identifiera innehålls typ baserat på fil namns tillägget eller innehållet i filen.

**--Skriv över** sträng skriver över konfliktskapande filer och blobbar på målet om den här flaggan har angetts till true. Möjliga värden är "true", "false", "ifSourceNewer" och "prompt". (standard "true")

**--sid-BLOB-skiktets** sträng överförings-blob till Azure Storage att använda denna BLOB-nivå. (standard "ingen")

**--bevara-senaste ändrings tid**          Endast tillgängligt om målet är fil system.

**--Behåll-SMB-Permissions** -sträng falskt som standard. Bevarar SMB ACL: er mellan medvetna resurser (Windows och Azure Files). För hämtningar måste du också använda `--backup` flaggan för att återställa behörigheter där den nya ägaren inte kommer att vara den användare som kör AzCopy. Den här flaggan gäller för både filer och mappar, om inte ett fil filter anges (t. ex `include-pattern`.).

**--Behåll-SMB-info** sträng falskt som standard. Bevarar information om SMB-egenskaper (senaste skrivnings tid, skapande tid, attribut bitar) mellan SMB-medvetna resurser (Windows och Azure Files). Endast de BITS-attribut som stöds av Azure Files kommer att överföras. andra kommer att ignoreras. Den här flaggan gäller för både filer och mappar, om inte ett fil filter anges (t. ex. include-mönster). Den information som överförs för mappar är samma som för filer, förutom senaste skrivnings tid som aldrig bevaras för mappar.

**--Behåll-ägare**                       Har bara en inverkan i vid hämtning av `--preserve-smb-permissions` data och endast när används. Om värdet är sant (standardvärdet) bevaras fil ägaren och gruppen i hämtningar. Om den här flaggan har angetts till false, kommer fortfarande att bevara ACL: er, `--preserve-smb-permissions` men ägare och grupp kommer att baseras på den användare som kör AzCopy.

**--Skicka-MD5**                             Skapa en MD5-hash av varje fil och spara hashen som Content-MD5-egenskapen för Målmatrisen eller-filen. (Som standard skapas inte hashen.) Endast tillgängligt vid uppladdning.

**--rekursivt**                            Titta i under kataloger rekursivt vid överföring från lokalt fil system.

**--S2S-detect-källa-ändrad**           Kontrol lera om källan har ändrats efter uppräkningen.

**--S2S-handle-ogiltig-metadata** -sträng anger hur ogiltiga nycklar för nycklar hanteras. Tillgängliga alternativ: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (standard "ExcludeIfInvalid")

**--S2S-konserver-Access-Tier**             Bevara åtkomst nivån under tjänst-till-tjänst-kopiering. Se [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) lag rings nivåer för att säkerställa att mål lagrings kontot har stöd för att ange åtkomst nivå. I de fall då det inte finns stöd för att ange åtkomst nivå kan du använda s2sPreserveAccessTier = false för att kringgå kopiering av åtkomst nivå. (standard sant)

**--S2S-bevara-egenskaper**              Behåll fullständiga egenskaper under tjänst-till-tjänst-kopiering. För AWS S3 och Azure File non-EnFile Source, returnerar List-åtgärden inte fullständiga egenskaper för objekt och filer. För att bevara fullständig egenskaper måste AzCopy skicka ytterligare en begäran per objekt eller fil. (standard sant)

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--Cap-Mbit/s UInt32**      CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**--Skriv** sträng format för kommandots utdata. Alternativen är: text, JSON. Standardvärdet är ' text '. (standard text)

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
