---
title: Kopiera eller flytta data till Azure Storage med hjälp av AzCopy v10 (förhandsversion) | Microsoft Docs
description: Använda AzCopy v10 (förhandsversion) kommandoradsverktyget för att flytta eller kopiera data till och från blob-, data lake- och filinnehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagringskonton. Migrera enkelt dina data till Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: ffd448db86c8658619da5339cd34eb9dba7e05ce
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278436"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>Överföra data med AzCopy v10 (förhandsversion)

AzCopy v10 (förhandsversion) är kommandoradsverktyg för att kopiera data till eller från Microsoft Azure Blob- och storage. AzCopy v10 erbjuder en omarbetad kommandoradsgränssnittet och ny arkitektur för tillförlitlig data överförs. Med AzCopy kan kopiera du data mellan ett filsystem och ett lagringskonto eller mellan lagringskonton.

## <a name="whats-new-in-azcopy-v10"></a>Vad är nytt i AzCopy v10

- Synkroniserar filsystem till Azure Blob storage eller vice versa. Använd `azcopy sync <source> <destination>`. Perfekt för inkrementell kopia scenarier.
- Har stöd för Azure Data Lake Storage Gen2 API: er. Använd `myaccount.dfs.core.windows.net` som en URI för att anropa API: er för Data Lake Storage Gen2.
- Har stöd för kopiering av ett hela konto (endast Blob-tjänst) till ett annat konto.
- Har stöd för kopiering av data från en Amazon Web Services S3-bucket.
- Använder den nya [placera Block från URL: en](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API: er för att konto kopia. Dataöverföringen är snabbare, eftersom krävs inte skickades till klienten.
- Visar en lista över eller tar bort filer och blobar i en viss sökväg.
- Stöd för mönster med jokertecken i en sökväg och--exkludera flaggor.
- Skapar en jobbet och en tillhörande loggfil med varje AzCopy-instans. Du kan visa och starta om tidigare jobb och återuppta misslyckade jobb. AzCopy försöker automatiskt en överföring efter ett fel.
- Allmän prestandaförbättringar för funktioner.

## <a name="download-and-install-azcopy"></a>Ladda ned och installera AzCopy

### <a name="latest-preview-version-v10"></a>Senaste förhandsversionen (v10)

Ladda ned den senaste förhandsversionen av AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>Senaste produktionsversionen (v8.1)

Ladda ned den [senaste produktionsversionen av AzCopy för Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy stöd för Table storage-tjänsten (v7.3)

Ladda ned den [AzCopy v7.3 stöd för kopiering av data till/från Microsoft Azure Table storage-tjänsten](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Anvisningarna efter installation

AzCopy v10 kräver inte en installation. Öppna din önskade kommandoradsprogram och bläddra till mappen där `azcopy.exe` finns. Om det behövs kan du lägga till mappen AzCopy systemsökvägen för enkel användning.

## <a name="authentication-options"></a>Autentiseringsalternativ

AzCopy v10 stöder följande alternativ när du autentiserar med Azure Storage:
- **Azure Active Directory** (stöds för **Blob-och Data Lake Storage Gen2**). Använd ```.\azcopy login``` att logga in med Azure Active Directory.  Användaren bör ha [”Storage Blob Data-deltagare” rolltilldelningen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) att skriva till Blob storage med Azure Active Directory-autentisering. Autentisering via hanterade identiteter för Azure-resurser, använda `azcopy login --identity`.
- **Delad signaturtoken för åtkomst [stöds för Blob-och Filtjänster]**. Lägg till token för delad åtkomst (signatur) till blob-sökväg på kommandoraden för att använda den. Du kan generera SAS-token med Azure-portalen [Lagringsutforskaren](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), eller andra verktyg du väljer. Mer information finns i [exempel](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Komma igång

> [!TIP]
> **Föredrar du ett grafiskt användargränssnitt?**
>
> [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), en skrivbordsklient som förenklar hantering Azure Storage-data nu använder AzCopy överföra data till och från Azure Storage för snabbare.
>
> Aktivera AzCopy i Storage Explorer under den **förhandsversion** menyn.
> ![Aktivera AzCopy som en motor för överföring i Azure Storage Explorer](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 har en egen dokumenterade syntax. När du har loggat in till Azure Active Directory, ut den allmänna syntaxen som följande:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D"
```

Här är hur du kan hämta en lista över tillgängliga kommandon:

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

Om du vill se hjälpsidan och exempel för ett visst kommando kör du följande kommando:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Skapa en blob-behållare eller filresurs 

**Skapa en blobcontainer**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Skapa en filresurs**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Skapa en blob-behållare med hjälp av Azure Data Lake Storage Gen2**

Om du har aktiverat hierarkisk namnområden på Blob storage-kontot kan använda du följande kommando för att skapa en ny blobbehållare för att överföra filer.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Kopiera data till Azure Storage

Använd kopieringskommandot för att överföra data från källan till målet. Käll- och målservrar kan vara en:
- Lokalt filsystem
- Azure Blob/virtuell katalog/behållare URI
- Azure-filen/Directory/filresurs URI
- Azure Data Lake Storage Gen2 filsystem/katalogfil URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Följande kommando laddar upp alla filer i mappen `C:\local\path` rekursivt till behållaren `mycontainer1`, skapa `path` katalogen i behållaren. När `--put-md5` flagga har angetts, AzCopy beräknar och lagrar md5-hash för varje fil i `Content-md5` egenskapen för motsvarande blob för senare användning.

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true --put-md5
```

Följande kommando laddar upp alla filer i mappen `C:\local\path` (utan recursing i underkatalogerna) till behållaren `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --put-md5
```

Om du vill söka efter fler exempel, använder du följande kommando:

```azcopy
.\azcopy cp -h
```

## <a name="copy-blob-data-between-two-storage-accounts"></a>Kopiera Blob-data mellan två lagringskonton

Kopiering av data mellan två lagringskonton använder den [placera Block från URL: en](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, och inte använda klientdatorns bandbredd i nätverket. Data kopieras mellan två Azure Storage servrar direkt, medan AzCopy samordnar bara kopieringen. Det här alternativet är för närvarande bara tillgänglig för Blob storage.

Om du vill kopiera alla Blob-data mellan två lagringskonton, använder du följande kommando:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

Om du vill kopiera en Blob-behållare till en annan Blob-behållare, använder du följande kommando:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Kopiera en VHD-avbildning till ett lagringskonto

AzCopy som standard överför data till blockblobar. Ladda upp filer som Tilläggsblobbar och Sidblobbar använder du flaggan `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Synkronisering: inkrementell kopiera och ta bort (endast Blob storage)

Kommandot Synkronisera synkroniserar innehållet i en källkatalog i en katalog i målet, jämföra filnamn och senast ändrad tidsstämplar. Den här åtgärden innehåller valfria borttagningen av målfilerna om de inte finns i källan när den `--delete-destination=prompt|true` flagga har angetts. Ta bort beteendet är inaktiverad som standard. 

> [!NOTE] 
> Använd den `--delete-destination` flaggan med försiktighet. Aktivera den [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funktionen innan du aktiverar delete beteende synkroniserade att förhindra oavsiktliga borttagningar i ditt konto. 
>
> När `--delete-destination` har angetts till SANT, AzCopy tar bort filer som inte finns i källan från målet utan någon uppmaning till användaren. Om du vill uppmanas att bekräfta använda `--delete-destination=prompt`.

Om du vill synkronisera det lokala filsystemet till storage-konto, använder du följande kommando:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Du kan också synkronisera en blob-behållare till ett lokalt filsystem:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Det här kommandot synkroniserar stegvis källan till målet baserat på senaste ändrade tidsstämplar. Om du lägger till eller ta bort en fil i källan, gör AzCopy v10 samma i målet. Före borttagningen uppmanar AzCopy dig att bekräfta.

## <a name="copy-data-from-amazon-web-services-aws-s3"></a>Kopiera data från Amazon Web Services (AWS) S3

Ange följande miljövariabler för att autentisera med AWS S3-bucket:

```
# For Windows:
set AWS_ACCESS_KEY_ID=<your AWS access key>
set AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For Linux:
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For MacOS
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
```

Om du vill kopiera en bucket på en blobbehållare, kör du följande kommando:

```
.\azcopy cp "https://s3.amazonaws.com/mybucket" "https://myaccount.blob.core.windows.net/mycontainer?<sastoken>" --recursive
```

Mer information om hur du kopierar data från AWS S3 med hjälp av AzCopy, finns på sidan [här](https://github.com/Azure/azure-storage-azcopy/wiki/Copy-from-AWS-S3).

## <a name="advanced-configuration"></a>Avancerad konfiguration

### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Om du vill konfigurera proxyinställningarna för AzCopy v10 ange miljö variabeln https_proxy med hjälp av följande kommando:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Optimera dataflöde

Ställa in miljövariabeln AZCOPY_CONCURRENCY_VALUE konfigurera antalet samtidiga begäranden och för att kontrollera dataflöde prestanda- och förbrukning. Värdet anges till 300 som standard. Minska värdet begränsar bandbredd och Processorn som används av AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>Ändra platsen för loggfilerna

Du kan ändra plats loggfiler om det behövs eller för att undvika att fylla OS-disken.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Ändra standardnivån 

AzCopy loggningsnivån är som standard information. Om du vill minska log detaljnivå för att spara diskutrymme kan du skriva över den inställningen med hjälp av ``--log-level`` alternativet. Tillgängliga loggningsnivåerna är: FELSÖKNING, INFO, varning, fel, PANIK och oåterkalleligt fel.

### <a name="review-the-logs-for-errors"></a>Granska loggarna efter fel

Följande kommando får alla fel med UPLOADFAILED status från 04dc9ca9-158f-7945-5933-564021086c79-loggen:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>Felsökning

AzCopy v10 skapar loggfiler och plan filer för alla jobb. Du kan använda loggarna för att undersöka och felsöka eventuella problem. Loggarna innehåller statusen för fel (UPLOADFAILED COPYFAILED och DOWNLOADFAILED), den fullständiga sökvägen och orsaken till felet. Jobbloggar och plan filer finns i % USERPROFILE\\.azcopy mapp på Windows eller $HOME\\.azcopy mapp på Mac och Linux.

> [!IMPORTANT]
> När du skickar en begäran om att Microsoft Support (eller felsöka problem som rör tredje part), dela den redigerade versionen av kommandot som du vill köra. Detta säkerställer att Signaturen inte är delas av misstag med vem som helst. Du hittar den redigerade versionen i början av loggfilen.

### <a name="view-and-resume-jobs"></a>Visa och återuppta jobb

Varje överföring åtgärden skapas ett jobb för AzCopy. Använd följande kommando för att visa historiken för jobb:

```azcopy
.\azcopy jobs list
```

Om du vill visa projektstatistik, använder du följande kommando:

```azcopy
.\azcopy jobs show <job-id>
```

Om du vill filtrera överföringar efter status, använder du följande kommando:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Använd följande kommando för att återuppta ett jobb som misslyckades/har avbrutits. Det här kommandot använder sin identifierare tillsammans med SAS-token som det är inte beständiga av säkerhetsskäl:

```azcopy
.\azcopy jobs resume <jobid> --source-sas="<sastokenhere>"
.\azcopy jobs resume <jobid> --destination-sas="<sastokenhere>"
```

## <a name="next-steps"></a>Nästa steg

Om du har frågor eller problem med allmän feedback kan du skicka dem [på GitHub](https://github.com/Azure/azure-storage-azcopy).


