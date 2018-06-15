---
title: Ändra inställningar för ReliableDictionaryActorStateProvider i Azure mikrotjänster | Microsoft Docs
description: Lär dig mer om hur du konfigurerar Azure Service Fabric tillståndskänslig aktörer av typen ReliableDictionaryActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 00ae5db5fc7a327ae19e64c3d8adf653afd12677
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213120"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurera Reliable Actors--ReliableDictionaryActorStateProvider
Du kan ändra standardkonfigurationen av ReliableDictionaryActorStateProvider genom att ändra filen settings.xml har skapats i Visual Studio-rot för package under mappen Config för angivna aktören.

Azure Service Fabric runtime söker efter fördefinierade Avsnittsnamnen i filen settings.xml och förbrukar konfigurationsvärdena när du skapar de underliggande runtime-komponenterna.

> [!NOTE]
> Gör **inte** ta bort eller ändra Avsnittsnamnen på de följande konfigurationerna i settings.xml-filen som skapas i Visual Studio-lösning.
> 
> 

Det finns också globala inställningar som påverkar konfigurationen av ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Global konfiguration
Den globala konfigurationen har angetts i klustermanifestet för klustret under avsnittet KtlLogger. Tillåter konfiguration av delade loggens plats och storlek samt de globala minnesgränserna som används av loggaren. Observera att alla tjänster som använder ReliableDictionaryActorStateProvider och tillförlitlig tillståndskänsliga tjänster påverkas av ändringar i klustermanifestet.

Klustermanifestet är en XML-fil som innehåller inställningar och konfigurationer som gäller för alla noder och tjänster i klustret. Filen kallas vanligtvis ClusterManifest.xml. Du kan se klustret manifestet för klustret med hjälp av powershell-kommandot Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minsta antal KB allokera i kernel-läge för loggaren skrivåtgärder buffertpool minne. Den här minnespoolen används för cachelagring statusinformation innan skrivning till disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobyte |Obegränsat |Maximal storlek som loggaren skriva buffert minnespoolen kan växa. |
| SharedLogId |GUID |"" |Anger ett unikt GUID som används för att identifiera delade Standardloggfilen används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogId i deras specifika tjänstkonfigurationen. Om SharedLogId anges måste även SharedLogPath anges. |
| SharedLogPath |Fullständig sökväg |"" |Anger den fullständiga sökvägen där loggfilen delade används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogPath i deras specifika tjänstkonfigurationen. Men om SharedLogPath anges måste sedan SharedLogId också anges. |
| SharedLogSizeInMB |Megabyte |8192 |Anger antalet MB diskutrymme att allokera statiskt för delade loggen. Värdet måste vara 2048 eller större. |

### <a name="sample-cluster-manifest-section"></a>Exemplet kluster manifest-delen
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Kommentarer
Loggaren har en global pool av minne som allokerats från icke växlingsbart kernelminne som är tillgänglig för alla tillförlitliga tjänster på en nod för cachelagring av systemtillstånd innan skrivs till dedikerade loggen som är associerade med tillförlitlig tjänst repliken. Poolstorleken styrs av inställningarna för WriteBufferMemoryPoolMinimumInKB och WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB anger både den ursprungliga storleken för den här minnespoolen och lägsta storlek som minnespoolen kan krympa. WriteBufferMemoryPoolMaximumInKB är den högsta storlek som minnespoolen kan växa. Varje tillförlitlig tjänst replik som har öppnats kan öka storleken på minnespoolen med ett system som är fastställt belopp upp till WriteBufferMemoryPoolMaximumInKB. Om det finns flera behovet av minne från minnespoolen än vad som är tillgängliga, begäranden för minne kommer att skjutas upp tills minne är tillgänglig. Därför om minnespoolen skrivåtgärder bufferten är för liten för en specifik konfiguration påverkas sedan prestanda negativt.

Inställningarna för SharedLogId och SharedLogPath är alltid användas tillsammans för att definiera GUID och platsen för delade standardloggen för alla noder i klustret. Inloggningskontot standard används för alla tillförlitliga tjänster som inte anger inställningarna i settings.xml för en specifik tjänst. För bästa prestanda bör delade loggfiler placeras på diskar som används enbart för den delade loggfilen för att minska konkurrens.

SharedLogSizeInMB Anger mängden ledigt diskutrymme för att Förallokera för delade standardloggen på alla noder.  SharedLogId och SharedLogPath behöver inte anges för SharedLogSizeInMB anges.

## <a name="replicator-security-configuration"></a>Replikatorn säkerhetskonfiguration
Replikatorn säkerhetskonfigurationer används för att skydda kommunikationskanalen som används vid replikering. Det innebär att tjänster inte kan se varandras replikeringstrafik, säkerställer att data som görs högtillgänglig också är säkra.
Som standard förhindrar en tom säkerhetskonfigurationsavsnittet replikeringssäkerhet.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikatorn konfiguration
Replikatorn konfigurationer för att konfigurera replikatorn som ansvarar för att göra tillståndet aktören Tillståndsprovidern mycket pålitlig genom att replikera och spara tillståndet lokalt.
Standardkonfigurationen genereras av Visual Studio-mall och bör vara tillräckligt. Det här avsnittet innehåller information om ytterligare konfigurationer som är tillgängliga för att finjustera replikatorn.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0.015 |Tidsperiod som replikatorn på de sekundära väntar när du har fått en åtgärd innan du skickar tillbaka en bekräftelse till den primära servern. Andra bekräftelser skickas för åtgärder som behandlas inom intervallet skickas som ett svar. |
| ReplicatorEndpoint |Gäller inte |Ingen standard--obligatorisk parameter |Ange IP-adress och port som primära och sekundära replikatorn ska använda för att kommunicera med andra replikatörer i replikeringen. Detta bör referera en TCP-slutpunkt för resurs i service manifest. Referera till [Service manifest resurser](service-fabric-service-manifest-resources.md) du kan läsa mer om hur du definierar endpoint resurser i service manifest. |
| MaxReplicationMessageSize |Byte |50 MB |Maximal storlek för replikeringsdata som kan överföras i ett enda meddelande. |
| MaxPrimaryReplicationQueueSize |Antal åtgärder |8192 |Maximalt antal åtgärder i primära kön. En åtgärd frigjorts när primära replikatorn tar emot en bekräftelse från de sekundära replikatörer. Det här värdet måste vara större än 64 och delbart med 2. |
| MaxSecondaryReplicationQueueSize |Antal åtgärder |16384 |Maximalt antal åtgärder i sekundär kö. När du har gjort tillståndet hög tillgänglighet via beständiga frigjorts en åtgärd. Det här värdet måste vara större än 64 och delbart med 2. |
| CheckpointThresholdInMB |MB |200 |Mängden utrymme i loggfilen efter vilken tillståndet är kontrollpunkt. |
| MaxRecordSizeInKB |kB |1024 |Största poststorleken replikatorn kan skriva i loggen. Det här värdet måste vara en multipel av 4 och större än 16. |
| OptimizeLogForLowerDiskUsage |Boolesk |true |Om värdet är true är loggen konfigurerad så att den repliken dedikerade loggfil skapas med hjälp av en NTFS-sparse-fil. Detta minskar den faktiska diskutrymmesanvändningen för filen. Om värdet är false skapas filen med fast allokering som ger bäst skrivprestanda. |
| SharedLogId |GUID |"" |Anger ett unikt guid som används för att identifiera delade loggfilen som används med den här repliken. Tjänster ska normalt inte använda den här inställningen. Men om SharedLogId anges måste sedan SharedLogPath också anges. |
| SharedLogPath |Fullständig sökväg |"" |Anger den fullständiga sökvägen där delade loggfilen för den här replikeringen kommer att skapas. Tjänster ska normalt inte använda den här inställningen. Men om SharedLogPath anges måste sedan SharedLogId också anges. |

## <a name="sample-configuration-file"></a>Exempel på konfigurationsfil
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
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

## <a name="remarks"></a>Kommentarer
Parametern BatchAcknowledgementInterval styr replikeringsfördröjning. Värdet '0' resulterar i lägsta möjliga tidsfördröjning på bekostnad av dataflöde (som flera bekräftelsemeddelanden måste skickas och bearbetas, som innehåller färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre övergripande replikering genomflödet, på bekostnad av högre latens för åtgärden. Detta innebär direkt svarstiden för genomförda transaktioner.

Parametern CheckpointThresholdInMB styr hur mycket diskutrymme som replikatorn kan använda för att lagra information om tillstånd i den repliken dedikerade loggfil. Öka det till ett högre värde än standardvärdet kan det leda till att omkonfiguration snabbare när en ny replik har lagts till i uppsättningen. Detta beror på partiella tillståndsöverföringen som äger rum på grund av tillgängligheten för tidigare åtgärder i loggen. Detta kan potentiellt öka tiden för återställning av en replik efter en krasch.

Om du anger OptimizeForLowerDiskUsage till true utrymme i loggfilen inte över etablerade så att aktiva repliker kan lagra mer statusinformation i sina loggfiler, medan inaktiva replikeringar använder mindre diskutrymme. Detta gör det möjligt att vara värd för flera repliker på en nod. Om du anger OptimizeForLowerDiskUsage till false, skrivs tillståndsinformationen loggfilerna snabbare.

MaxRecordSizeInKB inställningen definierar den maximala storleken för en post som kan skrivas av replikatorn i loggfilen. I de flesta fall är 1024 KB poststorleken optimalt. Men om tjänsten orsakar större dataobjekt som ska ingå i tillståndsinformationen kan kan sedan det här värdet behöva ökas. Det finns lite fördelen med att göra MaxRecordSizeInKB mindre än 1024, som mindre poster Använd endast det utrymme som krävs för den mindre posten. Vi räknar med att detta värde måste ändras endast i sällsynta fall.

Inställningarna för SharedLogId och SharedLogPath används alltid tillsammans för att en tjänst som använder en separat delad logg från standard delade loggen för noden. Mest effektivt så många tjänster som möjligt bör ange samma delade logg. Delade loggfiler ska placeras på diskar som används enbart för den delade loggfilen för att minska head flytt konkurrens. Vi räknar med att dessa värden måste ändras endast i sällsynta fall.

