---
title: Konfigurera tillförlitliga Azure mikrotjänster | Microsoft Docs
description: Lär dig mer om hur du konfigurerar tillståndskänsliga Reliable Services i Azure Service Fabric.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 3e058242840a3fa8b86c9ae9d5a0940cc02f04d2
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020397"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurera tillståndskänsliga reliable services
Det finns två uppsättningar av konfigurationsinställningar för tillförlitlig tjänster. En uppsättning är globala för alla tillförlitliga tjänster i klustret medan den andra uppsättningen är specifik för en viss tillförlitlig tjänst.

## <a name="global-configuration"></a>Global konfiguration
Tjänsten för global tillförlitliga konfigurationen har angetts i klustermanifestet för klustret under avsnittet KtlLogger. Tillåter konfiguration av delade loggens plats och storlek samt de globala minnesgränserna som används av loggaren. Klustermanifestet är en XML-fil som innehåller inställningar och konfigurationer som gäller för alla noder och tjänster i klustret. Filen kallas vanligtvis ClusterManifest.xml. Du kan se klustret manifestet för klustret med hjälp av powershell-kommandot Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minsta antal KB allokera i kernel-läge för loggaren skrivåtgärder buffertpool minne. Den här minnespoolen används för cachelagring statusinformation innan skrivning till disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobyte |Obegränsat |Maximal storlek som loggaren skriva buffert minnespoolen kan växa. |
| SharedLogId |GUID |"" |Anger ett unikt GUID som används för att identifiera delade Standardloggfilen används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogId i deras specifika tjänstkonfigurationen. Om SharedLogId anges måste även SharedLogPath anges. |
| SharedLogPath |Fullständig sökväg |"" |Anger den fullständiga sökvägen där loggfilen delade används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogPath i deras specifika tjänstkonfigurationen. Men om SharedLogPath anges måste sedan SharedLogId också anges. |
| SharedLogSizeInMB |Megabyte |8192 |Anger antalet MB diskutrymme att allokera statiskt för delade loggen. Värdet måste vara 2048 eller större. |

I Azure ARM eller lokala JSON-mall i exemplet nedan visar hur du ändrar den delade transaktionsloggen som skapas om du vill säkerhetskopiera alla tillförlitliga samlingar för tillståndskänsliga tjänster.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Exempel lokala developer klustret manifest-delen
Om du vill ändra den här på din lokala utvecklingsmiljö måste du redigera filen lokala clustermanifest.xml.

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
Loggaren har en global pool av minne som allokerats från icke växlingsbart kernelminne som är tillgänglig för alla tillförlitliga tjänster på en nod för cachelagring av systemtillstånd innan skrivs till dedikerade loggen som är associerade med tillförlitlig tjänst repliken. Poolstorleken styrs av inställningarna för WriteBufferMemoryPoolMinimumInKB och WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB anger både den ursprungliga storleken för den här minnespoolen och lägsta storlek som minnespoolen kan krympa. WriteBufferMemoryPoolMaximumInKB är den högsta storlek som minnespoolen kan växa. Varje tillförlitlig tjänst replik som har öppnats kan öka storleken på minnespoolen med ett system som är fastställt belopp upp till WriteBufferMemoryPoolMaximumInKB. Om det finns flera behovet av minne från minnespoolen än vad som är tillgängliga, begäranden för minne kommer att skjutas upp tills minne är tillgänglig. Därför om minnespoolen skrivåtgärder bufferten är för liten för en specifik konfiguration påverkas sedan prestanda negativt.

Inställningarna för SharedLogId och SharedLogPath är alltid användas tillsammans för att definiera GUID och platsen för delade standardloggen för alla noder i klustret. Inloggningskontot standard används för alla tillförlitliga tjänster som inte anger inställningarna i settings.xml för en specifik tjänst. För bästa prestanda bör delade loggfiler placeras på diskar som används enbart för den delade loggfilen för att minska konkurrens.

SharedLogSizeInMB Anger mängden ledigt diskutrymme för att Förallokera för delade standardloggen på alla noder.  SharedLogId och SharedLogPath behöver inte anges för SharedLogSizeInMB anges.

## <a name="service-specific-configuration"></a>Tjänstspecifik konfiguration
Du kan ändra standardkonfigurationer för tillståndskänsliga Reliable Services med hjälp av konfigurationspaketet (Config) eller service-implementeringen (kod).

* **Config** -konfigurationen via konfigurationspaketet kan åstadkommas genom att ändra filen Settings.xml som har genererats i Microsoft Visual Studio-rot för package under mappen Config för varje tjänst i programmet.
* **Koden** -konfigurationen via kod kan åstadkommas genom att skapa en ReliableStateManager med ett ReliableStateManagerConfiguration-objekt med lämpliga alternativ.

Standard Azure Service Fabric runtime söker efter fördefinierade Avsnittsnamnen i filen Settings.xml och förbrukar konfigurationsvärdena när du skapar de underliggande runtime-komponenterna.

> [!NOTE]
> Gör **inte** ta bort Avsnittsnamnen på de följande konfigurationerna i Settings.xml-filen som skapas i Visual Studio-lösning om du planerar att konfigurera tjänsten via kod.
> Byta namn på config paket eller avsnittet namn kommer att kräva en kodändring när du konfigurerar ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Replikatorn säkerhetskonfiguration
Replikatorn säkerhetskonfigurationer används för att skydda kommunikationskanalen som används vid replikering. Det innebär att tjänster inte kan se varandras replikeringstrafik, säkerställer att de data som har gjorts hög tillgänglighet är säker. Som standard förhindrar en tom säkerhetskonfigurationsavsnittet replikeringssäkerhet.

> [!IMPORTANT]
> Certifikaten måste ha PEM-formaterade för på Linux-noder. Läs mer om att hitta och konfigurera certifikat för Linux i [konfigurera certifikat på Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Avsnittet för standardnamnet
ReplicatorSecurityConfig

> [!NOTE]
> Om du vill ändra avsnittsnamnet åsidosätter du parametern replicatorSecuritySectionName till konstruktorn ReliableStateManagerConfiguration när du skapar ReliableStateManager för den här tjänsten.
> 
> 

### <a name="replicator-configuration"></a>Replikatorn konfiguration
Konfigurationer för replikatorn konfigurera replikatorn som ansvarar för att göra tillståndskänsliga Reliable tjänstens tillstånd mycket pålitlig genom att replikera och spara tillståndet lokalt.
Standardkonfigurationen genereras av Visual Studio-mall och bör vara tillräckligt. Det här avsnittet innehåller information om ytterligare konfigurationer som är tillgängliga för att finjustera replikatorn.

### <a name="default-section-name"></a>Avsnittet för standardnamnet
ReplicatorConfig

> [!NOTE]
> Om du vill ändra avsnittsnamnet åsidosätter du parametern replicatorSettingsSectionName till konstruktorn ReliableStateManagerConfiguration när du skapar ReliableStateManager för den här tjänsten.
> 
> 

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0.015 |Tidsperiod som replikatorn på de sekundära väntar när du har fått en åtgärd innan du skickar tillbaka en bekräftelse till den primära servern. Andra bekräftelser skickas för åtgärder som behandlas inom intervallet skickas som ett svar. |
| ReplicatorEndpoint |Gäller inte |Ingen standard--obligatorisk parameter |Ange IP-adress och port som primära och sekundära replikatorn ska använda för att kommunicera med andra replikatörer i replikeringen. Detta bör referera en TCP-slutpunkt för resurs i service manifest. Referera till [Service manifest resurser](service-fabric-service-manifest-resources.md) du kan läsa mer om hur du definierar endpoint resurser i en service manifest. |
| MaxPrimaryReplicationQueueSize |Antal åtgärder |8192 |Maximalt antal åtgärder i primära kön. En åtgärd frigjorts när primära replikatorn tar emot en bekräftelse från de sekundära replikatörer. Det här värdet måste vara större än 64 och delbart med 2. |
| MaxSecondaryReplicationQueueSize |Antal åtgärder |16384 |Maximalt antal åtgärder i sekundär kö. När du har gjort tillståndet hög tillgänglighet via beständiga frigjorts en åtgärd. Det här värdet måste vara större än 64 och delbart med 2. |
| CheckpointThresholdInMB |MB |50 |Mängden utrymme i loggfilen efter vilken tillståndet är kontrollpunkt. |
| MaxRecordSizeInKB |kB |1024 |Största poststorleken replikatorn kan skriva i loggen. Det här värdet måste vara en multipel av 4 och större än 16. |
| MinLogSizeInMB |MB |0 (system fastställa) |Minsta storlek för transaktionell loggen. Loggen kommer inte att kunna trunkera till en storlek under den här inställningen. 0 anger att replikatorn avgör den minsta storleken. Det här värdet ökar risken för att göra partiella kopior och inkrementella säkerhetskopieringar eftersom risken för relevanta loggposter trunkerades sänks. |
| TruncationThresholdFactor |Faktor |2 |Anger i vilken storlek på loggen trunkering ska utlösas. Trunkering tröskelvärdet bestäms av MinLogSizeInMB multiplicerat med TruncationThresholdFactor. TruncationThresholdFactor måste vara större än 1. MinLogSizeInMB * TruncationThresholdFactor måste vara mindre än MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Faktor |4 |Anger hur stor loggens repliken påbörjas begränsas. Bandbreddsbegränsning tröskelvärdet (i MB) bestäms av Max ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Bandbreddsbegränsning tröskelvärdet (i MB) måste vara större än trunkering tröskelvärdet (i MB). Trunkering tröskelvärdet (i MB) måste vara mindre än MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Max ackumulerade storleken (i MB) för säkerhetskopiering loggar i en viss loggkedja. En inkrementell säkerhetskopiering begäranden misslyckas om den inkrementella säkerhetskopian skulle Generera en säkerhetskopiering logg som skulle orsaka de ackumulerade antalet säkerhetskopieringsloggar sedan den relevanta fullständiga säkerhetskopian ska vara större än den här storleken. I sådana fall måste användaren gör en fullständig säkerhetskopia. |
| SharedLogId |GUID |"" |Anger ett unikt GUID som används för att identifiera delade loggfilen som används med den här repliken. Tjänster ska normalt inte använda den här inställningen. Men om SharedLogId anges måste sedan SharedLogPath också anges. |
| SharedLogPath |Fullständig sökväg |"" |Anger den fullständiga sökvägen där delade loggfilen för den här replikeringen kommer att skapas. Tjänster ska normalt inte använda den här inställningen. Men om SharedLogPath anges måste sedan SharedLogId också anges. |
| SlowApiMonitoringDuration |Sekunder |300 |Anger Övervakningsintervall för hanterade API-anrop. Exempel: säkerhetskopiering Återanropsfunktionen angavs av användaren. När intervallet har passerat skickas en varning hälsorapport till Health Manager. |

### <a name="sample-configuration-via-code"></a>Exempel på konfiguration via kod
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


### <a name="sample-configuration-file"></a>Exempel på konfigurationsfil
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
BatchAcknowledgementInterval styr replikeringsfördröjning. Värdet '0' resulterar i lägsta möjliga tidsfördröjning på bekostnad av dataflöde (som flera bekräftelsemeddelanden måste skickas och bearbetas, som innehåller färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre övergripande replikering genomflödet, på bekostnad av högre latens för åtgärden. Detta innebär direkt svarstiden för genomförda transaktioner.

Värdet för CheckpointThresholdInMB styr hur mycket diskutrymme som replikatorn kan använda för att lagra information om tillstånd i den repliken dedikerade loggfil. Öka det till ett högre värde än standardvärdet kan det leda till att omkonfiguration snabbare när en ny replik har lagts till i uppsättningen. Detta beror på partiella tillståndsöverföringen som äger rum på grund av tillgängligheten för tidigare åtgärder i loggen. Detta kan potentiellt öka tiden för återställning av en replik efter en krasch.

MaxRecordSizeInKB inställningen definierar den maximala storleken för en post som kan skrivas av replikatorn i loggfilen. I de flesta fall är 1024 KB poststorleken optimalt. Men om tjänsten orsakar större dataobjekt som ska ingå i tillståndsinformationen kan kan sedan det här värdet behöva ökas. Det finns lite fördelen med att göra MaxRecordSizeInKB mindre än 1024, som mindre poster Använd endast det utrymme som krävs för den mindre posten. Vi räknar med att detta värde måste ändras i endast sällsynta fall.

Inställningarna för SharedLogId och SharedLogPath används alltid tillsammans för att en tjänst som använder en separat delad logg från standard delade loggen för noden. Mest effektivt så många tjänster som möjligt bör ange samma delade logg. Delade loggfiler ska placeras på diskar som används enbart för den delade loggfilen för att minska head flytt konkurrens. Vi räknar med att detta värde måste ändras i endast sällsynta fall.

## <a name="next-steps"></a>Nästa steg
* [Felsöka Service Fabric-program i Visual Studio](service-fabric-debugging-your-application.md)
* [För utvecklare för Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

