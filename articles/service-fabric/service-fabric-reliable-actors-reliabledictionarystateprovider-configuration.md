---
title: Ändra ReliableDictionaryActorStateProvider i Azure Service Fabric actors | Microsoft Docs
description: Lär dig mer om hur du konfigurerar Azure Service Fabric tillståndskänsliga aktörer av typen ReliableDictionaryActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: ''
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 4e39357a765ec85aa64055b1aa422d8d7a01c116
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669409"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurera Reliable Actors--ReliableDictionaryActorStateProvider
Du kan ändra standardkonfigurationen för ReliableDictionaryActorStateProvider genom att ändra filen settings.xml som genererats i Visual Studio-paketrot Config-mappen för den angivna aktören.

Azure Service Fabric-körningen söker efter fördefinierade avsnittsnamn i filen settings.xml och förbrukar konfigurationsvärdena när du skapar de underliggande runtime-komponenterna.

> [!NOTE]
> Gör **inte** ta bort eller ändra avsnittsnamn av följande konfigurationer i filen settings.xml som genereras i Visual Studio-lösning.
> 
> 

Det finns även globala inställningar som påverkar konfigurationen av ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Global konfiguration
Den globala konfigurationen har angetts i klustermanifestet för klustret under avsnittet KtlLogger. Det tillåter konfiguration av delade loggens plats och storlek samt gränserna för globalt minne som används av loggaren. Observera att ändringar i klustermanifestet påverkar alla tjänster som använder ReliableDictionaryActorStateProvider och tillförlitliga tillståndskänsliga tjänster.

Klustermanifestet är en enkel XML-fil som innehåller inställningar och konfigurationer som gäller för alla noder och tjänster i klustret. Filen kallas vanligtvis ClusterManifest.xml. Du kan se klustret manifest för klustret med hjälp av powershell-kommandot Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minsta antal KB att allokera i kernelläge för loggaren skrivning buffertpooltillägget minne. Den här minnespoolen används för att cachelagra tillståndsinformationen innan skrivning till disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobyte |Obegränsat |Maximal storlek som loggaren skriva bufferten minnespoolen kan växa. |
| SharedLogId |GUID |"" |Anger ett unikt GUID för att använda för att identifiera delade Standardloggfilen används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogId i deras specifika tjänstekonfigurationen. Om SharedLogId anges måste även SharedLogPath anges. |
| SharedLogPath |Fullständig sökväg |"" |Anger den fullständigt kvalificerade sökvägen där loggfilen delade används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogPath i deras specifika tjänstekonfigurationen. Men om SharedLogPath anges måste sedan SharedLogId också anges. |
| SharedLogSizeInMB |Megabyte |8192 |Anger antalet MB diskutrymme att allokera statiskt för delade loggen. Värdet måste vara 2048 eller större. |

### <a name="sample-cluster-manifest-section"></a>Manifest Exempelavsnitt för kluster
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
Loggaren har en global pool av allokerat minne från icke växlade kernelminne som är tillgängliga för alla tillförlitliga tjänster på en nod för cachelagring tillståndsdata innan skrivs in i dedikerade som är associerade med reliable Services-repliken. Poolstorleken styrs av inställningarna WriteBufferMemoryPoolMinimumInKB och WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB anger storleken på den här minnespoolen och lägsta storleken som minnespoolen kan minska. WriteBufferMemoryPoolMaximumInKB är den högsta storleken som minnespoolen kan växa. Varje replik för tillförlitlig tjänst som är öppen kan öka storleken på minnespoolen med ett system som bestäms belopp upp till WriteBufferMemoryPoolMaximumInKB. Om det finns flera begäran för minne från minnespoolen än vad som är tillgängliga, ska begäranden för minne fördröjas tills minne är tillgänglig. Därför om skrivna minne buffertpooltillägget är för liten för en specifik konfiguration sjunka sedan prestanda.

Inställningar för SharedLogId och SharedLogPath används alltid tillsammans att definiera GUID och plats för delade standardloggen för alla noder i klustret. Delade Standardloggen används för alla tillförlitliga tjänster som inte anger inställningarna i settings.xml för den specifika tjänsten. För bästa prestanda bör delade loggfilerna placeras på diskar som används enbart för delade loggfilen för att minska konkurrensen.

SharedLogSizeInMB Anger mängden diskutrymme för att Förallokera för delade standardloggen på alla noder.  SharedLogId och SharedLogPath behöver inte anges för SharedLogSizeInMB anges.

## <a name="replicator-security-configuration"></a>Replikator säkerhetskonfiguration
Replikator säkerhetskonfigurationer används för att skydda kommunikationskanalen som används under replikering. Det innebär att tjänster inte kan se varandras replikeringstrafik, se till att de data som görs med hög tillgänglighet är också säkra.
Som standard förhindrar en tom security konfigurationsavsnittet replikeringssäkerhet.

> [!IMPORTANT]
> På Linux-noder vara certifikat PEM-formaterade. Läs mer om att hitta och konfigurera certifikat för Linux i [konfigurerar du certifikat i Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikator konfiguration
Replikator konfigurationer används för att konfigurera replikatorn som ansvarar för att göra aktören Tillståndsprovider tillstånd genom att replikera och spara tillståndet lokalt mycket pålitlig.
Standardkonfigurationen genereras av Visual Studio-mallen och bör vara tillräckligt. Det här avsnittet innehåller information om ytterligare konfigurationer som är tillgängliga att finjustera replikatorn.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0.015 |Tidsperiod som replikatorn på den sekundära väntar efter att ha fått en åtgärd innan du skickar tillbaka en bekräftelse till primärt. Andra bekräftelser skickas för åtgärder som bearbetas inom detta intervall skickas som ett svar. |
| ReplicatorEndpoint |Gäller inte |Inget standardvärde--obligatorisk parameter |Ange IP-adress och port som primär/sekundär replikatorn använder för att kommunicera med andra replikatörer i replikeringen. Detta ska referera till en resurs TCP-slutpunkt i tjänstmanifestet. Referera till [tjänstmanifestresurser](service-fabric-service-manifest-resources.md) läsa mer om hur du definierar resurser för slutpunkt i tjänstmanifestet. |
| MaxReplicationMessageSize |Byte |50 MB |Maximal storlek för replikeringsdata som kan överföras i ett enda meddelande. |
| MaxPrimaryReplicationQueueSize |Antal åtgärder |8192 |Maximalt antal åtgärder i den primära kön. En åtgärd frigjorts när primär replikator tar emot en bekräftelse från de sekundära replikatörer. Det här värdet måste vara större än 64 och delbart med 2. |
| MaxSecondaryReplicationQueueSize |Antal åtgärder |16384 |Maximalt antal åtgärder i den sekundära kön. En åtgärd frigjorts när du har gjort tillståndet med hög tillgänglighet via persistence. Det här värdet måste vara större än 64 och delbart med 2. |
| CheckpointThresholdInMB |MB |200 |Mängden utrymme i loggfilen efter vilken tillståndet är med kontrollpunkt. |
| MaxRecordSizeInKB |kB |1024 |Största poststorleken som replikatorn kan skriva i loggen. Det här värdet måste vara en multipel av 4 och större än 16. |
| OptimizeLogForLowerDiskUsage |Boolesk |true |Om värdet är true är loggen konfigurerad så att den repliken dedikerade loggfil skapas med hjälp av en NTFS-sparse-fil. Detta minskar den faktiska diskutrymmesanvändningen för filen. När värdet är FALSKT skapas filen med fast allokeringar som ger bäst skrivprestanda. |
| SharedLogId |GUID |"" |Anger ett unikt guid för att använda för att identifiera den delade loggfil som används med den här repliken. Tjänster ska normalt inte använda den här inställningen. Men om SharedLogId anges måste sedan SharedLogPath också anges. |
| SharedLogPath |Fullständig sökväg |"" |Anger den fullständigt kvalificerade sökvägen där delade loggfilen för den här repliken ska skapas. Tjänster ska normalt inte använda den här inställningen. Men om SharedLogPath anges måste sedan SharedLogId också anges. |

## <a name="sample-configuration-file"></a>Exempelkonfigurationsfil
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Parametern BatchAcknowledgementInterval styr replikeringsfördröjning. Värdet '0' resulterar i den lägsta möjliga tidsfördröjning bekostnad dataflöde (som mer bekräftelsemeddelanden måste skickas och bearbetas, som innehåller färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre den övergripande replikering dataflöden, men kräver högre svarstid. Detta innebär direkt svarstiden för genomförda transaktioner.

Parametern CheckpointThresholdInMB styr hur mycket diskutrymme som replikatorn kan använda för att lagra information om tillstånd i den repliken dedikerade loggfil. Öka detta på ett högre värde än standardvärdet kan leda till omkonfiguration snabbare när en ny replik läggs till i uppsättningen. Detta beror delvis tillstånd-överföring som äger rum på grund av tillgängligheten för mer historik över åtgärder i loggen. Detta kan potentiellt öka tiden för återställning av en replik efter en krasch.

Om du ställer in OptimizeForLowerDiskUsage till true blir utrymme i loggfilen överetablerade så att aktiva repliker kan lagra mer statusinformation i sina loggfiler, medan inaktiva replikeringar använder mindre diskutrymme. Detta gör det möjligt att vara värd för flera repliker på en nod. Om du ställer in OptimizeForLowerDiskUsage till false skrivs tillståndsinformationen loggfilerna snabbare.

MaxRecordSizeInKB inställningen definierar den maximala storleken för en post som kan skrivas av replikatorn i loggfilen. I de flesta fall är 1024 KB post standardstorlek optimalt. Dock om tjänsten orsakar större dataobjekt vara en del av Tillståndsinformationen kan kan sedan det här värdet behöva utökas. Det finns lite fördelen att göra MaxRecordSizeInKB mindre än 1024, som mindre poster Använd endast det utrymme som krävs för mindre posten. Vi förväntar oss att detta värde måste ändras endast i sällsynta fall.

Inställningar för SharedLogId och SharedLogPath används alltid tillsammans att göra en tjänst som använder en separat delad logg från delade standardloggen för noden. Mest effektivt så många tjänster som möjligt bör ange samma delade logg. Delade loggfiler ska placeras på diskar som används enbart för delade loggfilen för att minska konkurrensen huvudets rörelser. Vi förväntar oss att dessa värden måste ändras endast i sällsynta fall.

