---
title: Konfigurera, optimera och Felsök AzCopy med Azure Storage | Microsoft Docs
description: Konfigurera, optimera och Felsök AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3773f9a8464dc94436d6d2503b173d4674033ab1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565040"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurera, optimera och felsöka AzCopy

AzCopy är ett kommando rads verktyg som du kan använda för att kopiera blobbar eller filer till eller från ett lagrings konto. Den här artikeln hjälper dig att utföra avancerade konfigurations åtgärder och hjälper dig att felsöka problem som kan uppstå när du använder AzCopy.

> [!NOTE]
> Om du letar efter innehåll som hjälper dig att komma igång med AzCopy kan du läsa följande artiklar:
> - [Kom igång med AzCopy](storage-use-azcopy-v10.md)
> - [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
> - [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)
> - [Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Om du vill konfigurera proxyinställningarna för AzCopy anger du `https_proxy` miljövariabeln. Om du kör AzCopy i Windows identifierar AzCopy automatiskt proxyinställningar, så du behöver inte använda den här inställningen i Windows. Om du väljer att använda den här inställningen i Windows kommer den att åsidosätta automatisk identifiering.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | Använd följande i en kommando tolk:`set https_proxy=<proxy IP>:<proxy port>`<br> I PowerShell använder du:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

För närvarande stöder AzCopy inte proxyservrar som kräver autentisering med NTLM eller Kerberos.

## <a name="optimize-throughput"></a>Optimera data flödet

Du kan använda `cap-mbps` flaggan för att placera ett tak på data flödets data hastighet. Till exempel följande kommando CAPS data flöde till `10` megabit (MB) per sekund.

```azcopy
azcopy cap-mbps 10
```

Data flödet kan minska vid överföring av små filer. Du kan öka data flödet genom att ställa in `AZCOPY_CONCURRENCY_VALUE` miljövariabeln. Den här variabeln anger antalet samtidiga begär Anden som kan utföras.  Om datorn har färre än 5 processorer anges värdet för den här variabeln till `32`. Annars är standardvärdet lika med 16 multiplicerat med antalet processorer. Det maximala standardvärdet för den här `300`variabeln är, men du kan manuellt ange det här värdet högre eller lägre.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

`azcopy env` Använd för att kontrol lera det aktuella värdet för den här variabeln.  Om värdet är tomt `AZCOPY_CONCURRENCY_VALUE` anges variabeln till `300`standardvärdet.

## <a name="change-the-location-of-the-log-files"></a>Ändra platsen för loggfilerna

Som standard finns loggfiler i `%USERPROFILE\\.azcopy` katalogen på Windows eller `$HOME\\.azcopy` i katalogen på Mac och Linux. Du kan ändra den här platsen om du behöver med hjälp av dessa kommandon.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

`azcopy env` Använd för att kontrol lera det aktuella värdet för den här variabeln. Om värdet är tomt skrivs loggar till standard platsen.

## <a name="change-the-default-log-level"></a>Ändra standard logg nivån

Som standard är logg nivån för AzCopy inställd på `INFO`. Om du vill minska loggens utförlighet för att spara disk utrymme skriver du över den här inställningen med hjälp ``--log-level`` av alternativet. 

Tillgängliga logg nivåer är: `DEBUG`, `INFO`, `WARNING` `ERROR` `FATAL`, ,och.`PANIC`

## <a name="troubleshoot-issues"></a>Felsöka problem

AzCopy skapar logg-och plan-filer för varje jobb. Du kan använda loggarna för att undersöka och felsöka eventuella problem. 

Loggarna innehåller status för felen (`UPLOADFAILED`, `COPYFAILED`, och `DOWNLOADFAILED`), den fullständiga sökvägen och orsaken till problemet.

Som standard finns logg-och plan-filerna i katalogen på `%USERPROFILE\\.azcopy` Windows eller `$HOME\\.azcopy` i en katalog på Mac och Linux.

> [!IMPORTANT]
> När du skickar en begäran till Microsoft Support (eller fel sökning av problemet som berör tredje part) delar du den avvisade versionen av kommandot som du vill köra. Detta säkerställer att SAS inte delas av misstag med vem. Du kan hitta den avvisade versionen i början av logg filen.

### <a name="review-the-logs-for-errors"></a>Granska loggarna för fel

Följande kommando får alla fel med `UPLOADFAILED` status `04dc9ca9-158f-7945-5933-564021086c79` från loggen:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Visa och återuppta jobb

Varje överförings åtgärd kommer att skapa ett AzCopy-jobb. Använd följande kommando för att visa jobb historiken:

```
azcopy jobs list
```

Om du vill visa jobb statistiken använder du följande kommando:

```
azcopy jobs show <job-id>
```

Använd följande kommando för att filtrera överföringarna efter status:

```
azcopy jobs show <job-id> --with-status=Failed
```

Använd följande kommando för att återuppta ett misslyckat/avbrutet jobb. Det här kommandot använder sitt ID tillsammans med SAS-token eftersom det inte är beständigt av säkerhets skäl:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

När du återupptar ett jobb tittar AzCopy på jobb Plans filen. Plan filen visar en lista över alla filer som identifierades för bearbetning när jobbet först skapades. När du återupptar ett jobb kommer AzCopy att försöka överföra alla filer som anges i den plan fil som inte redan har överförts.