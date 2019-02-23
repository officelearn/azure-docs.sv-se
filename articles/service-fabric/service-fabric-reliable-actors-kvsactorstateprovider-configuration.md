---
title: Ändra KVSActorStateProvider i Azure Service Fabric actors | Microsoft Docs
description: Lär dig mer om hur du konfigurerar Azure Service Fabric tillståndskänsliga aktörer av typen KVSActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 0b7c6a356812e4acd39b5164cce279b5a18eb3d5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732763"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurera Reliable Actors--KVSActorStateProvider
Du kan ändra standardkonfigurationen av KVSActorStateProvider genom att ändra filen settings.xml som skapas i Microsoft Visual Studio-paketrot Config-mappen för den angivna aktören.

Azure Service Fabric-körningen söker efter fördefinierade avsnittsnamn i filen settings.xml och förbrukar konfigurationsvärdena när du skapar de underliggande runtime-komponenterna.

> [!NOTE]
> Gör **inte** ta bort eller ändra avsnittsnamn av följande konfigurationer i filen settings.xml som genereras i Visual Studio-lösning.
> 
> 

## <a name="replicator-security-configuration"></a>Replikator säkerhetskonfiguration
Replikator säkerhetskonfigurationer används för att skydda kommunikationskanalen som används under replikering. Det innebär att tjänster inte kan se varandras replikeringstrafik, säkerställer att de data som görs med hög tillgänglighet är säker.
Som standard förhindrar en tom security konfigurationsavsnittet replikeringssäkerhet.

> [!IMPORTANT]
> På Linux-noder vara certifikat PEM-formaterade. Läs mer om att hitta och konfigurera certifikat för Linux i [konfigurerar du certifikat i Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikator konfiguration
Replikator konfigurationer konfigurera replikatorn som ansvarar för att göra aktören Tillståndsprovider tillståndet mycket pålitlig.
Standardkonfigurationen genereras av Visual Studio-mallen och bör vara tillräckligt. Det här avsnittet innehåller information om ytterligare konfigurationer som är tillgängliga att finjustera replikatorn.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0.015 |Tidsperiod som replikatorn på den sekundära väntar efter att ha fått en åtgärd innan du skickar tillbaka en bekräftelse till primärt. Andra bekräftelser skickas för åtgärder som bearbetas inom detta intervall skickas som ett svar. |
| ReplicatorEndpoint |Gäller inte |Inget standardvärde--obligatorisk parameter |Ange IP-adress och port som primär/sekundär replikatorn använder för att kommunicera med andra replikatörer i replikeringen. Detta ska referera till en resurs TCP-slutpunkt i tjänstmanifestet. Referera till [tjänstmanifestresurser](service-fabric-service-manifest-resources.md) läsa mer om hur du definierar resurser för slutpunkt i tjänstmanifestet. |
| retryInterval |Sekunder |5 |Tidsperiod efter vilken replikatorn igen skickar ett meddelande om det inte får ett godkännande för en åtgärd. |
| MaxReplicationMessageSize |Byte |50 MB |Maximal storlek för replikeringsdata som kan överföras i ett enda meddelande. |
| MaxPrimaryReplicationQueueSize |Antal åtgärder |1024 |Maximalt antal åtgärder i den primära kön. En åtgärd frigjorts när primär replikator tar emot en bekräftelse från de sekundära replikatörer. Det här värdet måste vara större än 64 och delbart med 2. |
| MaxSecondaryReplicationQueueSize |Antal åtgärder |2048 |Maximalt antal åtgärder i den sekundära kön. En åtgärd frigjorts när du har gjort tillståndet med hög tillgänglighet via persistence. Det här värdet måste vara större än 64 och delbart med 2. |

## <a name="store-configuration"></a>Store-konfiguration
Store-konfigurationer för att konfigurera det lokala arkivet som ska användas för att bevara tillstånd som håller på att replikeras.
Standardkonfigurationen genereras av Visual Studio-mallen och bör vara tillräckligt. Det här avsnittet innehåller information om ytterligare konfigurationer som är tillgängliga att finjustera det lokala arkivet.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Millisekunder |200 |Anger den maximala batchbearbetning intervall för robust lokal lagring skrivningar. |
| MaxVerPages |Antal sidor |16384 |Det maximala antalet version sidor i lokalt lagra databasen. Den anger det maximala antalet utestående transaktioner. |

## <a name="sample-configuration-file"></a>Exempelkonfigurationsfil
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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

