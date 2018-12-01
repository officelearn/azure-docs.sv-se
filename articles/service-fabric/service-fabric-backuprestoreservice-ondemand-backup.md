---
title: Säkerhetskopiering på begäran i Azure Service Fabric | Microsoft Docs
description: Använd Service Fabric-säkerhetskopiering och återställning funktionen för säkerhetskopiering av programdata regelbundet behöver.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 2e83dcb4ce42a0c08ce482e9ffbbf1ac18634fd5
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730310"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Säkerhetskopiering på begäran i Azure Service Fabric

Data för tillståndskänsliga Reliable services och Reliable Actors kan säkerhetskopieras till adress katastrof eller data går förlorade.

Service Fabric är utrustad med funktioner för [regelbunden säkerhetskopiering av data](service-fabric-backuprestoreservice-quickstart-azurecluster.md) och säkerhetskopiering av data regelbundet behöver. Säkerhetskopiering på begäran är användbart eftersom det skyddar mot _dataförlust_/_skadade data_ orsakades på grund av planerade ändringar i den underliggande tjänsten eller dess miljö.

Funktionerna för säkerhetskopiering på begäran är användbar avbildning av tillståndet av tjänsterna, före en manuellt utlösta åtgärd som är relaterad till tjänsten eller service-miljö. Som ändrar service-binärfiler, det vill säga, uppgradera eller nedgradera av tjänsten; eftersom det skulle ha data skyddade mot att data skadas av programfel i programkoden.

## <a name="triggering-on-demand-backup"></a>Aktiverar säkerhetskopiering på begäran

Säkerhetskopiering på begäran kräver lagringsinformation för att ladda upp säkerhetskopior. Säkerhetskopiering på begäran sker i lagringen som angetts i princip för regelbunden säkerhetskopiering eller för den angivna lagringsenheten i begäran om säkerhetskopiering på begäran.

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>Säkerhetskopiering på begäran till lagring som anges av periodiska säkerhetskopieringsprincip

Partitionen av en tillförlitlig tillståndskänslig tjänst eller en Reliable Actor kan begäras för någon extra behöver basis säkerhetskopiering till lagring som anges i periodiska säkerhetskopieringsprincip. 

Nedan visas några exempel är fortsättningen på exemplet som anges i [aktiverar regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), där partitionen har en princip för säkerhetskopiering aktiverad och tar säkerhetskopiering med önskade intervall i Azure Storage.

Säkerhetskopiering på begäran för partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` kan utlösas av [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API. 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Den [på begäran-säkerhetskopiering](service-fabric-backuprestoreservice-ondemand-backup.md#tracking-on-demand-backup-progress) kan spåras av [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API.

### <a name="on-demand-backup-to-specified-storage"></a>Säkerhetskopiering på begäran till angiven storage

Säkerhetskopiering på begäran kan begäras för en partition av en tillförlitlig tillståndskänslig tjänst eller en Reliable Actor tillsammans med i informationen. I informationen måste anges som en del av begäran om säkerhetskopiering på begäran.

Säkerhetskopiering på begäran för partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` kan utlösas av [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API med Azure storage-information som visas nedan.

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

Den [på begäran-säkerhetskopiering](service-fabric-backuprestoreservice-ondemand-backup.md#tracking-on-demand-backup-progress) kan spåras av [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API.


## <a name="tracking-on-demand-backup-progress"></a>Spårning på begäran-säkerhetskopiering

En partition av en tillförlitlig tillståndskänslig tjänst eller en Reliable Actor accepterar endast en säkerhetskopiering på begäran-begäran i taget. En annan begäran kan accepteras endast när den aktuella säkerhetskopiering på begäran-begäran har slutförts. 

Flera säkerhetskopiering på begäran-begäranden kan aktiveras på olika partitioner på samma gång.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

På begäran säkerhetskopiering begärandets pågår kan vara något av följande tillstånd

* **Godkänt** -säkerhetskopieringen har påbörjats på partitionen och pågår.
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **Lyckades / misslyckades / Timeout** -A begär säkerhetskopiering på begäran kan utföras på något av följande tillstånd. Varje tillstånd har följande information om betydelse och svar.

    * **Lyckade** -säkerhetskopia som _lyckades_ anger att partition tillståndet säkerhetskopieras har. Svaret ger __BackupEpoch__ och __BackupLSN__ för partitionen tillsammans med tiden i UTC.
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **Fel** -säkerhetskopia som _fel_ anger felet uppstod under säkerhetskopieringen av partitionens tillstånd. Orsaken till felet anges i svaret.
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **Tidsgräns för** -säkerhetskopia som _Timeout_ anger att partition systemtillståndet gick inte att skapa i angivna tidsram; standardtidsgränsen är 10 minuter. Initierar en ny begäran om säkerhetskopiering med större [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) i begäran om säkerhetskopiering på begäran bör i det här scenariot.

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
- [Förstå periodiska säkerhetskopieringskonfiguration](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API-referens för Backup restore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

