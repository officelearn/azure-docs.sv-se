---
title: Ändra inställningar för KVSActorStateProvider
description: Lär dig mer om hur du konfigurerar tillståndskänsliga azure-tjänstinfrastrukturaktörer av typen KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609782"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurera pålitliga aktörer - KVSActorStateProvider
Du kan ändra standardkonfigurationen för KVSActorStateProvider genom att ändra filen settings.xml som genereras i Microsoft Visual Studio-paketroten under config-mappen för den angivna aktören.

Azure Service Fabric-körningen söker efter fördefinierade avsnittsnamn i filen settings.xml och använder konfigurationsvärdena samtidigt som de underliggande körningskomponenterna skapas.

> [!NOTE]
> Ta **inte** bort eller ändra avsnittsnamnen för följande konfigurationer i filen settings.xml som genereras i Visual Studio-lösningen.
> 
> 

## <a name="replicator-security-configuration"></a>Säkerhetskonfiguration för Replikator
Replikatorsäkerhetskonfigurationer används för att skydda kommunikationskanalen som används under replikering. Det innebär att tjänster inte kan se varandras replikeringstrafik, vilket säkerställer att de data som görs tillgängliga också är säkra.
Som standard förhindrar ett tomt säkerhetskonfigurationsavsnitt replikeringssäkerhet.

> [!IMPORTANT]
> På Linux-noder måste certifikaten PEM-formaterade. Mer information om hur du hittar och konfigurerar certifikat för Linux finns i [Konfigurera certifikat på Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguration av Replikator
Repliktorkonfigurationer konfigurerar replikatorn som är ansvarig för att göra tillståndet aktörstillstånd mycket tillförlitligt.
Standardkonfigurationen genereras av Visual Studio-mallen och bör räcka. I det här avsnittet beskrivs ytterligare konfigurationer som är tillgängliga för att finjustera replikatorn.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Anmärkningar |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0.015 |Tidsperiod för vilken replikatorn vid den sekundära väntar efter att ha fått en åtgärd innan du skickar tillbaka en bekräftelse till den primära. Alla andra bekräftelser som ska skickas för åtgärder som bearbetas inom det här intervallet skickas som ett svar. |
| ReplicatorEndpoint |Ej tillämpligt |Ingen parameter som krävs för standard |IP-adress och port som den primära/sekundära replikatorn använder för att kommunicera med andra replikatorer i replikuppsättningen. Detta bör referera till en TCP-resursslutpunkt i tjänstmanifestet. Se [Tjänstmanifestresurser](service-fabric-service-manifest-resources.md) om du vill läsa mer om hur du definierar slutpunktsresurser i tjänstmanifestet. |
| ÅterförsökInterval |Sekunder |5 |Tidsperiod efter vilken replikatorn åter överför ett meddelande om det inte får en bekräftelse för en åtgärd. |
| MaxReplicationMessageSize |Byte |50 MB |Maximal storlek på replikeringsdata som kan överföras i ett enda meddelande. |
| MaxPrimaryReplicationQueueSize |Antal operationer |1024 |Maximalt antal operationer i den primära kön. En åtgärd frigörs när den primära replikatorn tar emot en bekräftelse från alla sekundära replikatorer. Det här värdet måste vara större än 64 och en effekt på 2. |
| MaxSecondaryReplicationQueueSize |Antal operationer |2048 |Maximalt antal operationer i den sekundära kön. En operation frigörs efter att ha gjort sitt tillstånd mycket tillgängligt genom uthållighet. Det här värdet måste vara större än 64 och en effekt på 2. |

## <a name="store-configuration"></a>Lagringskonfiguration
Lagringskonfigurationer används för att konfigurera det lokala arkivet som används för att bevara det tillstånd som replikeras.
Standardkonfigurationen genereras av Visual Studio-mallen och bör räcka. I det här avsnittet beskrivs ytterligare konfigurationer som är tillgängliga för att justera det lokala arkivet.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceLokalStoreConfig

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Anmärkningar |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Millisekunder |200 |Anger det maximala batchintervallet för varaktiga lokala butiksförseringar. |
| MaxVerPages |Antal sidor |16384 |Det maximala antalet versionssidor i den lokala butiksdatabasen. Den bestämmer det maximala antalet utestående transaktioner. |

## <a name="sample-configuration-file"></a>Exempelkonfigurationsfil
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
## <a name="remarks"></a>Anmärkningar
Parametern BatchAcknowledgementInterval styr replikeringsfördröjningen. Värdet "0" resulterar i lägsta möjliga latens, på bekostnad av dataflöde (eftersom fler bekräftelsemeddelanden måste skickas och bearbetas, var och en med färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre är det totala replikeringsdataflödet, till kostnaden för högre operationsfördröjning. Detta översätts direkt till svarstiden för transaktionsförseningar.

