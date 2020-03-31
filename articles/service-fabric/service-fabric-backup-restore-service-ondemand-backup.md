---
title: Säkerhetskopiering på begäran i Azure Service Fabric
description: Använd funktionen för säkerhetskopiering och återställning i Service Fabric för att säkerhetskopiera dina programdata på behovsbasis.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d5eada62bec49fe771373671e9438d2786d6b165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458419"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Säkerhetskopiering på begäran i Azure Service Fabric

Du kan säkerhetskopiera data från tillförlitliga tillståndskänsliga tjänster och tillförlitliga aktörer för att hantera katastrof- eller dataförlustscenarier.

Azure Service Fabric har funktioner för [regelbunden säkerhetskopiering av data](service-fabric-backuprestoreservice-quickstart-azurecluster.md) och säkerhetskopiering av data på behovsbasis. Säkerhetskopiering på begäran är användbar eftersom den skyddar mot/_dataförlustdataskador_ på grund av planerade ändringar i den underliggande tjänsten eller dess miljö. _data loss_

Säkerhetskopieringsfunktionerna på begäran är användbara för att samla in tillståndet för tjänsterna innan du manuellt utlöser en tjänst- eller tjänstmiljöåtgärd. Om du till exempel gör en ändring av servicebinärer när du uppgraderar eller nedgraderar tjänsten. I ett sådant fall kan säkerhetskopiering på begäran hjälpa till att skydda data mot korruption av programkodsfel.
## <a name="prerequisites"></a>Krav

- Installera Microsoft.ServiceFabric.Powershell.Http-modul [I förhandsversion] för att ringa konfigurationsanrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Kontrollera att Klustret `Connect-SFCluster` är anslutet med kommandot innan du gör någon konfigurationsbegäran med Microsoft.ServiceFabric.Powershell.Http-modulen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Utlösa säkerhetskopiering på begäran

Säkerhetskopiering på begäran kräver lagringsinformation för att ladda upp säkerhetskopieringsfiler. Du anger platsen för säkerhetskopiering på begäran, antingen i den periodiska säkerhetskopieringsprincipen eller i en begäran om säkerhetskopiering på begäran.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Säkerhetskopiering på begäran till lagring som anges av en periodisk säkerhetskopieringsprincip

Du kan konfigurera den periodiska säkerhetskopieringsprincipen så att den använder en partition med en tillförlitlig tillståndskänslig tjänst eller tillförlitlig aktör för extra säkerhetskopiering på begäran till lagring.

Följande fall är en fortsättning av scenariot i [Möjliggöra periodisk säkerhetskopiering för tillförlitlig tillståndskänslig service och tillförlitliga aktörer](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). I det här fallet aktiverar du en princip för säkerhetskopiering för att använda en partition och en säkerhetskopia sker med en viss frekvens i Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell

Använd [API:et för säkerhetskopieringpartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) för att konfigurera utlösare för säkerhetskopiering på begäran för partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Använd [Api:et GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) för att aktivera spårning för [säkerhetskopieringsförloppet på begäran](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Säkerhetskopiering på begäran till angiven lagring

Du kan begära säkerhetskopiering på begäran för en partition av en tillförlitlig tillståndskänslig tjänst eller tillförlitlig aktör. Ange lagringsinformation som en del av begäran om säkerhetskopiering på begäran.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell

Använd [API:et för säkerhetskopieringpartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) för att konfigurera utlösare för säkerhetskopiering på begäran för partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. Inkludera följande Azure Storage-information:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Du kan använda [Api:et GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) för att konfigurera spårning för [säkerhetskopieringsförloppet på begäran](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer
Kontrollera att avancerat läge har aktiverats i inställningarna för Service Fabric Explorer.
1. Välj önskade partitioner och klicka på Åtgärder. 
2. Välj Säkerhetskopiering av utlösarpartition och fyll i information för Azure:

    ![Säkerhetskopiering av utlösarpartition][0]

    eller FileShare:

    ![Starta säkerhetskopierar partitionsfildel][1]

## <a name="tracking-on-demand-backup-progress"></a>Spåra för säkerhetskopiering på begäran

En partition av en tillförlitlig tillståndskänslig tjänst eller pålitlig aktör accepterar endast en begäran om säkerhetskopiering på begäran åt gången. En annan begäran kan accepteras först efter att den aktuella säkerhetskopieringsbegäran på begäran har slutförts.

Olika partitioner kan utlösa begäranden om säkerhetskopiering på begäran samtidigt.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Begäran om säkerhetskopiering på begäran kan vara i följande lägen:

- **Accepterad**: Säkerhetskopieringen har startat på partitionen och pågår.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Lyckad,** **misslyckad**eller **Timeout**: En begärd säkerhetskopiering på begäran kan slutföras i något av följande tillstånd:
  - **Lyckades**: Ett _säkerhetskopieringstillstånd för lyckade_ åtgärder indikerar att partitionstillståndet har säkerhetskopierats. Svaret ger _BackupEpoch_ och _BackupLSN_ för partitionen tillsammans med tiden i UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Fel**: Ett _felsäkerhetstillstånd_ indikerar att ett fel uppstod under säkerhetskopieringen av partitionens tillstånd. Orsaken till felet anges som svar.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout**: Ett _timeout-säkerhetskopieringstillstånd_ indikerar att säkerhetskopieringen av partitionstillstånd inte kunde skapas under en viss tid. Standardtidsgränsen är 10 minuter. Initiera en ny begäran om säkerhetskopiering på begäran med större [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) i det här scenariot.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Nästa steg

- [Förstå periodisk säkerhetskopieringskonfiguration](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Rest-API-referens för BackupRestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png