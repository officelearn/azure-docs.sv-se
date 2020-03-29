---
title: Återställa säkerhetskopiering i Azure Service Fabric
description: Använd funktionen för periodisk säkerhetskopiering och återställning i Service Fabric för att återställa data från en säkerhetskopia av programdata.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 1737102ee652cc2263bd0a908c1336bc93a6757b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377913"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Återställa säkerhetskopiering i Azure Service Fabric

I Azure Service Fabric kan tillförlitliga tillståndskänsliga tjänster och tillförlitliga aktörer upprätthålla ett föränderligt, auktoritärt tillstånd när en begäran och svarstransaktion har slutförts. En tillståndskänslig tjänst kan gå ner under en lång tid eller förlora information på grund av en katastrof. Om det händer måste tjänsten återställas från den senaste godtagbara säkerhetskopian så att den kan fortsätta fungera.

Du kan till exempel konfigurera en tjänst så att den säkerhetskopierar data för att skydda mot följande scenarier:

- **Händelse av haveriåterställning**: Permanent förlust av ett helt Service Fabric-kluster.
- **Dataförlust**: Permanent förlust av en majoritet av replikerna för en tjänstpartition.
- **Vid dataförlust**: Oavsiktlig radering eller skada av tjänsten. En administratör tar till exempel felaktigt bort tjänsten.
- **Fall av data korruption**: Buggar i tjänsten orsaka data korruption. Data kan till exempel inträffa när en uppgradering av tjänstkod skriver felaktiga data till en tillförlitlig samling. I så fall kan du behöva återställa både koden och data till ett tidigare tillstånd.

## <a name="prerequisites"></a>Krav

- För att utlösa en återställning måste _FAS (Fault Analysis Service)_ vara aktiverad för klustret.
- _Brs (Backup Restore Service)_ skapade säkerhetskopian.
- Återställningen kan bara utlösas vid en partition.
- Installera Microsoft.ServiceFabric.Powershell.Http-modul [I förhandsversion] för att ringa konfigurationsanrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Kontrollera att Klustret `Connect-SFCluster` är anslutet med kommandot innan du gör någon konfigurationsbegäran med Microsoft.ServiceFabric.Powershell.Http-modulen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Utlöst återställning

En återställning kan utlösas för något av följande scenarier:

- Dataåterställning för _haveriberedskap_.
- Dataåterställning för _data korruption/dataförlust_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Dataåterställning vid haveriberedskap

Om ett helt Service Fabric-kluster går förlorat kan du återställa data för partitionerna för tjänsten Reliable Stateful och Reliable Actors. Den önskade säkerhetskopian kan väljas från listan när du använder [GetBackupAPI med information om lagring för säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). Säkerhetskopieringen kan vara för ett program, en tjänst eller en partition.

Anta följande exempel att det förlorade klustret är samma kluster som refereras till i [Aktivera periodisk säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och tillförlitliga aktörer](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). I det `SampleApp` här fallet distribueras med säkerhetskopieringsprincipen aktiverad och säkerhetskopiorna är konfigurerade till Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell

Kör ett PowerShell-skript för att använda REST API för att returnera `SampleApp` en lista över de säkerhetskopior som skapats för alla partitioner i programmet. API:et kräver lagringsinformation för säkerhetskopiering för att kunna lista tillgängliga säkerhetskopior.

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

Exempel på utdata för ovanstående körning:

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

Om du vill utlösa återställningen väljer du en av säkerhetskopiorna. Den aktuella säkerhetskopian för haveriberedskap kan till exempel vara följande säkerhetskopia:

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

För återställnings-API:et måste du ange information om _BackupId_ och _BackupLocation._

Du måste också välja en målpartition i det alternativa klustret enligt beskrivningen i [partitionsschemat](service-fabric-concepts-partitioning.md#get-started-with-partitioning). Den alternativa klustersäkerhetskopieringen återställs till den partition som anges i partitionsschemat från det ursprungliga förlorade klustret.

Om partitions-ID:t `1c42c47f-439e-4e09-98b9-88b8f60800c6`i det alternativa klustret är `974bd92a-b395-4631-8a7f-53bd4ae9cf22` kan du mappa det till det ursprungliga klusterpartitions-ID:t genom att jämföra högnyckeln och lågnyckeln för _intervallpartitionering (UniformInt64Partition)_.

För _namngiven partitionering_jämförs namnvärdet för att identifiera målpartitionen i det alternativa klustret.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell

Du begär återställningen mot klusterpartitionen för säkerhetskopiering med hjälp av följande [Återställnings-API:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Du kan spåra förloppet för en återställning med TrackRestoreProgress.

### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer
Du kan utlösa en återställning från Service Fabric Explorer. Kontrollera att avancerat läge har aktiverats i inställningarna för Service Fabric Explorer.
1. Välj önskade partitioner och klicka på Åtgärder. 
2. Välj Återställning av utlösarpartition och fyll i information för Azure:

    ![Återställning av utlösarepartition][2]

    eller FileShare:

    ![Återställning av utlösare partitionsåterställning][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Dataåterställning för data _korruption_/_dataförlust_

För _dataförlust_ eller _dataskador_kan säkerhetskopierade partitioner för tillförlitlig tillståndskänslig tjänst och reliable actors-partitioner återställas till någon av de valda säkerhetskopiorna.

Följande exempel är en fortsättning [på Att möjliggöra periodisk säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och tillförlitliga aktörer](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). I det här exemplet är en princip för säkerhetskopiering aktiverad för partitionen och tjänsten gör säkerhetskopior med önskad frekvens i Azure Storage.

Välj en säkerhetskopia från utdata från [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). I det här fallet genereras säkerhetskopian från samma kluster som tidigare.

Om du vill utlösa återställningen väljer du en säkerhetskopia i listan. För skadade _dataförlustdata_/_data corruption_för aktuella data väljer du följande säkerhetskopia:

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

För återställnings-API:et anger du information om _BackupId_ och _BackupLocation._ Klustret har säkerhetskopiering aktiverat så _att BRS (Service_ Fabric Backup Restore Service) identifierar rätt lagringsplats från den associerade säkerhetskopieringsprincipen.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Du kan spåra återställningsförloppet med trackrestoreProgress.

## <a name="track-restore-progress"></a>Spåra återställningsstatus

En partition av en tillförlitlig tillståndskänslig tjänst eller tillförlitlig aktör accepterar bara en återställningsbegäran åt gången. En partition accepterar bara en annan begäran när den aktuella återställningsbegäran har slutförts. Flera återställningsbegäranden kan utlösas på olika partitioner samtidigt.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Återställningsbegäran fortskrider i följande ordning:

1. **Accepterad**: Ett _accepterat_ återställningstillstånd indikerar att den begärda partitionen har utlösts med korrekta parametrar för begäran.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: Ett _inprogress-återställningstillstånd_ indikerar att en återställning sker i partitionen med säkerhetskopian som nämns i begäran. Partitionen rapporterar _datalosstillståndet._
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Lyckades,** **misslyckades**eller **Timeout**: En begärd återställning kan slutföras i något av följande tillstånd. Varje tillstånd har följande signifikans- och svarsdetaljer:
    - **Framgång**: Ett _tillstånd för återställning av lyckade_ funktioner indikerar ett återfått partitionstillstånd. Partitionen rapporterar _RestoredEpoch_ och _RestoredLSN-lägena_ tillsammans med tiden i UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Fel**: Ett _tillstånd för återställning av fel_ indikerar fel på återställningsbegäran. Orsaken till felet rapporteras.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Timeout**: Ett _timeout-återställningstillstånd_ indikerar att begäran har timeout. Skapa en ny återställningsbegäran med större [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Standardtidsgränsen är 10 minuter. Kontrollera att partitionen inte är i ett dataförlusttillstånd innan du begär återställning igen.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatisk återställning

Du kan konfigurera reliable stateful service och Reliable Actors-partitioner i service fabric-klustret för _automatisk återställning_. I säkerhetskopieringsprincipen `AutoRestore` inställd på _true_. Om du aktiverar _automatisk återställning_ återställs data automatiskt från den senaste partitionssäkerhetskopieringen när dataförlust rapporteras. Mer information finns i:

- [Aktivera automatisk återställning i säkerhetskopieringsprincipen](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Api-referens för återställningpartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [Api-referens för GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Nästa steg
- [Förstå konfiguration av regelbunden säkerhetskopiering](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API-referens för reservåteråterställning](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png