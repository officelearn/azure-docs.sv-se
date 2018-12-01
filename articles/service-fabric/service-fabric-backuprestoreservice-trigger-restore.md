---
title: Återställning av säkerhetskopia i Azure Service Fabric | Microsoft Docs
description: Använd Service Fabric regelbunden säkerhetskopiering och återställning av funktionen för att återställa data från en säkerhetskopia av dina programdata.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d0b385e5856e85ad0d7d9baeaddbac0fb4230e3b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730283"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Återställning av säkerhetskopia i Azure Service Fabric


Reliable Stateful services och Reliable Actors i Service Fabric kan underhålla föränderligt och auktoritärt tillstånd bortom begäran och svaret eller hela transaktionen. Om en tillståndskänslig tjänst kraschar under en längre tid eller förlorar information på grund av en katastrof, kan det behöva återställas till senaste acceptabel säkerhetskopia av dess tillstånd för att kunna fortsätta att tillhandahålla tjänsten när det kommer tillbaka.

Tjänsten kan till exempel vill säkerhetskopiera dess data för att skydda mot följande scenarier:

- I händelse av permanent förlusten av en hela Service Fabric-kluster. **(Fall av Disaster Recovery – DR)**
- Permanent förlust av en majoritet av replikeringar av en tjänstpartition. **(Fall att data går förlorade)**
- Administrativa fel där tillståndet hämtar eller förstörs. En administratör med tillräcklig behörighet för tar till exempel felaktigt bort tjänsten. **(Fall att data går förlorade)**
- Buggar i tjänsten som orsaka skadade data. Till exempel kan skadade data inträffa när en kod tjänsteuppgraderingen börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kanske både koden och data återställas till ett tidigare tillstånd. **(Fall av skadade data)**


## <a name="prerequisites"></a>Förutsättningar
* Om du vill starta, återställa den _fas (FAULT Analysis Service)_ måste vara aktiverat för klustret
* Säkerhetskopian ska återställa bör har vidtagit _säkerhetskopiering återställa Service (BRS)_
* Återställningen kan bara aktiveras på en partition.

## <a name="triggering-restore"></a>Återställningen

Återställningen kan vara något av följande scenarier 
* Data återställa i händelse av _haveriberedskap_ (DR)
* Data återställa i händelse av _skadade data / förlust av data_



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>Data återställa i händelse av _haveriberedskap_ (DR)
I händelse av ett helt Service Fabric-kluster förlorad kan data för partitioner av tillförlitliga tillståndskänslig tjänst och Reliable Actors återställas till en annan kluster. Önskad säkerhetskopia kan väljas från uppräkning av [GetBackupAPI med säkerhetskopiering lagringsinformation](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Uppräkningen säkerhetskopiering kan vara för ett program, en tjänst eller en partition.

Vi antar att förlorad klustret har nämns i klustret [aktiverar regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), som hade `SampleApp` distribueras, där partitionen har med säkerhetskopiering principen är aktiverad och säkerhetskopiering händer i Azure Storage. 


Kör följande PowerShell-skript för att anropa REST-API för att räkna upp de säkerhetskopior som har skapats för alla partitioner i den `SampleApp` program i förlorad Service Fabric-kluster. Uppräkningen API kräver lagringinformationen var lagras säkerhetskopiorna av ett program, för att räkna upp tillgängliga säkerhetskopior. 

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



Vi måste välja önskad säkerhetskopia för att utlösa återställningen. Låt den önskade säkerhetskopieringen för den aktuella haveriberedskap (DR) vara följande säkerhetskopia

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

För restore-API måste vi ange det __BackupId__ och __BackupLocation__ information. Partitionen i alternativa klustret måste den valda enligt den [partitionsschema](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Ansvarar användaren att välja målpartitionen att återställa säkerhetskopian från alternativa klustret enligt partitionsschema i ursprungliga förlorad klustret.

Anta att partitions-ID på alternativa klustret är `1c42c47f-439e-4e09-98b9-88b8f60800c6`, som mappar till det ursprungliga klustret partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` genom att jämföra den hög och låg nyckeln för _intervallet partitionering (UniformInt64Partition)_.

För _med namnet partitionering_, namnvärdet jämförs för att identifiera målpartitionen i alternativa kluster.

Återställningen har begärts mot partition av säkerhetskopiering klustret med följande [återställa API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
Förloppet för återställningen kan vara [TrackRestoreProgress](service-fabric-backuprestoreservice-trigger-restore.md#tracking-restore-progress)

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>Data återställa i händelse av _skadade data / förlust av data_

Om _dataförlust_ eller _skadade data_ data för partitioner av tillförlitliga tillståndskänslig tjänst och Reliable Actors kan återställas till någon av de valda säkerhetskopiorna. Nedan visas några exempel är fortsättningen på exemplet som anges i [aktiverar regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), där partitionen har en princip för säkerhetskopiering aktiverad och tar säkerhetskopiering med önskade intervall i Azure Storage. 

Önskad säkerhetskopia väljs från utdata från [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). I det här scenariot skapas säkerhetskopian från samma kluster tidigare.
Vi måste välja önskad säkerhetskopia i listan för att utlösa återställningen. Låt vår önskade säkerhetskopiering för aktuellt _dataförlust_ / _skadade data_ inte följande säkerhetskopia

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

För restore-API måste vi ange det __BackupId__ och __BackupLocation__ information. Eftersom klustret säkerhetskopiering möjliggjort Service Fabric _säkerhetskopiering återställa Service (BRS)_ identifierar rätt lagringsplatsen från den tillhörande säkerhetskopieringsprincipen.

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Förloppet för återställningen kan vara [TrackRestoreProgress](service-fabric-backuprestoreservice-trigger-restore.md#tracking-restore-progress)


## <a name="tracking-restore-progress"></a>Spåra återställning pågår

En partition av en tillförlitlig tillståndskänslig tjänst eller en Reliable Actor accepterar endast en restore-förfrågan i taget. En annan begäran kan accepteras endast när den aktuella restore-begäran har slutförts. Flera restore-begäranden kan aktiveras på olika partitioner på samma gång.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

Återställ begäran utvecklas i följande ordning

1. __Godkända__ -återställningstillstånd som _godkända_ anger att den begärda har aktiverats med rätt begäranparametrar.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __InProgress__ -återställningstillstånd som _InProgress_ anger att partitionen genomgå en återställning med säkerhetskopian som anges i begäran. Partitionen rapporterar _dataloss_ tillstånd.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __Lyckade__/ __fel__/ __Timeout__ – en begärda återställning kan utföras på något av följande tillstånd. Varje tillstånd har följande information om betydelse och svar.
       
    * __Lyckade__ -återställningstillstånd som _lyckades_ anger tillståndet partition har återupprättats. Svaret ger RestoreEpoch och RestordLSN för partitionen tillsammans med tiden i UTC. 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __Fel__ -återställningstillstånd som _fel_ anger fel på restore-förfrågan. Orsaken till felet anges i begäran.
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __Tidsgräns för__ -återställningstillstånd som _Timeout_ anger begäran har timeout. Ny restore-förfrågan med större [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) är rekommenderas, som standard timeout-värdet är 10 minuter. Det är bäst att se till att partitionen är utanför tillstånd för förlust av data, innan du begär restore igen.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>Automatisk återställning

Partitionerna för tillförlitlig tillståndskänslig tjänst och Reliable Actors i Service Fabric-klustret kan konfigureras för _automatisk återställning_. När du skapar principen för säkerhetskopiering, principen kan ha `AutoRestore` inställd _SANT_.  Aktivera _automatisk återställning_ för en partition, återställer du data från den senaste säkerhetskopian om dataförlust rapporteras.
 
 [Automatisk återställning aktivering i Säkerhetskopieringsprincipen](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[RestorePartition API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[GetPartitionRestoreProgress API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Nästa steg
- [Förstå periodiska säkerhetskopieringskonfiguration](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API-referens för Backup restore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
