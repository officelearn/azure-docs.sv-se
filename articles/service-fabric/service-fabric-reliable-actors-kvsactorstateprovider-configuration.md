---
title: Ändra inställningar för KVSActorStateProvider i Azure mikrotjänster | Microsoft Docs
description: Lär dig mer om hur du konfigurerar Azure Service Fabric tillståndskänslig aktörer av typen KVSActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: c01bcfecea8d79784b764e715f077c76e7d4be45
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017655"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurera Reliable Actors--KVSActorStateProvider
Du kan ändra standardkonfigurationen av KVSActorStateProvider genom att ändra filen settings.xml som skapas i Microsoft Visual Studio-rot för package under mappen Config för den angivna aktören.

Azure Service Fabric runtime söker efter fördefinierade Avsnittsnamnen i filen settings.xml och förbrukar konfigurationsvärdena när du skapar de underliggande runtime-komponenterna.

> [!NOTE]
> Gör **inte** ta bort eller ändra Avsnittsnamnen på de följande konfigurationerna i settings.xml-filen som skapas i Visual Studio-lösning.
> 
> 

## <a name="replicator-security-configuration"></a>Replikatorn säkerhetskonfiguration
Replikatorn säkerhetskonfigurationer används för att skydda kommunikationskanalen som används vid replikering. Det innebär att tjänster inte kan se varandras replikeringstrafik, säkerställer att de data som har gjorts hög tillgänglighet är säker.
Som standard förhindrar en tom säkerhetskonfigurationsavsnittet replikeringssäkerhet.

> [!IMPORTANT]
> Certifikaten måste ha PEM-formaterade för på Linux-noder. Läs mer om att hitta och konfigurera certifikat för Linux i [konfigurera certifikat på Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikatorn konfiguration
Konfigurationer för replikatorn konfigurera replikatorn som ansvarar för att göra tillståndet aktören Tillståndsprovidern hög tillförlitlig.
Standardkonfigurationen genereras av Visual Studio-mall och bör vara tillräckligt. Det här avsnittet innehåller information om ytterligare konfigurationer som är tillgängliga för att finjustera replikatorn.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0.015 |Tidsperiod som replikatorn på de sekundära väntar när du har fått en åtgärd innan du skickar tillbaka en bekräftelse till den primära servern. Andra bekräftelser skickas för åtgärder som behandlas inom intervallet skickas som ett svar. |
| ReplicatorEndpoint |Gäller inte |Ingen standard--obligatorisk parameter |Ange IP-adress och port som primära och sekundära replikatorn ska använda för att kommunicera med andra replikatörer i replikeringen. Detta bör referera en TCP-slutpunkt för resurs i service manifest. Referera till [Service manifest resurser](service-fabric-service-manifest-resources.md) du kan läsa mer om hur du definierar endpoint resurser i service manifest. |
| retryInterval |Sekunder |5 |Tidsperiod efter vilken replikatorn igen skickar ett meddelande om den inte får ett godkännande för en åtgärd. |
| MaxReplicationMessageSize |Byte |50 MB |Maximal storlek för replikeringsdata som kan överföras i ett enda meddelande. |
| MaxPrimaryReplicationQueueSize |Antal åtgärder |1024 |Maximalt antal åtgärder i primära kön. En åtgärd frigjorts när primära replikatorn tar emot en bekräftelse från de sekundära replikatörer. Det här värdet måste vara större än 64 och delbart med 2. |
| MaxSecondaryReplicationQueueSize |Antal åtgärder |2048 |Maximalt antal åtgärder i sekundär kö. När du har gjort tillståndet hög tillgänglighet via beständiga frigjorts en åtgärd. Det här värdet måste vara större än 64 och delbart med 2. |

## <a name="store-configuration"></a>Store-konfiguration
Store konfigurationer för att konfigurera det lokala arkivet som används för att bevara tillståndet som replikeras.
Standardkonfigurationen genereras av Visual Studio-mall och bör vara tillräckligt. Det här avsnittet innehåller information om ytterligare konfigurationer som är tillgängliga att justera det lokala arkivet.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Kommentarer |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Millisekunder |200 |Anger den maximala batchbearbetning intervall för beständig lokalt Arkiv genomföranden. |
| MaxVerPages |Antal sidor |16384 |Det maximala antalet version sidor i lokalt lagra databasen. Den anger det maximala antalet utestående transaktioner. |

## <a name="sample-configuration-file"></a>Exempel på konfigurationsfil
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
Parametern BatchAcknowledgementInterval styr replikeringsfördröjning. Värdet '0' resulterar i lägsta möjliga tidsfördröjning på bekostnad av dataflöde (som flera bekräftelsemeddelanden måste skickas och bearbetas, som innehåller färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre övergripande replikering genomflödet, på bekostnad av högre latens för åtgärden. Detta innebär direkt svarstiden för genomförda transaktioner.

