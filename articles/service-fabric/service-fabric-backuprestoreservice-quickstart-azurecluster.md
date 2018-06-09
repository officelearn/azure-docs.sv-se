---
title: Regelbunden säkerhetskopiering och återställning i Azure Service Fabric (förhandsversion) | Microsoft Docs
description: Använd Service Fabric regelbunden säkerhetskopiering och återställning funktionen för att skydda dina program från förlust av data.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: 73b5356f63199c7530fe5eef0c4b4b7ee617ff5f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236128"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Regelbunden säkerhetskopiering och återställning i Azure Service Fabric (förhandsgranskning)
> [!div class="op_single_selector"]
> * [Kluster på Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Fristående kluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric är en plattform för distribuerade system som gör det enkelt att utveckla och hantera tillförlitliga, distribuerade, mikrotjänster baserat molnprogram. Det tillåter körning av både tillståndslösa och tillståndskänsliga micro tjänster. Tillståndskänsliga tjänster kan underhålla föränderliga, auktoritär tillstånd utöver begäran och svar eller slutföra överföringen. Om en tillståndskänslig service ligger nere under lång tid eller förlorar information på grund av en katastrofåterställning, kan den behöva återställas till en aktuell säkerhetskopia av dess tillstånd för att kunna fortsätta att tillhandahålla tjänsten när den visas.

Service Fabric replikerar tillståndet över flera noder så att tjänsten är hög tillgänglighet. Även om en nod i klustret misslyckas, fortsätter tjänsten ska vara tillgängliga. I vissa fall, men är det fortfarande önskvärt för service-data för att tillförlitligt mot bredare fel.
 
Tjänsten kanske exempelvis vill säkerhetskopiera data för att skydda mot följande scenarier:
- Om en hel Service Fabric-klustret permanent förlorade.
- Permanent förlorade en majoritet av replikerna för en partition för tjänsten
- Administrativa fel där tillståndet hämtar eller förstörs. En administratör med tillräcklig behörighet för tar exempelvis felaktigt bort tjänsten.
- Programfel i tjänsten som kan orsakar att data skadas. Det kan till exempel hända när en tjänst koduppgradering startas felaktiga data skrivs till en tillförlitlig samling. I sådana fall kanske både koden och data återställas till ett tidigare tillstånd.
- Offline databearbetning. Det kan vara praktiskt att ha offline bearbetning av data för business intelligence som sker separat från den tjänst som genererar data.

Service Fabric ger en inbyggda API för att punkten i [säkerhetskopierar och återställer](service-fabric-reliable-services-backup-restore.md). Programutvecklare kan använda dessa API: er för att regelbundet säkerhetskopiera status för tjänsten. Dessutom om administratörer vill ska utlösa en säkerhetskopia från utanför tjänsten vid en viss tid, behöver som innan du uppgraderar programmet, utvecklare exponera säkerhetskopiering (och återställa) som en API från tjänsten. Det är en extra kostnad senare här för att upprätthålla säkerhetskopieringar. Du kanske vill vidta 5 inkrementella säkerhetskopieringar varje halvtimme följt av en fullständig säkerhetskopia. Efter en fullständig säkerhetskopiering kan du ta bort tidigare inkrementella säkerhetskopieringar. Den här metoden kräver ytterligare kod som leder till extra kostnad under programutvecklingen.

Säkerhetskopiering av programdata regelbundet krävs ett grundläggande för att hantera ett distribuerat program och skyddar mot förlust av data eller långvarig för tjänstetillgänglighet. Service Fabric tillhandahåller ett valfritt säkerhetskopiering och återställning tjänsten, där du kan konfigurera regelbunden säkerhetskopiering av tillståndskänsliga Reliable Services (inklusive Aktörstjänster) utan att behöva skriva ytterligare kod. Gör det också lättare återställa tidigare tagit säkerhetskopior. 

> [!NOTE]
> Periodiska funktionen för säkerhetskopiering och återställning är för närvarande i **Preview** och stöds inte för produktionsarbetsbelastningar. 
>

Service Fabric innehåller en uppsättning API: er för att uppnå följande funktioner relaterade till periodiska säkerhetskopia och återställa funktionen:

- Schemalägga regelbunden säkerhetskopiering av tillförlitliga Stateful tjänster och Reliable Actors med stöd för att överföra säkerhetskopiering till (externt) lagringsplatser. Stöds lagringsplatser
    - Azure Storage
    - Filresurs (lokalt)
- Räkna upp säkerhetskopieringar
- Utlös en ad hoc-säkerhetskopiering för en partition
- Återställa en partition med hjälp av en tidigare säkerhetskopiering
- Tillfälligt säkerhetskopieringar
- Kvarhållning hantering av säkerhetskopior (kommande)

## <a name="prerequisites"></a>Förutsättningar
* Service Fabric-kluster med infrastruktur version 6.2 och senare. Klustret måste konfigureras på Windows Server. Se [artikel](service-fabric-cluster-creation-via-arm.md) steg för att skapa Service Fabric-kluster med hjälp av Azure-resurs-mall.
* X.509-certifikat för kryptering av hemligheter som krävs för att ansluta till en lagringsplats för lagring av säkerhetskopior. Se [artikel](service-fabric-cluster-creation-via-arm.md) kunskap om att hämta eller skapa ett X.509-certifikat.
* Service Fabric tillförlitliga Stateful program som har skapats med hjälp av Service Fabric SDK version 3.0 eller senare. Core 2.0 för inriktat på .net-program, programmet bör skapas med hjälp av Service Fabric SDK version 3.1 eller senare.
* Skapa Azure Storage-konto för att lagra säkerhetskopior av programmet.

## <a name="enabling-backup-and-restore-service"></a>Aktivera tjänsten för säkerhetskopiering och återställning
Först måste du aktivera den _säkerhetskopierar och återställer service_ i klustret. Hämta mallen för det kluster som du vill distribuera. Du kan använda den [exempel mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) eller skapa en Resource Manager-mall. Aktivera den _säkerhetskopierar och återställer service_ med följande steg:

1. Kontrollera att den `apiversion` är inställd på **`2018-02-01`** för den `Microsoft.ServiceFabric/clusters` resursen, och om inte, uppdatera det som visas i följande utdrag:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Aktivera nu den _säkerhetskopierar och återställer service_ genom att lägga till följande `addonFeatures` avsnittet `properties` avsnittet som visas i följande utdrag: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurera X.509-certifikat för kryptering av autentiseringsuppgifter. Det här är viktigt att se till att autentiseringsuppgifterna för anslutning till lagring krypteras innan beständighet. Konfigurera certifikat för kryptering genom att lägga till följande `BackupRestoreService` avsnittet `fabricSettings` avsnittet som visas i följande utdrag: 

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

4. När du har uppdaterat mallen för kluster med föregående ändringarna, använda dem och låta Slutför distribution eller uppgradering. Kan den _säkerhetskopierar och återställer service_ börjar köras i klustret. Uri för den här tjänsten är `fabric:/System/BackupRestoreService` och tjänsten kan finnas under system service-avsnittet i Service Fabric explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivera regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig service och Reliable Actors
Låt oss gå igenom stegen för att aktivera regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig service och tillförlitlig aktörer. De här stegen förutsätter
- Att klustret har konfigurerats med X.509-säkerhet med _säkerhetskopierar och återställer service_.
- En tillförlitlig Stateful-tjänsten har distribuerats på klustret. I den här snabbstartsguide programmets Uri är `fabric:/SampleApp` och Uri för tillförlitlig tillståndskänslig service som hör till det här programmet är `fabric:/SampleApp/MyStatefulService`. Den här tjänsten har distribuerats med partition och partitions-ID är `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Klientcertifikatet med administratörsroll installeras i _min_ (_personliga_) lagrar namnet på _CurrentUser_ certifikat lagringsplats på datorn varifrån nedan skript kommer att anropas. Det här exemplet används `1b7ebe2174649c45474a4819dafae956712c31d3` som tumavtrycket för certifikatet. Mer information om certifikat finns [rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Skapa princip för säkerhetskopiering

Första steget är att skapa princip för säkerhetskopiering som beskriver schemat för säkerhetskopiering, mål-lagringskontot för säkerhetskopierade data, principnamn och maximala säkerhetskopior som ska tillåtas innan fullständig säkerhetskopiering. 

Använd Azure Storage konto skapade ovan för lagring av säkerhetskopior. Behållaren `backup-container` har konfigurerats för att lagra säkerhetskopior. En behållare med detta namn skapas om den inte redan finns vid säkerhetskopiering överföring. Fyll i `ConnectionString` med en giltig anslutningssträng för Azure Storage-konto ersätter `account-name` med namnet på ditt lagringskonto, och `account-key` med din lagringskontonyckel.

Kör följande PowerShell-skript för att anropa krävs REST API för att skapa en ny princip. Ersätt `account-name` med namnet på ditt lagringskonto, och `account-key` med din lagringskontonyckel.

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

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

### <a name="enable-periodic-backup"></a>Aktivera regelbunden säkerhetskopiering
Principen för säkerhetskopiering ska vara associerat med programmet när du har definierat säkerhetskopieringsprincip för att uppfylla kraven på dataskydd för programmet. Beroende på krav, kan säkerhetskopieringsprincipen som associeras med ett program, tjänst eller en partition.

Kör följande PowerShell-skript för att anropa krävs REST API för att associera en princip för säkerhetskopiering med namnet `BackupPolicy1` i ovanstående steg med programmet `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Kontrollera att periodiska säkerhetskopieringar fungerar

När du har aktiverat säkerhetskopiering på programnivå startar alla partitioner som hör till tillförlitliga Stateful tjänster och Reliable Actors under programmet komma säkerhetskopierade med jämna mellanrum enligt den associera säkerhetskopieringsprincipen. 

![Partitionen BackedUp Hälsohändelse][0]

### <a name="list-backups"></a>Lista säkerhetskopieringar

Säkerhetskopior som är associerade med alla partitioner som hör till tillförlitliga Stateful tjänster och Reliable Actors av programmet kan räknas med _GetBackups_ API. Säkerhetskopieringar kan räknas för ett program, tjänst eller en partition.

Kör följande PowerShell-skript för att anropa HTTP-API för att räkna upp säkerhetskopior som har skapats för alla partitioner i den `SampleApp` program.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

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

## <a name="preview-limitation-caveats"></a>Förhandsgranska begränsning / varningar
- Ingen Service Fabric inbyggda i PowerShell-cmdlets.
- Inget stöd för Service Fabric CLI.
- Inget stöd för automatisk säkerhetskopiering Rensa. Kräver manuell rensning av säkerhetskopior.
- Inget stöd för Service Fabric-kluster på Linux.

## <a name="next-steps"></a>Nästa steg
- [Återställningsverktyg REST API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

