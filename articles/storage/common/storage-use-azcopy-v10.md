---
title: Kopiera eller flytta data till Azure Storage med AzCopy v10 (förhandsversion) | Microsoft Docs
description: Använda AzCopy v10 (förhandsversion) verktyg för att flytta eller kopiera data till och från blob-, tabell- och filinnehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagringskonton. Migrera enkelt dina data till Azure Storage.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.component: common
ms.openlocfilehash: a1b183e5b0929a2149502aa340e2e69c725dba6d
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168273"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Överföra data med AzCopy v10 (förhandsversion)

AzCopy v10 (förhandsversion) är nästa generations kommandoradsverktyg för att kopiera data till och från Microsoft Azure Blob- och storage som erbjuder en omarbetad kommandoradsgränssnittet och ny arkitektur för tillförlitlig dataöverföringar med höga prestanda. Du kan använda AzCopy för att kopiera data mellan ett filsystem och ett lagringskonto eller mellan lagringskonton.

## <a name="whats-new-in-azcopy-v10"></a>Vad är nytt i AzCopy v10

- Synkronisera ett filsystem till Azure Blob eller vice versa. Använd `azcopy sync <source> <destination>`. Perfekt för inkrementell kopia scenarier.
- Har stöd för Azure Data Lake Storage Gen2 API: er. Använd `myaccount.dfs.core.windows.net` som en URI för att anropa API: er för ADLS Gen2.
- Har stöd för kopiering av ett hela konto (endast Blob-tjänst) till ett annat konto.
- Konto för att kopiera konto nu använder den nya [placera från URL: en](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API: er. Det krävs ingen dataöverföring till klienten vilket gör överföringen snabbare!
- Lista/ta bort filer och blobar i en viss sökväg.
- Har stöd för mönster med jokertecken i en sökväg som--inkludera och--exkludera flaggor.
- Förbättrad flexibilitet: varje AzCopy-instans skapas en jobbet och en tillhörande loggfil. Du kan visa och starta om tidigare jobb och återuppta misslyckade jobb. AzCopy försöker automatiskt en överföring efter ett fel.
- Förbättringar av allmänna prestanda.

## <a name="download-and-install-azcopy"></a>Ladda ned och installera AzCopy

### <a name="latest-preview-version-v10"></a>Senaste förhandsversionen (v10)

Ladda ned den senaste förhandsversionen av AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>Senaste produktionsversionen (v8.1)

Ladda ned den [senaste produktionsversionen av AzCopy för Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy stöd för Table storage-tjänsten (v7.3)

Ladda ned den [AzCopy v7.3 stöd för kopiering av data till/från Microsoft Azure Table storage-tjänsten](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Anvisningarna efter installation

AzCopy v10 kräver inte en installation. Öppna ett kommandoradsprogram för önskade och navigera till mappen där den `azcopy.exe` körbara finns. Om du vill kan du lägga till mappen AzCopy systemsökvägen.

## <a name="authentication-options"></a>Autentiseringsalternativ

AzCopy v10 kan du använda följande alternativ när du autentiserar med Azure Storage:
- Azure Active Directory. Använd ```.\azcopy login``` att logga in med Azure Active Directory.  Användaren bör ha [”Storage Blob Data-deltagare” rolltilldelningen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) att skriva till Blob storage med Azure Active Directory-autentisering.
- SAS-token som behöver läggas till Blob-sökväg. Du kan generera SAS-token med hjälp av Azure Portal, [Lagringsutforskaren](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestorageblobsastoken?view=azurermps-6.9.0), eller andra verktyg du väljer. Mer information finns i [exempel](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Komma igång

AzCopy v10 har en enkel lokal dokumenterade syntax. Den allmänna syntaxen ser ut så här:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Example:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/containersastoken" --recursive=true
```

Här är hur du kan hämta en lista över tillgängliga kommandon:

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

Se hjälpsidan och exempel för ett visst kommando kör du kommandot nedan:

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="copy-data-to-azure-storage"></a>Kopiera data till Azure Storage

Använd kopieringskommandot för att överföra data från källan till målet. Käll-och mål kan vara en:
- Lokalt filsystem
- Azure Blob/virtuell katalog/behållare URI
- Azure-filen/Directory/filresurs URI
- Azure Data Lake Storage Gen2 filsystem/katalogfil URI

> [!NOTE]
> Just nu stöder AzCopy v10 kopierar endast blockblob-objekt mellan två lagringskonton.

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Följande kommando laddar upp alla filer i mappen C:\local\path rekursivt till behållaren ”mycontainer1”:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Följande kommando laddar upp alla filer i mappen C:\local\path (utan recursing i underkatalogerna) till behållaren ”mycontainer1”:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Om du vill ha fler exempel, använder du följande kommando:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Kopiera data mellan två lagringskonton

Kopiering av data mellan två lagringskonton använder den [placera Block från URL: en](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API och inte använda klientdatorns nätverkets bandbredd. Data kopieras mellan två Azure Storage servrar direkt medan AzCopy samordnar bara kopieringen. 

Om du vill kopiera data mellan två lagringskonton, använder du följande kommando:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> Kommandot kommer att räkna upp alla blob-behållare och kopiera dem till mål-kontot. Just nu stöder AzCopy v10 kopierar endast blockblob-objekt mellan två lagringskonton. Alla andra storage-konto-objekt (append BLOB-objekt, sidblobar, filer, tabeller och köer) kommer att hoppas över.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Kopiera en VHD-avbildning till ett lagringskonto

AzCopy v10 som standard överför data till blockblobar. Men om en källfil har vhd-tillägg, AzCopy v10 kommer som standard att överföra den till en sidblobb. Det här beteendet inte konfigurerbart.

## <a name="sync-incremental-copy-and-delete"></a>Synkronisering: inkrementell kopiera och ta bort

> [!NOTE]
> Synkronisera kommandot synkroniserar innehåll från källa till mål, vilket omfattar borttagning av målfilerna om de inte finns i källan. Kontrollera att du använder det mål som du tänker synkronisera.

Om du vill synkronisera det lokala filsystemet till storage-konto, använder du följande kommando:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

På samma sätt som kan du synkronisera en Blob-behållare till ett lokalt filsystem:

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Kommandot kan du synkronisera källan till målet baserat på tidsstämplar som senast ändrade inkrementellt. Om du lägger till eller ta bort en fil i källan, gör AzCopy v10 samma i målet.

## <a name="advanced-configuration"></a>Avancerad konfiguration

### <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

För att konfigurera proxyinställningarna för AzCopy v10, anger du den miljö variabeln https_proxy med följande kommando:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Optimera dataflöde

Ställa in miljövariabeln AZCOPY_CONCURRENCY_VALUE kan konfigurera antalet samtidiga begäranden och styra dataflödesprestanda och användning av databasresurser. Värdet anges till 300 som standard. Minska värdet begränsar bandbredd och Processorn som används av AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
```

## <a name="troubleshooting"></a>Felsökning

AzCopy v10 skapar loggfiler och plan filer för alla jobb. Du kan använda loggarna för att undersöka och felsöka eventuella problem. Loggarna innehåller statusen för fel (UPLOADFAILED COPYFAILED och DOWNLOADFAILED), den fullständiga sökvägen och orsaken till felet. Jobbloggar och plan filer finns i % USERPROFILE\\.azcopy mapp.

### <a name="review-the-logs-for-errors"></a>Granska loggarna efter fel

Följande kommando får alla fel med UPLOADFAILED status från 04dc9ca9-158f-7945-5933-564021086c79-loggen:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>Visa och återuppta jobb

Varje överföring åtgärden skapas ett jobb för AzCopy. Du kan visa historiken för jobb med hjälp av följande kommando:

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

Du kan återuppta en misslyckades/avbrutna jobb med hjälp av dess identifierare tillsammans med SAS-token (det inte är beständiga av säkerhetsskäl):

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>Nästa steg

Din feedback är alltid välkomnade. Om du har några frågor kan problem eller allmän feedback skicka dem på https://github.com/Azure/azure-storage-azcopy. Tack!