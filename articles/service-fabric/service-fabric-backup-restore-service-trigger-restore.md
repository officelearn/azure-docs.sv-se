---
title: Återställning av säkerhetskopia i Azure Service Fabric | Microsoft Docs
description: Använd regelbunden säkerhetskopiering och återställning-funktionen i Service Fabric för att återställa data från en säkerhetskopia av dina programdata.
services: service-fabric
documentationcenter: .net
author: aagup
manager: chackdan
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: a82004fdd6bbb4eda0842670f210f846f9446384
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310884"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Återställning av säkerhetskopia i Azure Service Fabric

I Azure Service Fabric kan Reliable Stateful services och Reliable Actors underhålla ett föränderligt och auktoritärt tillstånd när en begäran och svar-transaktion har slutförts. En tillståndskänslig tjänst kan gå under en längre tid eller förlora information på grund av en katastrof. Om detta händer, måste tjänsten återställas från den senaste acceptabla säkerhetskopian så att den kan fortsätta att arbeta.

Du kan till exempel konfigurera en tjänst för att säkerhetskopiera data till att skydda mot följande scenarier:

- **Om haveriberedskap**: Permanent förlust av ett helt Service Fabric-kluster.
- **Om data går förlorade**: Permanent förlust av en majoritet av replikeringar av en tjänstpartition.
- **Om data går förlorade**: Oavsiktlig borttagning eller skadade data i tjänsten. En administratör tar till exempel felaktigt bort tjänsten.
- **Om data skadas**: Buggar i tjänsten orsaka skadade data. Till exempel kan skadade data inträffa när en kod tjänsteuppgraderingen skriver felaktiga data till en tillförlitlig samling. I sådana fall kanske du återställer koden och data till ett tidigare tillstånd.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Att aktivera en återställning på _fas (FAULT Analysis Service)_ måste vara aktiverat för klustret.
- Den _säkerhetskopiering återställa Service (BRS)_ skapade säkerhetskopian.
- Återställningen kan bara aktiveras på en partition.

## <a name="triggered-restore"></a>Utlösta återställning

En återställning kan aktiveras för någon av följande scenarier:

- Data återställa för _haveriberedskap_.
- Data återställa för _data skadas/dataförlust_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Återställningen av när det gäller haveriberedskap

Om ett helt Service Fabric-kluster har gått förlorade måste återställa du data för partitioner av tillförlitliga tillståndskänslig tjänst och Reliable Actors. Önskad säkerhetskopia kan väljas från listan över när du använder [GetBackupAPI med säkerhetskopiering lagringsinformation](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Uppräkningen säkerhetskopiering kan vara för ett program, en tjänst eller en partition.

I följande exempel förutsätter att gå förlorade klustret är i samma kluster som refereras till i [aktiverar regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). I det här fallet `SampleApp` distribueras med säkerhetskopiering principen är aktiverad, och säkerhetskopiorna som är konfigurerade för Azure Storage.

Köra ett PowerShell.skript för att använda REST-API för att returnera en lista över säkerhetskopior som har skapats för alla partitioner i den `SampleApp` program. API: et kräver lagring av säkerhetskopior information att lista tillgängliga säkerhetskopior.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Exempel på utdata för ovanstående kör:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Välj en av säkerhetskopieringar för att utlösa återställningen. Den aktuella säkerhetskopieringen för haveriberedskap kan till exempel vara följande säkerhetskopia:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

För restore-API måste du ange den _BackupId_ och _BackupLocation_ information.

Du måste också välja en målpartitionen i alternativa klustret enligt anvisningarna i den [partitionsschema](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Alternativa kluster säkerhetskopian återställs på partitionen som anges i partitionsschemat från det ursprungliga förlorad klustret.

Om partitions-ID på alternativa klustret är `1c42c47f-439e-4e09-98b9-88b8f60800c6`, kan du mappa den till det ursprungliga klustret partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` genom att jämföra den hög och låg nyckeln för _intervallet partitionering (UniformInt64Partition)_.

För _med namnet partitionering_, namnvärdet jämförs för att identifiera målpartitionen i alternativa kluster.

Du begär återställning mot säkerhetskopiering kluster partitionen med hjälp av följande [återställa API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Du kan följa förloppet för en återställning med TrackRestoreProgress.

### <a name="data-restore-for-data-corruptiondata-loss"></a>Data återställa för _skadade data_/_dataförlust_

För _dataförlust_ eller _skadade data_, säkerhetskopierade partitioner för tillförlitlig tillståndskänslig tjänst och Reliable Actors partitioner kan återställas till någon av de valda säkerhetskopiorna.

I följande exempel är en förlängning av [aktiverar regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). En princip för säkerhetskopiering har aktiverats för partitionen i det här exemplet och tjänsten gör säkerhetskopior med önskade intervall i Azure Storage.

Välj en säkerhetskopia från utdata från [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). I det här scenariot skapas säkerhetskopian från i samma kluster som innan.

Välj en säkerhetskopia i listan för att utlösa återställningen. För aktuellt _dataförlust_/_skadade data_, Välj säkerhetskopian som följande:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Restore-API ger den _BackupId_ och _BackupLocation_ information. Klustret har säkerhetskopiering aktiverad så Service Fabric _säkerhetskopiering återställa Service (BRS)_ identifierar rätt lagringsplatsen från den tillhörande säkerhetskopieringsprincipen.

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Du kan spåra förloppet för återställning med hjälp av TrackRestoreProgress.

## <a name="track-restore-progress"></a>Spåra återställning pågår

En partition av en tillförlitlig tillståndskänslig tjänst eller en Reliable Actor accepterar endast en restore-förfrågan i taget. En partition accepterar endast en annan begäran när den aktuella restore-begäran har slutförts. Flera restore-begäranden kan aktiveras på olika partitioner på samma gång.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Återställ begäran utvecklas i följande ordning:

1. **Godkänt**: En _godkända_ återställningstillstånd anger att den begärda partitionen har aktiverats med rätt begäranparametrar.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: En _InProgress_ återställningstillstånd anger att en återställning sker i partitionen med säkerhetskopian som anges i begäran. Partition-rapporter i _dataloss_ tillstånd.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Lyckade**, **fel**, eller **Timeout**: En begärda återställning kan utföras på något av följande tillstånd. Varje tillstånd har följande information om betydelse och svar:
    - **Success**: En _lyckades_ återställningstillstånd anger tillståndet återupprättats partition. Rapporter för partition _RestoredEpoch_ och _RestoredLSN_ tillstånd tillsammans med tiden i UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Fel**: En _fel_ återställningstillstånd anger fel på restore-förfrågan. Orsaken till felet har rapporterats.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Timeout**: En _Timeout_ återställningstillstånd anger att begäran har timeout. Skapa en ny begäran om återställning med större [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Standardvärdet för timeout är 10 minuter. Se till att partitionen inte är i ett tillstånd för förlust av data innan du begär restore igen.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatisk återställning

Du kan konfigurera tillförlitliga tillståndskänslig tjänst och partitioner för Reliable Actors i Service Fabric-kluster för _automatisk återställning_. I säkerhetskopieringspolicyn anger `AutoRestore` till _SANT_. Aktivera _automatisk återställning_ automatiskt återställer data från den senaste säkerhetskopian av partition när dataförlust rapporteras. Mer information finns i:

- [Automatisk återställning aktivering i Säkerhetskopieringsprincipen](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [RestorePartition API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Nästa steg
- [Förstå periodiska säkerhetskopieringskonfiguration](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API-referens för Backup restore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
