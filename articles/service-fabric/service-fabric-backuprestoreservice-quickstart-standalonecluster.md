---
title: Regelbunden säkerhetskopiering och återställning i Azure Service Fabric (förhandsversion) | Microsoft Docs
description: Använd Service Fabric regelbunden säkerhetskopiering och återställning funktionen för att skydda dina program från förlust av data.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: cfbc0e6ca255bd005bb6e4cc381a9121347fe227
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206053"
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
* Service Fabric-kluster med infrastruktur version 6.2 och senare. Klustret måste konfigureras på Windows Server. Se [artikel](service-fabric-cluster-creation-for-windows-server.md) steg för att ladda ned paket som krävs.
* X.509-certifikat för kryptering av hemligheter som krävs för att ansluta till en lagringsplats för lagring av säkerhetskopior. Se [artikel](service-fabric-windows-cluster-x509-security.md) kunskaper om att hämta eller skapa ett självsignerat X.509-certifikat.
* Service Fabric tillförlitliga Stateful program som har skapats med hjälp av Service Fabric SDK version 3.0 eller senare. Core 2.0 för inriktat på .net-program, programmet bör skapas med hjälp av Service Fabric SDK version 3.1 eller senare.

## <a name="enabling-backup-and-restore-service"></a>Aktivera tjänsten för säkerhetskopiering och återställning
Först måste du aktivera den _säkerhetskopierar och återställer service_ i klustret. Hämta mallen för det kluster som du vill distribuera. Du kan använda den [exempel mallar](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Aktivera den _säkerhetskopierar och återställer service_ med följande steg:

1. Kontrollera att den `apiversion` är inställd på `10-2017` i klusterkonfigurationen filen och om inte, uppdatera det som visas i följande utdrag:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Aktivera nu den _säkerhetskopierar och återställer service_ genom att lägga till följande `addonFeatures` avsnittet `properties` avsnittet som visas i följande utdrag: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurera X.509-certifikat för kryptering av autentiseringsuppgifter. Det är viktigt att se till att autentiseringsuppgifterna, om någon, som ska ansluta till lagring krypteras innan beständighet. Konfigurera certifikat för kryptering genom att lägga till följande `BackupRestoreService` avsnittet `fabricSettings` avsnittet som visas i följande utdrag: 

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

4. När du har uppdaterat konfigurationsfilen kluster med föregående ändringarna, tillämpar dem och kan slutföra distributionen eller uppgradering. Kan den _säkerhetskopierar och återställer service_ börjar köras i klustret. Uri för den här tjänsten är `fabric:/System/BackupRestoreService` och tjänsten kan finnas under system service-avsnittet i Service Fabric explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivera regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig service och Reliable Actors
Låt oss gå igenom stegen för att aktivera regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig service och tillförlitlig aktörer. De här stegen förutsätter
- Att klustret har konfigurerats med _säkerhetskopierar och återställer service_.
- En tillförlitlig Stateful-tjänsten har distribuerats på klustret. I den här snabbstartsguide programmets Uri är `fabric:/SampleApp` och Uri för tillförlitlig tillståndskänslig service som hör till det här programmet är `fabric:/SampleApp/MyStatefulService`. Den här tjänsten har distribuerats med partition och partitions-ID är `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Skapa princip för säkerhetskopiering

Första steget är att skapa princip för säkerhetskopiering som beskriver schemat för säkerhetskopiering, mål-lagringskontot för säkerhetskopierade data, principnamn och maximala säkerhetskopior som ska tillåtas innan fullständig säkerhetskopiering. 

Skapa filresurs för lagring av säkerhetskopior, och ge ReadWrite åtkomst till filresursen för alla datorer i Service Fabric-noden. Det här exemplet förutsätter resursen med namnet `BackupStore` finns på `StorageServer`.

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

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Aktivera regelbunden säkerhetskopiering
Principen för säkerhetskopiering ska vara associerat med programmet när du har definierat princip för att uppfylla kraven på dataskydd för programmet. Beroende på krav, kan säkerhetskopieringsprincipen som associeras med ett program, tjänst eller en partition.

Kör följande PowerShell-skript för att anropa krävs REST API för att associera en princip för säkerhetskopiering med namnet `BackupPolicy1` i ovanstående steg med programmet `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Kontrollera att periodiska säkerhetskopieringar fungerar

När du har aktiverat säkerhetskopiering för programmet, startar alla partitioner som hör till tillförlitliga Stateful tjänster och Reliable Actors under programmet komma säkerhetskopierade med jämna mellanrum enligt den associera säkerhetskopieringsprincipen. 

![Partitionen BackedUp Hälsohändelse][0]

### <a name="list-backups"></a>Lista säkerhetskopieringar

Säkerhetskopior som är associerade med alla partitioner som hör till tillförlitliga Stateful tjänster och Reliable Actors av programmet kan räknas med _GetBackups_ API. Beroende på krav, kan du räkna upp säkerhetskopieringar för program, tjänst eller en partition.

Kör följande PowerShell-skript för att anropa HTTP-API för att räkna upp säkerhetskopior som har skapats för alla partitioner i den `SampleApp` program.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Exempel på utdata för ovanstående kör:

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

## <a name="preview-limitation-caveats"></a>Förhandsgranska begränsning / varningar
- Ingen Service Fabric inbyggda i PowerShell-cmdlets.
- Inget stöd för Service Fabric CLI.
- Inget stöd för automatisk säkerhetskopiering Rensa. Kräver manuell rensning av säkerhetskopior.
- Inget stöd för Service Fabric-kluster på Linux.

## <a name="next-steps"></a>Nästa steg
- [Återställningsverktyg REST API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

