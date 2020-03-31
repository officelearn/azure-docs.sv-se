---
title: Konfigurera, optimera och felsöka AzCopy med Azure Storage | Microsoft-dokument
description: Konfigurera, optimera och felsöka AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d2cb40d7510e46539db46bdb61ec2d64c0fd1ec7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526503"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurera, optimera och felsöka AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobbar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att utföra avancerade konfigurationsuppgifter och hjälper dig att felsöka problem som kan uppstå när du använder AzCopy.

> [!NOTE]
> Om du letar efter innehåll som hjälper dig att komma igång med AzCopy läser du någon av följande artiklar:
> - [Kom igång med AzCopy](storage-use-azcopy-v10.md)
> - [Överföra data med AzCopy- och bloblagring](storage-use-azcopy-blobs.md)
> - [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
> - [Överför data med AzCopy och Amazon S3 hinkar](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Om du vill konfigurera proxyinställningarna `https_proxy` för AzCopy ställer du in miljövariabeln. Om du kör AzCopy i Windows identifierar AzCopy proxyinställningar automatiskt, så du behöver inte använda den här inställningen i Windows. Om du väljer att använda den här inställningen i Windows åsidosätts automatisk identifiering.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | Använd i en kommandotolk:`set https_proxy=<proxy IP>:<proxy port>`<br> Användning i PowerShell:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **Macos** | `export https_proxy=<proxy IP>:<proxy port>` |

För närvarande stöder AzCopy inte proxyservrar som kräver autentisering med NTLM eller Kerberos.

## <a name="optimize-performance"></a>Optimera prestanda

Du kan jämföra prestanda och sedan använda kommandon och miljövariabler för att hitta en optimal avvägning mellan prestanda och resursförbrukning.

Det här avsnittet hjälper dig att utföra dessa optimeringsuppgifter:

> [!div class="checklist"]
> * Kör benchmark-tester
> * Optimera dataflödet
> * Optimera minnesanvändning 
> * Optimera filsynkronisering

### <a name="run-benchmark-tests"></a>Kör benchmark-tester

Du kan köra ett prestandariktmärkestest på specifika blob-behållare för att visa allmän prestandastatistik och identitetsprestandaflaskhalsar. 

> [!NOTE]
> I den aktuella versionen är den här funktionen endast tillgänglig för Blob Storage-behållare.

Använd följande kommando för att köra ett prestandariktmärkestest.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Exempel** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> I det här exemplet omsluter du banargument med enstaka citattecken (''). Använd enstaka citattecken i alla kommandoskal utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe) bifogar du sökvägsargument med dubbla citattecken ("") i stället för enstaka citattecken ('').

Det här kommandot kör ett prestandariktmärke genom att överföra testdata till ett angivet mål. Testdata genereras i minnet, överförs till målet och tas sedan bort från målet när testet är klart. Du kan ange hur många filer som ska genereras och vilken storlek du vill att de ska ha med hjälp av valfria kommandoparametrar.

Detaljerad referensdokument, se [askakopiskiva](storage-ref-azcopy-bench.md).

Om du vill visa detaljerad `azcopy bench -h` hjälpvägledning för det här kommandot skriver du och trycker sedan på RETUR.

### <a name="optimize-throughput"></a>Optimera dataflödet

Du kan `cap-mbps` använda flaggan i dina kommandon för att placera ett tak på datahastigheten för dataflödet. Följande kommando återupptar till exempel ett jobb `10` och caps-dataflöde till megabit (MB) per sekund. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Dataflödet kan minska när små filer överförs. Du kan öka dataflödet genom `AZCOPY_CONCURRENCY_VALUE` att ställa in miljövariabeln. Den här variabeln anger antalet samtidiga begäranden som kan uppstå.  

Om datorn har färre än 5 processorer anges värdet `32`för den här variabeln på . Annars är standardvärdet lika med 16 multiplicerat med antalet processorer. Det maximala standardvärdet för `3000`den här variabeln är , men du kan manuellt ange det här värdet högre eller lägre. 

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Macos** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Använd `azcopy env` för att kontrollera det aktuella värdet för den här variabeln. Om värdet är tomt kan du läsa vilket värde som används genom att titta i början av en AzCopy-loggfil. Det valda värdet och orsaken till att det valdes rapporteras där.

Innan du ställer in den här variabeln rekommenderar vi att du kör ett benchmark-test. Benchmark-testprocessen rapporterar det rekommenderade samtidighetsvärdet. Alternativt, om nätverksvillkoren och nyttolasten varierar, ställer `AUTO` du in den här variabeln på ordet i stället för till ett visst nummer. Det kommer att orsaka AzCopy att alltid köra samma automatiska tuning process som den använder i benchmark tester.

### <a name="optimize-memory-use"></a>Optimera minnesanvändning

Ange `AZCOPY_BUFFER_GB` att miljövariabeln ska ange den maximala mängden av systemminnet som du vill att AzCopy ska använda när du hämtar och laddar upp filer.
Uttryck detta värde i gigabyte (GB).

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **Macos** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Optimera filsynkronisering

[Synkroniseringskommandot](storage-ref-azcopy-sync.md) identifierar alla filer på målet och jämför sedan filnamn och senast ändrade tidsstämplar innan synkroniseringen påbörjas. Om du har ett stort antal filer kan du förbättra prestanda genom att eliminera den här direktbearbetningen. 

För att åstadkomma detta använder du [kommandot askakopkopia](storage-ref-azcopy-copy.md) i stället och ställer in flaggan på `--overwrite` `ifSourceNewer`. AzCopy kommer att jämföra filer som de kopieras utan att utföra några up-front skanningar och jämförelser. Detta ger en prestandakant i fall där det finns ett stort antal filer att jämföra.

Kommandot [askakopkopian tar](storage-ref-azcopy-copy.md) inte bort filer från målet, så om du vill ta bort filer på målet när de inte längre finns `prompt`vid källan använder du kommandot [akoposkopisynkronisering](storage-ref-azcopy-sync.md) med `--delete-destination` flaggan inställd på värdet `true` eller . 

## <a name="troubleshoot-issues"></a>Felsöka problem

AzCopy skapar logg- och planfiler för varje jobb. Du kan använda loggarna för att undersöka och felsöka eventuella problem. 

Loggarna innehåller status för fel`UPLOADFAILED` `COPYFAILED`( `DOWNLOADFAILED`, och ), den fullständiga sökvägen och orsaken till felet.

Som standard finns logg- och planfilerna i katalogen `%USERPROFILE%\.azcopy` på Windows eller `$HOME$\.azcopy` katalogen på Mac och Linux, men du kan ändra den platsen om du vill.

Det relevanta felet är inte nödvändigtvis det första felet som visas i filen. För fel som nätverksfel, timeout och server upptagen fel, azcopy kommer att försöka upp till 20 gånger och vanligtvis återförsök processen lyckas.  Det första felet som du ser kan vara något ofarligt som har gjorts på nytt.  Så istället för att titta på det första felet i `UPLOADFAILED`filen, leta efter de fel som är nära , `COPYFAILED`, eller `DOWNLOADFAILED`. 

> [!IMPORTANT]
> När du skickar en begäran till Microsoft Support (eller felsöker problemet som involverar tredje part) delar du den redigerade versionen av kommandot som du vill köra. Detta säkerställer att SAS inte av misstag delas med någon. Du hittar den redigerade versionen i början av loggfilen.

### <a name="review-the-logs-for-errors"></a>Granska loggarna för fel

Följande kommando hämtar alla fel `UPLOADFAILED` med `04dc9ca9-158f-7945-5933-564021086c79` status från loggen:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Visa och återuppta jobb

Varje överföringsåtgärd skapar ett AzCopy-jobb. Använd följande kommando för att visa jobbhistoriken:

```
azcopy jobs list
```

Om du vill visa jobbstatistiken använder du följande kommando:

```
azcopy jobs show <job-id>
```

Om du vill filtrera överföringarna efter status använder du följande kommando:

```
azcopy jobs show <job-id> --with-status=Failed
```

Använd följande kommando för att återuppta ett misslyckat/avbrutet jobb. Det här kommandot använder dess identifierare tillsammans med SAS-token eftersom den inte är beständig av säkerhetsskäl:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Bifoga sökvägsargument som SAS-token med enstaka citattecken (''). Använd enstaka citattecken i alla kommandoskal utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe) bifogar du sökvägsargument med dubbla citattecken ("") i stället för enstaka citattecken ('').

När du återupptar ett jobb tittar AzCopy på jobbplansfilen. I planfilen visas alla filer som identifierades för bearbetning när jobbet först skapades. När du återupptar ett jobb försöker AzCopy överföra alla filer som visas i planfilen och som inte redan har överförts.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Ändra platsen för planen och loggfilerna

Som standard finns plan- och `%USERPROFILE%\.azcopy` loggfiler i katalogen `$HOME$\.azcopy` i Windows eller i katalogen på Mac och Linux. Du kan ändra den här platsen.

### <a name="change-the-location-of-plan-files"></a>Ändra placeringen av planfiler

Använd något av dessa kommandon.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Macos** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Använd `azcopy env` för att kontrollera det aktuella värdet för den här variabeln. Om värdet är tomt skrivs planfiler till standardplatsen.

### <a name="change-the-location-of-log-files"></a>Ändra platsen för loggfiler

Använd något av dessa kommandon.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **Macos** | `export AZCOPY_LOG_LOCATION=<value>` |

Använd `azcopy env` för att kontrollera det aktuella värdet för den här variabeln. Om värdet är tomt skrivs loggar till standardplatsen.

## <a name="change-the-default-log-level"></a>Ändra standardloggnivån

Som standard är AzCopy-loggnivån inställd på `INFO`. Om du vill minska loggverbheten för att spara diskutrymme skriver ``--log-level`` du över den här inställningen med hjälp av alternativet. 

Tillgängliga loggnivåer `NONE`är: `INFO` `WARNING`, `ERROR` `DEBUG` `PANIC`, `FATAL`, , , och .

## <a name="remove-plan-and-log-files"></a>Ta bort plan- och loggfiler

Om du vill ta bort alla abonnemangs- och loggfiler `azcopy jobs clean` från den lokala datorn för att spara diskutrymme använder du kommandot.

Om du vill ta bort planen och `azcopy jobs rm <job-id>`loggfiler som är associerade med endast ett jobb använder du . Ersätt `<job-id>` platshållaren i det här exemplet med jobbets jobb-ID.


