---
title: Konfigurera Azure Service Fabric Reliable Services | Microsoft Docs
description: Lär dig mer om hur du konfigurerar tillståndskänsliga Reliable Services i Azure Service Fabric.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: ef7b012c96522f15bab230475a97681945592d59
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728598"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurera tillståndskänsliga reliable services
Det finns två uppsättningar med konfigurationsinställningarna för reliable services. En uppsättning är globala för alla reliable services i klustret medan den andra uppsättningen är specifik för en viss tillförlitlig tjänst.

## <a name="global-configuration"></a>Global konfiguration
Global reliable Services-konfiguration har angetts i klustermanifestet för klustret under avsnittet KtlLogger. Det tillåter konfiguration av delade loggens plats och storlek samt gränserna för globalt minne som används av loggaren. Klustermanifestet är en enkel XML-fil som innehåller inställningar och konfigurationer som gäller för alla noder och tjänster i klustret. Filen kallas vanligtvis ClusterManifest.xml. Du kan se klustret manifest för klustret med hjälp av powershell-kommandot Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minsta antal KB att allokera i kernelläge för loggaren skrivning buffertpooltillägget minne. Den här minnespoolen används för att cachelagra tillståndsinformationen innan skrivning till disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobyte |Obegränsat |Maximal storlek som loggaren skriva bufferten minnespoolen kan växa. |
| SharedLogId |GUID |"" |Anger ett unikt GUID för att använda för att identifiera delade Standardloggfilen används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogId i deras specifika tjänstekonfigurationen. Om SharedLogId anges måste även SharedLogPath anges. |
| SharedLogPath |Fullständig sökväg |"" |Anger den fullständigt kvalificerade sökvägen där loggfilen delade används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogPath i deras specifika tjänstekonfigurationen. Men om SharedLogPath anges måste sedan SharedLogId också anges. |
| SharedLogSizeInMB |Megabyte |8192 |Anger antalet MB diskutrymme att allokera statiskt för delade loggen. Värdet måste vara 2048 eller större. |

Exemplet nedan visar hur du ändrar delade transaktionsloggen som skapas för att säkerhetskopiera alla tillförlitliga samlingar för tillståndskänsliga tjänster i Azure ARM eller lokala JSON-mall.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Exemplet lokala developer kluster manifest-delen
Om du vill ändra detta på din lokala utvecklingsmiljö, måste du redigera filen lokala clustermanifest.xml.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Kommentarer
Loggaren har en global pool av allokerat minne från icke växlade kernelminne som är tillgängliga för alla tillförlitliga tjänster på en nod för cachelagring tillståndsdata innan skrivs in i dedikerade som är associerade med reliable Services-repliken. Poolstorleken styrs av inställningarna WriteBufferMemoryPoolMinimumInKB och WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB anger storleken på den här minnespoolen och lägsta storleken som minnespoolen kan minska. WriteBufferMemoryPoolMaximumInKB är den högsta storleken som minnespoolen kan växa. Varje replik för tillförlitlig tjänst som är öppen kan öka storleken på minnespoolen med ett system som bestäms belopp upp till WriteBufferMemoryPoolMaximumInKB. Om det finns flera begäran för minne från minnespoolen än vad som är tillgängliga, ska begäranden för minne fördröjas tills minne är tillgänglig. Därför om skrivna minne buffertpooltillägget är för liten för en specifik konfiguration sjunka sedan prestanda.

Inställningar för SharedLogId och SharedLogPath används alltid tillsammans att definiera GUID och plats för delade standardloggen för alla noder i klustret. Delade Standardloggen används för alla tillförlitliga tjänster som inte anger inställningarna i settings.xml för den specifika tjänsten. För bästa prestanda bör delade loggfilerna placeras på diskar som används enbart för delade loggfilen för att minska konkurrensen.

SharedLogSizeInMB Anger mängden diskutrymme för att Förallokera för delade standardloggen på alla noder.  SharedLogId och SharedLogPath behöver inte anges för SharedLogSizeInMB anges.

## <a name="service-specific-configuration"></a>Specifika tjänstkonfiguration
Du kan ändra standardkonfigurationer för tillståndskänsliga Reliable Services med hjälp av konfigurationspaketet (Config) eller service-implementeringen (kod).

* **Config** -konfiguration via konfigurationspaketet åstadkoms genom att ändra filen Settings.xml som genereras i Microsoft Visual Studio-paketrot Config-mappen för varje tjänst i programmet.
* **Kod** -konfiguration via kod åstadkoms genom att skapa en ReliableStateManager med ett ReliableStateManagerConfiguration-objekt med lämpliga inställningar.

Som standard, Azure Service Fabric-körningen söker efter fördefinierade avsnittsnamn i filen Settings.xml och förbrukar konfigurationsvärdena när du skapar de underliggande runtime-komponenterna.

> [!NOTE]
> Gör **inte** ta bort Avsnittsnamnen på de följande konfigurationerna i filen Settings.xml som genereras i Visual Studio-lösning om du planerar att konfigurera din tjänst via kod.
> Byta namn på paketet eller ett avsnitt konfigurationsnamn kräver en kodändring när du konfigurerar ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Replikator säkerhetskonfiguration
Replikator säkerhetskonfigurationer används för att skydda kommunikationskanalen som används under replikering. Det innebär att tjänster inte kan se varandras replikeringstrafik, säkerställer att de data som görs med hög tillgänglighet är säker. Som standard förhindrar en tom security konfigurationsavsnittet replikeringssäkerhet.

> [!IMPORTANT]
> På Linux-noder vara certifikat PEM-formaterade. Läs mer om att hitta och konfigurera certifikat för Linux i [konfigurerar du certifikat i Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Standardnamnet för avsnittet
ReplicatorSecurityConfig

> [!NOTE]
> Om du vill ändra namn på det här avsnittet, åsidosätter du parametern replicatorSecuritySectionName till konstruktorn ReliableStateManagerConfiguration när du skapar ReliableStateManager för den här tjänsten.
> 
> 

### <a name="replicator-configuration"></a>Replikator konfiguration
Replikator konfigurationer konfigurera replikatorn som ansvarar för att göra den tillståndskänslig tillförlitlig tjänst tillstånd genom att replikera och spara tillståndet lokalt mycket pålitlig.
Standardkonfigurationen genereras av Visual Studio-mallen och bör vara tillräckligt. Det här avsnittet innehåller information om ytterligare konfigurationer som är tillgängliga att finjustera replikatorn.

### <a name="default-section-name"></a>Standardnamnet för avsnittet
ReplicatorConfig

> [!NOTE]
> Om du vill ändra namn på det här avsnittet, åsidosätter du parametern replicatorSettingsSectionName till konstruktorn ReliableStateManagerConfiguration när du skapar ReliableStateManager för den här tjänsten.
> 
> 

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0.015 |Tidsperiod som replikatorn på den sekundära väntar efter att ha fått en åtgärd innan du skickar tillbaka en bekräftelse till primärt. Andra bekräftelser skickas för åtgärder som bearbetas inom detta intervall skickas som ett svar. |
| ReplicatorEndpoint |Gäller inte |Inget standardvärde--obligatorisk parameter |Ange IP-adress och port som primär/sekundär replikatorn använder för att kommunicera med andra replikatörer i replikeringen. Detta ska referera till en resurs TCP-slutpunkt i tjänstmanifestet. Referera till [tjänstmanifestresurser](service-fabric-service-manifest-resources.md) läsa mer om hur du definierar endpoint resurser i ett tjänstmanifest. |
| MaxPrimaryReplicationQueueSize |Antal åtgärder |8192 |Maximalt antal åtgärder i den primära kön. En åtgärd frigjorts när primär replikator tar emot en bekräftelse från de sekundära replikatörer. Det här värdet måste vara större än 64 och delbart med 2. |
| MaxSecondaryReplicationQueueSize |Antal åtgärder |16384 |Maximalt antal åtgärder i den sekundära kön. En åtgärd frigjorts när du har gjort tillståndet med hög tillgänglighet via persistence. Det här värdet måste vara större än 64 och delbart med 2. |
| CheckpointThresholdInMB |MB |50 |Mängden utrymme i loggfilen efter vilken tillståndet är med kontrollpunkt. |
| MaxRecordSizeInKB |kB |1024 |Största poststorleken som replikatorn kan skriva i loggen. Det här värdet måste vara en multipel av 4 och större än 16. |
| MinLogSizeInMB |MB |0 (system bestäms) |Minsta storlek på transaktionella loggen. Loggen kommer inte att kunna trunkera till en storlek under den här inställningen. 0 anger att replikatorn avgör den minsta storleken. Det här värdet ökas ökar risken för att göra partiella kopior och inkrementella säkerhetskopieringar sedan risken för relevanta loggposter trunkerades sjunker. |
| TruncationThresholdFactor |Faktor |2 |Anger vilken storlek i loggen trunkering aktiveras. Tröskelvärde för trunkering bestäms av MinLogSizeInMB multiplicerat med TruncationThresholdFactor. TruncationThresholdFactor måste vara större än 1. MinLogSizeInMB * TruncationThresholdFactor måste vara mindre än MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Faktor |4 |Anger vilken storlek i loggen repliken börjar begränsas. Strypning tröskelvärdet (i MB) avgörs av Max ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Begränsning tröskelvärdet (i MB) måste vara större än trunkering tröskelvärdet (i MB). Trunkering tröskelvärdet (i MB) måste vara mindre än MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maximalt antal ackumulerade storleken (i MB) för backup-loggar i en viss loggkedja. En inkrementell säkerhetskopiering begäranden misslyckas om den inkrementella säkerhetskopian skulle Generera en säkerhetskopiering logg som skulle orsaka ackumulerade säkerhetskopieringsloggar sedan den relevanta fullständiga säkerhetskopieringen ska vara större än den här storleken. I sådana fall kan måste användaren göra en fullständig säkerhetskopia. |
| SharedLogId |GUID |"" |Anger ett unikt GUID för att använda för att identifiera den delade loggfil som används med den här repliken. Tjänster ska normalt inte använda den här inställningen. Men om SharedLogId anges måste sedan SharedLogPath också anges. |
| SharedLogPath |Fullständig sökväg |"" |Anger den fullständigt kvalificerade sökvägen där delade loggfilen för den här repliken ska skapas. Tjänster ska normalt inte använda den här inställningen. Men om SharedLogPath anges måste sedan SharedLogId också anges. |
| SlowApiMonitoringDuration |Sekunder |300 |Anger Övervakningsintervall för hanterade API-anrop. Exempel: användaren har angett säkerhetskopiering återanropsfunktion. Efter intervallet skickas hälsorapport en varning till Health Manager. |
| LogTruncationIntervalSeconds |Sekunder |0 |Konfigurerbara intervall på vilka log trunkering initieras på varje replik. Används för att se till att loggen har trunkerats även baserat på tid i stället för bara loggstorleken. Den här inställningen tvingar även rensning av borttagna poster i tillförlitlig ordlista. Därför användas det för att se till att borttagna objekt rensas vid rätt tidpunkt. |

### <a name="sample-configuration-via-code"></a>Exempelkonfiguration via kod
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Exempelkonfigurationsfil
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Kommentarer
BatchAcknowledgementInterval styr replikeringsfördröjning. Värdet '0' resulterar i den lägsta möjliga tidsfördröjning bekostnad dataflöde (som mer bekräftelsemeddelanden måste skickas och bearbetas, som innehåller färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre den övergripande replikering dataflöden, men kräver högre svarstid. Detta innebär direkt svarstiden för genomförda transaktioner.

Värdet för CheckpointThresholdInMB styr hur mycket diskutrymme som replikatorn kan använda för att lagra information om tillstånd i den repliken dedikerade loggfil. Öka detta på ett högre värde än standardvärdet kan leda till omkonfiguration snabbare när en ny replik läggs till i uppsättningen. Detta beror delvis tillstånd-överföring som äger rum på grund av tillgängligheten för mer historik över åtgärder i loggen. Detta kan potentiellt öka tiden för återställning av en replik efter en krasch.

MaxRecordSizeInKB inställningen definierar den maximala storleken för en post som kan skrivas av replikatorn i loggfilen. I de flesta fall är 1024 KB post standardstorlek optimalt. Dock om tjänsten orsakar större dataobjekt vara en del av Tillståndsinformationen kan kan sedan det här värdet behöva utökas. Det finns lite fördelen att göra MaxRecordSizeInKB mindre än 1024, som mindre poster Använd endast det utrymme som krävs för mindre posten. Vi förväntar oss att detta värde måste ändras i endast sällsynta fall.

Inställningar för SharedLogId och SharedLogPath används alltid tillsammans att göra en tjänst som använder en separat delad logg från delade standardloggen för noden. Mest effektivt så många tjänster som möjligt bör ange samma delade logg. Delade loggfiler ska placeras på diskar som används enbart för delade loggfilen för att minska konkurrensen huvudets rörelser. Vi förväntar oss att detta värde måste ändras i endast sällsynta fall.

## <a name="next-steps"></a>Nästa steg
* [Felsöka Service Fabric-programmet i Visual Studio](service-fabric-debugging-your-application.md)
* [Utvecklarreferens för Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

