---
title: Regelbunden säkerhetskopiering och återställning i Azure Service Fabric | Microsoft Docs
description: Använd Service Fabric regelbunden säkerhetskopiering och återställning av funktionen för att aktivera säkerhetskopiering av periodiska data för dina programdata.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: hrushib
ms.openlocfilehash: 9bce408215cef540604a72109bc5b29ebc3359e7
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413810"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Regelbunden säkerhetskopiering och återställning i Azure Service Fabric 
> [!div class="op_single_selector"]
> * [Kluster på Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Fristående kluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric är en distribuerad systemplattform som gör det enkelt att utveckla och hantera tillförlitliga, distribuerat mikrotjänster som bygger molnprogram. Det tillåter körning av både tillståndslösa och tillståndskänsliga mikrotjänster. Tillståndskänsliga tjänster kan underhålla föränderligt och auktoritärt tillstånd bortom begäran och svaret eller hela transaktionen. Om en tillståndskänslig tjänst kraschar under en längre tid eller förlorar information på grund av en katastrof, kan det behöva återställas till en aktuell säkerhetskopia av dess tillstånd för att kunna fortsätta att tillhandahålla tjänsten när den visas.

Service Fabric replikerar tillståndet över flera noder så att tjänsten har hög tillgänglighet. Även om en nod i klustret misslyckas, fortsätter tjänsten att vara tillgängliga. I vissa fall, men är det fortfarande önskvärt service-data kan vara tillförlitlig mot bredare fel.
 
Tjänsten kanske exempelvis vill säkerhetskopiera data för att skydda mot följande scenarier:
- I händelse av permanent förlusten av en hela Service Fabric-kluster.
- Permanent förlust av en majoritet av replikeringar av en tjänstpartition
- Administrativa fel där tillståndet hämtar eller förstörs. En administratör med tillräcklig behörighet för tar till exempel felaktigt bort tjänsten.
- Buggar i tjänsten som orsaka skadade data. Det kan exempelvis ske när en kod tjänsteuppgraderingen börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kanske både koden och data återställas till ett tidigare tillstånd.
- Offline databearbetning. Det kan vara praktiskt att ha offline bearbetning av data för business intelligence som sker separat från den tjänst som genererar data.

Service Fabric tillhandahåller ett inbyggda API som tidpunkt [säkerhetskopierar och återställer](service-fabric-reliable-services-backup-restore.md). Utvecklare kan använda dessa API: er för att säkerhetskopiera tillståndet för tjänsten med jämna mellanrum. Administratörer kan utlösa en säkerhetskopia från utanför tjänsten vid en viss tidpunkt måste som innan du uppgraderar program, utvecklare även att exponera säkerhetskopiering (och återställa) som ett API från tjänsten. Att underhålla säkerhetskopiorna som är en ytterligare kostnad över detta. Du kanske vill göra fem inkrementella säkerhetskopior varje halvtimme följt av en fullständig säkerhetskopia. När du har en fullständig säkerhetskopiering kan du ta bort tidigare inkrementella säkerhetskopior. Den här metoden kräver ytterligare kod som leder till extra kostnad under programutvecklingen.

Säkerhetskopiera och återställa tjänsten i Service Fabric kan enkelt och automatisk säkerhetskopiering av information som lagras i tillståndskänsliga tjänster. Säkerhetskopiering av programdata på regelbunden basis är grundläggande för skydd mot förlust och tjänsten otillgängliga för data. Service Fabric tillhandahåller ett valfritt säkerhetskopiering och återställning tjänst, där du kan konfigurera regelbunden säkerhetskopiering av tillståndskänsliga Reliable Services (inklusive Aktörstjänster) utan att behöva skriva ytterligare kod. Det underlättar också återställa utfört tidigare säkerhetskopior. 


Service Fabric tillhandahåller en uppsättning API: er för att uppnå följande funktioner relaterade till periodiska säkerhetskopia och återställa funktionen:

- Schemalägga regelbunden säkerhetskopiering av Reliable Stateful services och Reliable Actors med stöd för att ladda upp säkerhetskopiering till (extern) lagringsplatser. Stöds lagringsplatser
    - Azure Storage
    - Filresurs (lokalt)
- Räkna upp säkerhetskopior
- Utlös en ad hoc-säkerhetskopiering för en partition
- Återställa en partition med hjälp av föregående säkerhetskopia
- Tillfälligt säkerhetskopior
- Kvarhållning av säkerhetskopior (kommande)

## <a name="prerequisites"></a>Nödvändiga komponenter
* Service Fabric-kluster med Fabric version 6.2 och senare. Klustret ska ställas in på Windows Server. Referera till denna [artikeln](service-fabric-cluster-creation-via-arm.md) för steg för att skapa Service Fabric-kluster med Azure resource-mall.
* X.509-certifikat för kryptering av hemligheter som behövs för att ansluta till storage för lagring av säkerhetskopior. Se [artikeln](service-fabric-cluster-creation-via-arm.md) kunskaper om att hämta eller skapa ett X.509-certifikat.
* Service Fabric tillförlitliga tillståndskänsliga program som skapats med hjälp av Service Fabric SDK version 3.0 eller senare. För program som riktar in sig på .NET Core 2.0, bör programmet skapas med hjälp av Service Fabric SDK version 3.1 eller senare.
* Skapa Azure Storage-konto för att lagra säkerhetskopior av programmet.
* Installera Microsoft.ServiceFabric.Powershell.Http modulen [förhandsgranskning] för configuration anrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Se till att klustret är anslutna med hjälp av den `Connect-SFCluster` kommandot innan du gör några konfigurationsbegäran med Microsoft.ServiceFabric.Powershell.Http-modulen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Aktivera säkerhetskopiera och återställa tjänsten

### <a name="using-azure-portal"></a>Använda Azure Portal

Aktivera `Include backup restore service` kryssruta under `+ Show optional settings` i `Cluster Configuration` fliken.

![Aktivera Backup Restore-tjänsten med portalen][1]


### <a name="using-azure-resource-manager-template"></a>Med hjälp av Azure Resource Manager-mall
Först måste du aktivera den _säkerhetskopiera och återställa tjänsten_ i klustret. Hämta mallen för det kluster som du vill distribuera. Du kan använda den [exempel på mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) eller skapa en Resource Manager-mall. Aktivera den _säkerhetskopiera och återställa tjänsten_ med följande steg:

1. Kontrollerar du att den `apiversion` är inställd på **`2018-02-01`** för den `Microsoft.ServiceFabric/clusters` resursen, och om inte, uppdatera det som visas i följande kodavsnitt:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Nu kan du aktivera den _säkerhetskopiera och återställa tjänsten_ genom att lägga till följande `addonFeatures` avsnittet `properties` som visas i följande kodavsnitt: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurera X.509-certifikat för kryptering av autentiseringsuppgifter. Det här är viktigt att se till att autentiseringsuppgifterna för att ansluta till storage krypteras före beständig lagring. Konfigurera certifikat för kryptering genom att lägga till följande `BackupRestoreService` avsnittet `fabricSettings` som visas i följande kodavsnitt: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. När du har uppdaterat klustermallen med föregående ändringarna, tillämpar dem och låta Slutför distribution/uppgradering. När det är klart, den _säkerhetskopiera och återställa tjänsten_ börjar köras i klustret. Uri för den här tjänsten är `fabric:/System/BackupRestoreService` och tjänsten kan finnas under system service-avsnittet i Service Fabric explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivera regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors
Låt oss gå igenom stegen för att aktivera regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors. Dessa steg förutsätter
- Att klustret har konfigurerats med hjälp av X.509-säkerhet med _säkerhetskopiera och återställa tjänsten_.
- En tillförlitlig tillståndskänslig tjänst har distribuerats på klustret. I den här snabbstartsguiden programmets Uri är `fabric:/SampleApp` och URI: N för tillförlitlig tillståndskänslig tjänst som hör till det här programmet är `fabric:/SampleApp/MyStatefulService`. Den här tjänsten har distribuerats med partition och partitions-ID är `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Klientcertifikatet med administratörsrollen är installerat i _min_ (_personliga_) lagrar namnet på _CurrentUser_ certifikat lagringsplats på datorn varifrån nedan skript kommer att anropas. Det här exemplet används `1b7ebe2174649c45474a4819dafae956712c31d3` som tumavtrycket för certifikatet. Mer information om certifikat finns i [rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Skapa säkerhetskopieringsprincip

Första steget är att skapa princip för säkerhetskopiering som beskriver schemat för säkerhetskopiering, målstorleken för säkerhetskopierade data, principnamn, maximalt inkrementella säkerhetskopior som ska tillåtas innan du aktiverar fullständig säkerhetskopiering och kvarhållningsprincipen för lagring av säkerhetskopior. 

För lagring av säkerhetskopior, använder du Azure Storage-kontot skapats ovan. Behållaren `backup-container` har konfigurerats för att lagra säkerhetskopior. En behållare med detta namn skapas om den inte redan finns, under säkerhetskopiering överföringen. Fyll i `ConnectionString` med en giltig anslutningssträng för Azure Storage-konto där du ersätter `account-name` med namnet på ditt lagringskonto, och `account-key` med din lagringskontonyckel.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Microsoft.ServiceFabric.Powershell.Http-modulen

Kör följande PowerShell-cmdletar för att skapa en ny säkerhetskopieringsprincip. Ersätt `account-name` med namnet på ditt lagringskonto, och `account-key` med din lagringskontonyckel.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>REST-anrop med hjälp av PowerShell

Kör följande PowerShell-skript för att anropa den nödvändiga REST API för att skapa ny princip. Ersätt `account-name` med namnet på ditt lagringskonto, och `account-key` med din lagringskontonyckel.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

> [!IMPORTANT]
> Se till att kvarhållningsvaraktighetens i bevarandeprincipen är konfigurerad för att vara mindre än 24 dagar eller be om det skulle resultera i Backup Restore-tjänsten för att gå till kvorum förlust efter replikeringsredundans på grund av ett problem i körningen.

### <a name="enable-periodic-backup"></a>Aktivera regelbunden säkerhetskopiering
Principen för säkerhetskopiering ska vara associerat med programmet när du har definierat principen för säkerhetskopiering för att uppfylla kraven på dataskydd för programmet. Beroende på krav, kan säkerhetskopieringspolicyn som associeras med ett program, tjänst eller en partition.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Microsoft.ServiceFabric.Powershell.Http-modulen

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>REST-anrop med hjälp av PowerShell

Kör följande PowerShell-skript för att anropa den nödvändiga REST API för att associera säkerhetskopieringspolicyn med namnet `BackupPolicy1` i ovanstående steg med programmet `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Kontrollera att regelbundna säkerhetskopieringar fungerar

När du har aktiverat säkerhetskopiering på programnivå, startas alla partitioner som hör till Reliable Stateful services och Reliable Actors under programmet komma säkerhetskopieras regelbundet enligt den tillhörande säkerhetskopieringsprincipen. 

![Partition säkerhetskopierade Hälsotillståndshändelse][0]

### <a name="list-backups"></a>Lista över säkerhetskopior

Säkerhetskopieringar som är associerade med alla partitioner som hör till Reliable Stateful services och Reliable Actors för programmet kan att räkna upp med hjälp av _GetBackups_ API. Säkerhetskopieringar kan räknas upp för ett program, tjänst eller en partition.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Microsoft.ServiceFabric.Powershell.Http-modulen

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>REST-anrop med hjälp av PowerShell

Kör följande PowerShell-skript för att anropa HTTP-API för att räkna upp de säkerhetskopior som har skapats för alla partitioner i den `SampleApp` program.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

## <a name="limitation-caveats"></a>Begränsningen / varningar
- Service Fabric PowerShell-cmdlet: ar är i förhandsgranskningsläge.
- Inget stöd för Service Fabric-kluster på Linux.

## <a name="known-issues"></a>Kända problem
- Se till att kvarhållningsvaraktighetens är konfigurerad för att vara mindre än 24 dagar. 


## <a name="next-steps"></a>Nästa steg
- [Förstå periodiska säkerhetskopieringskonfiguration](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API-referens för Backup restore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png

