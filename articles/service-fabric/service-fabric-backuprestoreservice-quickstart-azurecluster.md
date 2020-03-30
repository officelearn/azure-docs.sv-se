---
title: Regelbunden säkerhetskopiering och återställning i Azure Service Fabric
description: Använd Service Fabric:s periodiska säkerhetskopierings- och återställningsfunktion för att aktivera periodisk säkerhetskopiering av dina programdata.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: f56fcb7d1dde700d954c3b55bcf8cd7759893521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259011"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Periodisk säkerhetskopiering och återställning i ett Azure Service Fabric-kluster
> [!div class="op_single_selector"]
> * [Kluster i Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Fristående kluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric är en distribuerad systemplattform som gör det enkelt att utveckla och hantera tillförlitliga, distribuerade mikrotjänstbaserade molnprogram. Det gör det möjligt att köra både statslösa och tillståndskänsliga mikrotjänster. Tillståndskänsliga tjänster kan upprätthålla föränderliga, auktoritära tillstånd utöver begäran och svar eller en fullständig transaktion. Om en tillståndskänslig tjänst går ner under en lång tid eller förlorar information på grund av en katastrof, kan den behöva återställas till någon nyligen säkerhetskopiering av sitt tillstånd för att fortsätta att tillhandahålla service efter att den kommer tillbaka upp.

Service Fabric replikerar tillståndet över flera noder för att säkerställa att tjänsten är högtillgängliga. Även om en nod i klustret misslyckas fortsätter tjänsten att vara tillgänglig. I vissa fall är det dock fortfarande önskvärt att servicedata är tillförlitliga mot bredare fel.
 
Tjänsten kanske till exempel vill säkerhetskopiera sina data för att skydda mot följande scenarier:
- I händelse av permanent förlust av ett helt Service Fabric-kluster.
- Permanent förlust av en majoritet av replikerna av en tjänstpartition
- Administrativa fel där tillståndet av misstag tas bort eller skadas. En administratör med tillräcklig behörighet tar till exempel bort tjänsten felaktigt.
- Buggar i tjänsten som orsakar dataskador. Detta kan till exempel inträffa när en uppgradering av servicekod börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kan både koden och uppgifterna behöva återställas till ett tidigare tillstånd.
- Bearbetning av offlinedata. Det kan vara praktiskt att ha offlinebearbetning av data för business intelligence som sker separat från den tjänst som genererar data.

Service Fabric tillhandahåller ett inbyggt API för att göra [tidpunkts säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md). Programutvecklare kan använda dessa API:er för att säkerhetskopiera tjänstens tillstånd med jämna mellanrum. Dessutom, om tjänstadministratörer vill utlösa en säkerhetskopia från utanför tjänsten vid en viss tidpunkt, som innan du uppgraderar programmet, utvecklare måste exponera säkerhetskopiering (och återställa) som ett API från tjänsten. Att underhålla säkerhetskopiorna är en extra kostnad utöver detta. Du kanske till exempel vill ta fem inkrementella säkerhetskopior varje halvtimme, följt av en fullständig säkerhetskopia. Efter den fullständiga säkerhetskopian kan du ta bort de tidigare inkrementella säkerhetskopiorna. Den här metoden kräver ytterligare kod som leder till extra kostnad under programutveckling.

Säkerhetskopierings- och återställningstjänsten i Service Fabric möjliggör enkel och automatisk säkerhetskopiering av information som lagras i tillståndskänsliga tjänster. Att säkerhetskopiera programdata regelbundet är grundläggande för att skydda mot dataförlust och otillgänglighet för tjänster. Service Fabric tillhandahåller en valfri säkerhetskopierings- och återställningstjänst, som gör att du kan konfigurera periodisk säkerhetskopiering av tillståndskänsliga reliable services (inklusive Actor Services) utan att behöva skriva någon ytterligare kod. Det underlättar också återställning av tidigaretagna säkerhetskopior. 


Service Fabric tillhandahåller en uppsättning API:er för att uppnå följande funktioner relaterade till periodisk säkerhetskopiering och återställningsfunktion:

- Schemalägga periodisk säkerhetskopiering av tillförlitliga tillståndskänsliga tjänster och tillförlitliga aktörer med stöd för att ladda upp säkerhetskopiering till (externa) lagringsplatser. Lagringsplatser som stöds
    - Azure Storage
    - Fildelning (lokalt)
- Räkna upp säkerhetskopior
- Utlösa en ad hoc-säkerhetskopia av en partition
- Återställa en partition med föregående säkerhetskopia
- Tillfälligt avbryta säkerhetskopior
- Bevarandehantering av säkerhetskopior (kommande)

## <a name="prerequisites"></a>Krav
* Service Fabric kluster med Fabric version 6.4 eller högre. Se den här [artikeln](service-fabric-cluster-creation-via-arm.md) för steg för att skapa Service Fabric-kluster med Hjälp av Azure-resursmall.
* X.509 Certifikat för kryptering av hemligheter som behövs för att ansluta till lagring för att lagra säkerhetskopior. Se [artikel](service-fabric-cluster-creation-via-arm.md) för att veta hur du skaffar eller skapar ett X.509-certifikat.
* Service Fabric Pålitlig tillståndskänsliga program byggd med Service Fabric SDK version 3.0 eller högre. För program som är inriktade på .NET Core 2.0 bör programmet byggas med Service Fabric SDK version 3.1 eller senare.
* Skapa Azure Storage-konto för lagring av programsäkerhetskopior.
* Installera Microsoft.ServiceFabric.Powershell.Http-modul [I förhandsversion] för att ringa konfigurationsanrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Kontrollera att Klustret `Connect-SFCluster` är anslutet med kommandot innan du gör någon konfigurationsbegäran med Microsoft.ServiceFabric.Powershell.Http-modulen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Aktivera säkerhetskopiering och återställning

### <a name="using-azure-portal"></a>Använda Azure Portal

Kryssrutan `Include backup restore service` Aktivera `+ Show optional settings` under `Cluster Configuration` fliken Aktivera.

![Aktivera återställningstjänst för säkerhetskopiering med portal][1]


### <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
Först måste du aktivera _säkerhetskopiering och återställning_ i klustret. Hämta mallen för det kluster som du vill distribuera. Du kan antingen använda [exempelmallarna](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) eller skapa en Resource Manager-mall. Aktivera _säkerhetskopierings- och återställningstjänsten_ med följande steg:

1. Kontrollera att `apiversion` resursen **`2018-02-01`** är `Microsoft.ServiceFabric/clusters` inställd på för resursen och uppdatera den enligt följande kodavsnitt om inte:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Nu aktivera _säkerhetskopiering och återställning_ `addonFeatures` tjänst `properties` genom att lägga till följande avsnitt under avsnitt som visas i följande utdrag: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurera X.509-certifikat för kryptering av autentiseringsuppgifter. Detta är viktigt för att säkerställa att autentiseringsuppgifterna som tillhandahålls för att ansluta till lagring krypteras innan de sparas. Konfigurera krypteringscertifikat genom `BackupRestoreService` att `fabricSettings` lägga till följande avsnitt under avsnittet som visas i följande kodavsnitt: 

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

4. När du har uppdaterat klustermallen med föregående ändringar använder du dem och låter distributionen/uppgraderingen slutföras. När du är klar börjar _säkerhetskopierings- och återställningstjänsten_ köras i klustret. Uri för denna `fabric:/System/BackupRestoreService` tjänst är och tjänsten kan placeras under systemservice avsnitt i Service Fabric explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Möjliggör periodisk säkerhetskopiering för tillförlitlig tillståndskänslig service och pålitliga aktörer
Låt oss gå igenom steg för att möjliggöra periodisk säkerhetskopiering för tillförlitlig tillståndskänslig service och tillförlitliga aktörer. Dessa steg förutsätter att
- Att klustret är inställt med X.509-säkerhet med _säkerhetskopiering och återställningstjänst_.
- En tillförlitlig tillståndskänslig tjänst distribueras i klustret. I den här snabbstartsguiden är `fabric:/SampleApp` ansökan Uri och Uri för tillförlitlig `fabric:/SampleApp/MyStatefulService`tillståndskänslig tjänst som tillhör denna applikation . Den här tjänsten distribueras med en enda `974bd92a-b395-4631-8a7f-53bd4ae9cf22`partition och partitions-ID är .
- Klientcertifikatet med administratörsrollen installeras i _Arkivet My_ (_Personal_) på den plats där skripten nedan ska anropas. _CurrentUser_ I det `1b7ebe2174649c45474a4819dafae956712c31d3` här exemplet används som tumavtryck för det här certifikatet. Mer information om klientcertifikat finns i [Rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Skapa princip för säkerhetskopiering

Första steget är att skapa säkerhetskopieringsprincip som beskriver säkerhetskopieringsschema, mållagring för säkerhetskopieringsdata, principnamn, maximala inkrementella säkerhetskopior som ska tillåtas innan fullständig säkerhetskopierings- och lagringsprincip utlöses för säkerhetskopieringslagring. 

För lagring av säkerhetskopiering använder du Azure Storage-kontot som skapats ovan. Behållaren `backup-container` är konfigurerad för att lagra säkerhetskopior. En behållare med det här namnet skapas, om den inte redan finns, under säkerhetskopieringsöverföring. Fyll `ConnectionString` i med en giltig anslutningssträng `account-name` för Azure Storage-kontot, ersätt med ditt lagringskontonamn och `account-key` med din lagringskontonyckel.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

Kör följande PowerShell-cmdlets för att skapa en ny säkerhetskopieringsprincip. Ersätt `account-name` med ditt lagringskontonamn och `account-key` med din lagringskontonyckel.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Vila samtal med PowerShell

Kör följande PowerShell-skript för att anropa krävs REST API för att skapa en ny princip. Ersätt `account-name` med ditt lagringskontonamn och `account-key` med din lagringskontonyckel.

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

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

1. I Service Fabric Explorer navigerar du till fliken Säkerhetskopior och väljer Åtgärder > Skapa principer för säkerhetskopiering.

    ![Skapa princip för säkerhetskopiering][6]

2. Fyll i informationen. För Azure-kluster bör AzureBlobStore väljas.

    ![Skapa Azure Blob-lagring för säkerhetskopieringsprincipen][7]

### <a name="enable-periodic-backup"></a>Aktivera periodisk säkerhetskopiering
När du har definierat säkerhetskopieringsprincipen för att uppfylla dataskyddskraven för programmet bör säkerhetskopieringsprincipen associeras med programmet. Beroende på behov kan säkerhetskopieringsprincipen associeras med ett program, en tjänst eller en partition.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Vila samtal med PowerShell

Kör följande PowerShell-skript för att anropa krävs `BackupPolicy1` REST API för `SampleApp`att associera säkerhetskopieringsprincip med namn som skapats i steget ovan med programmet .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

1. Välj ett program och gå till handling. Klicka på Aktivera/uppdatera säkerhetskopiering av program.

    ![Aktivera säkerhetskopiering av program][3]

2. Slutligen markerar du önskad princip och klickar på Aktivera säkerhetskopiering.

    ![Välj princip][4]


### <a name="verify-that-periodic-backups-are-working"></a>Kontrollera att periodiska säkerhetskopior fungerar

När du har aktiverat säkerhetskopiering på programnivå kommer alla partitioner som tillhör tillförlitliga tillståndskänsliga tjänster och tillförlitliga aktörer under programmet att börja få säkerhetskopiering med jämna mellanrum enligt den associerade säkerhetskopieringsprincipen. 

![Hälsohändelse för partitionens säkerhetskopierade säkerhetskopiering][0]

### <a name="list-backups"></a>Säkerhetskopior i listan

Säkerhetskopior som är associerade med alla partitioner som tillhör tillförlitliga tillståndskänsliga tjänster och tillförlitliga aktörer i programmet kan räknas upp med _GetBackups_ API. Säkerhetskopior kan räknas upp för ett program, en tjänst eller en partition.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Vila samtal med PowerShell

Kör följande PowerShell-skript för att anropa HTTP API för att räkna upp `SampleApp` de säkerhetskopior som skapats för alla partitioner i programmet.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

Om du vill visa säkerhetskopior i Service Fabric Explorer navigerar du till en partition och väljer fliken Säkerhetskopior.

![Räkna upp säkerhetskopior][5]

## <a name="limitation-caveats"></a>Begränsning/varningar
- PowerShell-cmdlets för service fabric är i förhandsgranskningsläge.
- Inget stöd för Service Fabric-kluster på Linux.

## <a name="next-steps"></a>Nästa steg
- [Förstå konfiguration av regelbunden säkerhetskopiering](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API-referens för reservåteråterställning](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png