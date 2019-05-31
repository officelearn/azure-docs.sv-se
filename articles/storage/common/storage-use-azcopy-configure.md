---
title: Konfigurera, optimera och felsöka AzCopy med Azure Storage | Microsoft Docs
description: Konfigurera, optimera och felsöka AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a160591ef0a47eed097ce8db373878f32965de9b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247131"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurera, optimera och felsöka AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar och filer till eller från ett lagringskonto. Den här artikeln hjälper dig att utföra uppgifter för avancerad konfiguration och hjälper dig att felsöka problem som kan uppstå när du använder AzCopy.

> [!NOTE]
> Om du letar efter innehåll som hjälper dig att komma igång med AzCopy, se följande artiklar:
> - [Kom igång med AzCopy](storage-use-azcopy-v10.md)
> - [Överföra data med AzCopy och blob storage](storage-use-azcopy-blobs.md)
> - [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
> - [Överföra data med AzCopy och Amazon S3 buckets](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Om du vill konfigurera proxyinställningarna för AzCopy, ange den `https_proxy` miljövariabeln.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set https_proxy=<proxy IP>:<proxy port>` |
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

AzCopy stöder för närvarande inte proxyservrar som kräver autentisering med NTLM eller Kerberos.

## <a name="optimize-throughput"></a>Optimera dataflöde

Ange den `AZCOPY_CONCURRENCY_VALUE` miljövariabeln konfigurera antalet samtidiga begäranden och för att kontrollera dataflöde prestanda- och förbrukning. Om datorn har färre än 5 processorer, så är värdet för den här variabeln anges till `32`. Annars är standardvärdet lika med 16 multiplicerat med antalet processorer. Det maximala standardvärdet för den här variabeln är `300`, men du kan manuellt ange ett värde högre eller lägre.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Använd den `azcopy env` att kontrollera det aktuella värdet för den här variabeln.  Om värdet är tomt, kommer `AZCOPY_CONCURRENCY_VALUE` variabeln anges till standardvärdet `300`.

## <a name="change-the-location-of-the-log-files"></a>Ändra platsen för loggfilerna

Loggfilerna finns som standard i den `%USERPROFILE\\.azcopy` mapp på Windows eller i den `$HOME\\.azcopy` mapp på Mac och Linux. Du kan ändra den här platsen om du behöver med hjälp av dessa kommandon.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Använd den `azcopy env` att kontrollera det aktuella värdet för den här variabeln. Om värdet är tomt, skrivs loggarna till standardplatsen.

## <a name="change-the-default-log-level"></a>Ändra standardnivån

Som standard AzCopy är loggnivån `INFO`. Om du vill minska log detaljnivå för att spara diskutrymme kan du skriva över den här inställningen med hjälp av den ``--log-level`` alternativet. 

Tillgängliga loggningsnivåerna är: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, och `FATAL`.

## <a name="troubleshoot-issues"></a>Felsöka problem

AzCopy skapar log och planera filer för alla jobb. Du kan använda loggarna för att undersöka och felsöka eventuella problem. 

Loggarna innehåller statusen för fel (`UPLOADFAILED`, `COPYFAILED`, och `DOWNLOADFAILED`), den fullständiga sökvägen och orsaken till felet.

Som standard logg-och planen finns i den `%USERPROFILE\\.azcopy` mapp på Windows eller `$HOME\\.azcopy` mapp på Mac och Linux.

> [!IMPORTANT]
> När du skickar en begäran om att Microsoft Support (eller felsöka problem som rör tredje part), dela den redigerade versionen av kommandot som du vill köra. Detta säkerställer att Signaturen inte är delas av misstag med vem som helst. Du hittar den redigerade versionen i början av loggfilen.

### <a name="review-the-logs-for-errors"></a>Granska loggarna efter fel

Följande kommando får alla fel med `UPLOADFAILED` status från den `04dc9ca9-158f-7945-5933-564021086c79` loggen:

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Visa och återuppta jobb

Varje överföring åtgärden skapas ett jobb för AzCopy. Använd följande kommando för att visa historiken för jobb:

```
azcopy jobs list
```

Om du vill visa projektstatistik, använder du följande kommando:

```
azcopy jobs show <job-id>
```

Om du vill filtrera överföringar efter status, använder du följande kommando:

```
azcopy jobs show <job-id> --with-status=Failed
```

Använd följande kommando för att återuppta ett jobb som misslyckades/har avbrutits. Det här kommandot använder sin identifierare tillsammans med SAS-token som det är inte beständiga av säkerhetsskäl:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

När du återuppta ett jobb, söker AzCopy till jobbet plan-filen. Filen plan listar alla filer som har identifierats för bearbetning när jobbet skapades. När du återuppta ett jobb, görs AzCopy försök att överföra alla filer som anges i filen plan som inte redan överförts.