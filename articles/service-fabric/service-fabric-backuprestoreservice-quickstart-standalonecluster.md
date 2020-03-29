---
title: Periodisk säkerhetskopiering/återställning i fristående Azure Service Fabric
description: Använd Service Fabric:s periodiska säkerhetskopierings- och återställningsfunktion för att aktivera periodisk säkerhetskopiering av dina programdata.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 938cbbde9f53c52350ef64715f6c61c4aa961057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526251"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Periodisk säkerhetskopiering och återställning i ett fristående servicetyg
> [!div class="op_single_selector"]
> * [Kluster i Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Fristående kluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric är en distribuerad systemplattform som gör det enkelt att utveckla och hantera tillförlitliga, distribuerade mikrotjänstbaserade molnprogram. Det gör det möjligt att köra både statslösa och tillståndskänsliga mikrotjänster. Tillståndskänsliga tjänster kan upprätthålla föränderliga, auktoritära tillstånd utöver begäran och svar eller en fullständig transaktion. Om en tillståndskänslig tjänst går ner under en lång tid eller förlorar information på grund av en katastrof, kan den behöva återställas till någon nyligen säkerhetskopiering av sitt tillstånd för att fortsätta att tillhandahålla service efter att den kommer tillbaka upp.

Service Fabric replikerar tillståndet över flera noder för att säkerställa att tjänsten är högtillgängliga. Även om en nod i klustret misslyckas fortsätter tjänsten att vara tillgänglig. I vissa fall är det dock fortfarande önskvärt att servicedata är tillförlitliga mot bredare fel.
 
En tjänst kanske till exempel vill säkerhetskopiera sina data för att skydda mot följande scenarier:
- Permanent förlust av ett helt Service Fabric-kluster.
- Permanent förlust av en majoritet av replikerna av en tjänstpartition
- Administrativa fel där tillståndet av misstag tas bort eller skadas. En administratör med tillräcklig behörighet tar till exempel bort tjänsten felaktigt.
- Buggar i tjänsten som orsakar dataskador. Detta kan till exempel inträffa när en uppgradering av servicekod börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kan både koden och uppgifterna behöva återställas till ett tidigare tillstånd.
- Bearbetning av offlinedata. Det kan vara praktiskt att ha offlinebearbetning av data för business intelligence som sker separat från den tjänst som genererar data.

Service Fabric tillhandahåller ett inbyggt API för att göra [tidpunkts säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md). Programutvecklare kan använda dessa API:er för att säkerhetskopiera tjänstens tillstånd med jämna mellanrum. Dessutom, om tjänstadministratörer vill utlösa en säkerhetskopia från utanför tjänsten vid en viss tidpunkt, som innan du uppgraderar programmet, utvecklare måste exponera säkerhetskopiering (och återställa) som ett API från tjänsten. Att underhålla säkerhetskopiorna är en extra kostnad utöver detta. Du kanske till exempel vill ta fem inkrementella säkerhetskopior varje halvtimme, följt av en fullständig säkerhetskopia. Efter den fullständiga säkerhetskopian kan du ta bort de tidigare inkrementella säkerhetskopiorna. Den här metoden kräver ytterligare kod som leder till extra kostnad under programutveckling.

Säkerhetskopiering av programdata regelbundet är ett grundläggande behov av att hantera ett distribuerat program och skydda mot förlust av data eller långvarig förlust av tjänstens tillgänglighet. Service Fabric tillhandahåller en valfri säkerhetskopierings- och återställningstjänst, som gör att du kan konfigurera periodisk säkerhetskopiering av tillståndskänsliga reliable services (inklusive Actor Services) utan att behöva skriva någon ytterligare kod. Det underlättar också återställning av tidigaretagna säkerhetskopior. 

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
* Service Fabric kluster med Fabric version 6.4 eller högre. Se den här [artikeln](service-fabric-cluster-creation-for-windows-server.md) för steg för att hämta krävs paket.
* X.509 Certifikat för kryptering av hemligheter som behövs för att ansluta till lagring för att lagra säkerhetskopior. Se [artikel](service-fabric-windows-cluster-x509-security.md) för att veta hur du hämtar eller skapar ett självsignerat X.509-certifikat.

* Service Fabric Pålitlig tillståndskänsliga program byggd med Service Fabric SDK version 3.0 eller högre. För program som är inriktade på .Net Core 2.0 bör programmet byggas med Service Fabric SDK version 3.1 eller senare.
* Installera Microsoft.ServiceFabric.Powershell.Http-modul [I förhandsversion] för att ringa konfigurationsanrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Kontrollera att Klustret `Connect-SFCluster` är anslutet med kommandot innan du gör någon konfigurationsbegäran med Microsoft.ServiceFabric.Powershell.Http-modulen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Aktivera säkerhetskopiering och återställning
Först måste du aktivera _säkerhetskopiering och återställning_ i klustret. Hämta mallen för det kluster som du vill distribuera. Du kan använda [exempelmallarna](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Aktivera _säkerhetskopierings- och återställningstjänsten_ med följande steg:

1. Kontrollera att `apiversion` den `10-2017` är inställd på i klusterkonfigurationsfilen och uppdatera den enligt följande kodavsnitt om inte, och uppdatera den enligt följande kodavsnitt:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Nu aktivera _säkerhetskopiering och återställning_ `addonFeatures` tjänst `properties` genom att lägga till följande avsnitt under avsnitt som visas i följande utdrag: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurera X.509-certifikat för kryptering av autentiseringsuppgifter. Detta är viktigt för att säkerställa att de autentiseringsuppgifter som tillhandahålls, om sådana finns, för att ansluta till lagring krypteras innan de sparas. Konfigurera krypteringscertifikat genom `BackupRestoreService` att `fabricSettings` lägga till följande avsnitt under avsnittet som visas i följande kodavsnitt: 

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

4. När du har uppdaterat klusterkonfigurationsfilen med föregående ändringar använder du dem och låter distributionen/uppgraderingen slutföras. När du är klar börjar _säkerhetskopierings- och återställningstjänsten_ köras i klustret. Uri för denna `fabric:/System/BackupRestoreService` tjänst är och tjänsten kan placeras under systemservice avsnitt i Service Fabric explorer. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Möjliggör periodisk säkerhetskopiering för tillförlitlig tillståndskänslig service och pålitliga aktörer
Låt oss gå igenom steg för att möjliggöra periodisk säkerhetskopiering för tillförlitlig tillståndskänslig service och tillförlitliga aktörer. Dessa steg förutsätter att
- Att klustret har konfigurerats med _säkerhetskopierings- och återställningstjänst_.
- En tillförlitlig tillståndskänslig tjänst distribueras i klustret. I den här snabbstartsguiden är `fabric:/SampleApp` ansökan Uri och Uri för tillförlitlig `fabric:/SampleApp/MyStatefulService`tillståndskänslig tjänst som tillhör denna applikation . Den här tjänsten distribueras med en enda `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`partition och partitions-ID är .  

### <a name="create-backup-policy"></a>Skapa princip för säkerhetskopiering

Första steget är att skapa säkerhetskopieringsprincip som beskriver säkerhetskopieringsschema, mållagring för säkerhetskopieringsdata, principnamn, maximala inkrementella säkerhetskopior som ska tillåtas innan fullständig säkerhetskopierings- och lagringsprincip utlöses för säkerhetskopieringslagring. 

För säkerhetskopieringslagring skapar du filresurs och ger ReadWrite åtkomst till den här filresursen för alla Service Fabric-nodddmaskiner. Det här exemplet förutsätter `BackupStore` att `StorageServer`resursen med namn finns på .


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell

Kör följande PowerShell-skript för att anropa krävs REST API för att skapa en ny princip.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
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
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

1. I Service Fabric Explorer navigerar du till fliken Säkerhetskopior och väljer Åtgärder > Skapa principer för säkerhetskopiering.

    ![Skapa princip för säkerhetskopiering][6]

2. Fyll i informationen. För fristående kluster bör FileShare väljas.

    ![Skapa fildelning för säkerhetskopieringsprincip][7]

### <a name="enable-periodic-backup"></a>Aktivera periodisk säkerhetskopiering
När du har definierat principen för att uppfylla dataskyddskraven för programmet bör säkerhetskopieringsprincipen associeras med programmet. Beroende på behov kan säkerhetskopieringsprincipen associeras med ett program, en tjänst eller en partition.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell
Kör följande PowerShell-skript för att anropa krävs `BackupPolicy1` REST API för `SampleApp`att associera säkerhetskopieringsprincip med namn som skapats i steget ovan med programmet .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Använda Service Fabric Explorer

1. Välj ett program och gå till handling. Klicka på Aktivera/uppdatera säkerhetskopiering av program.

    ![Aktivera säkerhetskopiering av program][3] 

2. Slutligen markerar du önskad princip och klickar på Aktivera säkerhetskopiering.

    ![Välj princip][4]

### <a name="verify-that-periodic-backups-are-working"></a>Kontrollera att periodiska säkerhetskopior fungerar

När du har aktiverat säkerhetskopiering för programmet kommer alla partitioner som tillhör tillförlitliga tillståndskänsliga tjänster och tillförlitliga aktörer under programmet att börja få säkerhetskopiering med jämna mellanrum enligt den associerade säkerhetskopieringsprincipen.

![Hälsohändelse för partitionens säkerhetskopierade säkerhetskopiering][0]

### <a name="list-backups"></a>Säkerhetskopior i listan

Säkerhetskopior som är associerade med alla partitioner som tillhör tillförlitliga tillståndskänsliga tjänster och tillförlitliga aktörer i programmet kan räknas upp med _GetBackups_ API. Beroende på behov kan säkerhetskopiorna räknas upp för program, tjänst eller en partition.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell med Hjälp av Microsoft.ServiceFabric.Powershell.Http-modul

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Vila samtal med Powershell

Kör följande PowerShell-skript för att anropa HTTP API för att räkna upp `SampleApp` de säkerhetskopior som skapats för alla partitioner i programmet.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Exempel på utdata för ovanstående körning:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
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

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png