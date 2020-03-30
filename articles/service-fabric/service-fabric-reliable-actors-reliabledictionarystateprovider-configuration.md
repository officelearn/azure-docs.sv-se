---
title: Ändra reliableDictionaryActorStateProvider-inställningar
description: Lär dig mer om hur du konfigurerar azure service fabric-tillståndskänsliga aktörer av typen ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609748"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurera pålitliga aktörer - ReliableDictionaryActorStateProvider
Du kan ändra standardkonfigurationen för ReliableDictionaryActorStateProvider genom att ändra filen settings.xml som genereras i Visual Studio-paketroten under config-mappen för den angivna aktören.

Azure Service Fabric-körningen söker efter fördefinierade avsnittsnamn i filen settings.xml och använder konfigurationsvärdena samtidigt som de underliggande körningskomponenterna skapas.

> [!NOTE]
> Ta **inte** bort eller ändra avsnittsnamnen för följande konfigurationer i filen settings.xml som genereras i Visual Studio-lösningen.
> 
> 

Det finns också globala inställningar som påverkar konfigurationen av ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Global konfiguration
Den globala konfigurationen anges i klustermanifestet för klustret under avsnittet KtlLogger. Det möjliggör konfiguration av den delade loggplatsen och storleken plus de globala minnesgränser som används av loggern. Observera att ändringar i klustermanifestet påverkar alla tjänster som använder ReliableDictionaryActorStateProvider och tillförlitliga tillståndskänsliga tjänster.

Klustermanifestet är en enda XML-fil som innehåller inställningar och konfigurationer som gäller för alla noder och tjänster i klustret. Filen kallas vanligtvis ClusterManifest.xml. Du kan se klustermanifestet för ditt kluster med kommandot Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Anmärkningar |
| --- | --- | --- | --- |
| SkrivBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minsta antal KB att allokera i kernel-läge för logger skriva buffert minne pool. Den här minnespoolen används för cachelagringstillståndsinformation innan du skriver till disk. |
| SkrivBufferMemoryPoolMaximumInKB |Kilobyte |Obegränsat |Maximal storlek som logger skriva buffert minne pool kan växa. |
| SharedLogId |GUID |"" |Anger ett unikt GUID som ska användas för att identifiera standarddokumenterade loggfilen som används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogId i sin tjänstspecifika konfiguration. Om SharedLogId har angetts måste även SharedLogPath anges. |
| SharedLogPath |Fullständigt kvalificerat sökvägsnamn |"" |Anger den fullständigt kvalificerade sökvägen där den delade loggfilen som används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogPath i sin tjänstspecifika konfiguration. Men om SharedLogPath anges måste SharedLogId också anges. |
| SharedLogSizeInMB |Megabyte |8192 |Anger antalet MB diskutrymme som ska allokeras statiskt för den delade loggen. Värdet måste vara 2048 eller större. |

### <a name="sample-cluster-manifest-section"></a>Exempel på klustermanifestavsnitt
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Anmärkningar
Loggern har en global minnespool som allokerats från icke-sökt kernelminne som är tillgänglig för alla tillförlitliga tjänster på en nod för cachelagring av tillståndsdata innan den skrivs till den dedikerade loggen som är associerad med den tillförlitliga tjänstrepliken. Poolstorleken styrs av inställningarna WriteBufferMemoryPoolMinimumInKB och WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB anger både den ursprungliga storleken på den här minnespoolen och den lägsta storleken som minnespoolen kan krympa till. WriteBufferMemoryPoolMaximumInKB är den högsta storleken som minnespoolen kan växa till. Varje tillförlitlig tjänst replik som öppnas kan öka storleken på minnespoolen med ett system bestämd belopp upp till WriteBufferMemoryPoolMaximumInKB. Om det finns större efterfrågan på minne från minnespoolen än vad som är tillgängligt, kommer begäranden om minne att fördröjas tills minnet är tillgängligt. Därför om skrivbuffertminnespoolen är för liten för en viss konfiguration kan prestanda uppstå.

Inställningarna SharedLogId och SharedLogPath används alltid tillsammans för att definiera GUID och plats för standardloggen för alla noder i klustret. Standardloggen för delad logg används för alla tillförlitliga tjänster som inte anger inställningarna i settings.xml för den specifika tjänsten. För bästa prestanda bör delade loggfiler placeras på diskar som endast används för den delade loggfilen för att minska konkurrens.

SharedLogSizeInMB anger hur mycket diskutrymme som ska förallokeras för standarddelloggen för alla noder.  SharedLogId och SharedLogPath behöver inte anges för att SharedLogSizeInMB ska kunna anges.

## <a name="replicator-security-configuration"></a>Säkerhetskonfiguration för Replikator
Replikatorsäkerhetskonfigurationer används för att skydda kommunikationskanalen som används under replikering. Det innebär att tjänster inte kan se varandras replikeringstrafik, vilket säkerställer att data som görs högtillgängade också är säkra.
Som standard förhindrar ett tomt säkerhetskonfigurationsavsnitt replikeringssäkerhet.

> [!IMPORTANT]
> På Linux-noder måste certifikaten PEM-formaterade. Mer information om hur du hittar och konfigurerar certifikat för Linux finns i [Konfigurera certifikat på Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfiguration av Replikator
Replikatorkonfigurationer används för att konfigurera replikatorn som är ansvarig för att göra tillståndet aktörstillstånd mycket tillförlitligt genom att replikera och bevara tillståndet lokalt.
Standardkonfigurationen genereras av Visual Studio-mallen och bör räcka. I det här avsnittet beskrivs ytterligare konfigurationer som är tillgängliga för att finjustera replikatorn.

### <a name="section-name"></a>Avsnittsnamn
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Anmärkningar |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0.015 |Tidsperiod för vilken replikatorn vid den sekundära väntar efter att ha fått en åtgärd innan du skickar tillbaka en bekräftelse till den primära. Alla andra bekräftelser som ska skickas för åtgärder som bearbetas inom det här intervallet skickas som ett svar. |
| ReplicatorEndpoint |Ej tillämpligt |Ingen parameter som krävs för standard |IP-adress och port som den primära/sekundära replikatorn använder för att kommunicera med andra replikatorer i replikuppsättningen. Detta bör referera till en TCP-resursslutpunkt i tjänstmanifestet. Se [Tjänstmanifestresurser](service-fabric-service-manifest-resources.md) om du vill läsa mer om hur du definierar slutpunktsresurser i tjänstmanifestet. |
| MaxReplicationMessageSize |Byte |50 MB |Maximal storlek på replikeringsdata som kan överföras i ett enda meddelande. |
| MaxPrimaryReplicationQueueSize |Antal operationer |8192 |Maximalt antal operationer i den primära kön. En åtgärd frigörs när den primära replikatorn tar emot en bekräftelse från alla sekundära replikatorer. Det här värdet måste vara större än 64 och en effekt på 2. |
| MaxSecondaryReplicationQueueSize |Antal operationer |16384 |Maximalt antal operationer i den sekundära kön. En operation frigörs efter att ha gjort sitt tillstånd mycket tillgängligt genom uthållighet. Det här värdet måste vara större än 64 och en effekt på 2. |
| CheckpointThresholdInMB |MB |200 |Mängden loggfilsutrymme efter vilket tillståndet är kontrollområde. |
| MaxRecordSizeInKB |KB |1024 |Största poststorlek som replikatorn kan skriva i loggen. Det här värdet måste vara en multipel av 4 och större än 16. |
| OptimeraLogFörlowerDiskUsage |Boolean |true |När värdet är true konfigureras loggen så att replikens dedikerade loggfil skapas med hjälp av en NTFS-gles fil. Detta sänker den faktiska diskutrymmesanvändningen för filen. När det är falskt skapas filen med fasta allokeringar, vilket ger bästa skrivprestanda. |
| SharedLogId |Guid |"" |Anger ett unikt guid som ska användas för att identifiera den delade loggfilen som används med den här repliken. Vanligtvis bör tjänster inte använda den här inställningen. Men om SharedLogId anges måste SharedLogPath också anges. |
| SharedLogPath |Fullständigt kvalificerat sökvägsnamn |"" |Anger den fullständigt kvalificerade sökvägen där den delade loggfilen för den här repliken ska skapas. Vanligtvis bör tjänster inte använda den här inställningen. Men om SharedLogPath anges måste SharedLogId också anges. |

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

## <a name="remarks"></a>Anmärkningar
Parametern BatchAcknowledgementInterval styr replikeringsfördröjningen. Värdet "0" resulterar i lägsta möjliga latens, på bekostnad av dataflöde (eftersom fler bekräftelsemeddelanden måste skickas och bearbetas, var och en med färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre är det totala replikeringsdataflödet, till kostnaden för högre operationsfördröjning. Detta översätts direkt till svarstiden för transaktionsförseningar.

Parametern CheckpointThresholdInMB styr mängden diskutrymme som replikatorn kan använda för att lagra tillståndsinformation i replikens dedikerade loggfil. Om du ökar detta till ett högre värde än standardvärdet kan det leda till snabbare omkonfigurationstider när en ny replik läggs till i uppsättningen. Detta beror på den partiella tillståndsöverföringen som sker på grund av tillgängligheten för mer historik över åtgärder i loggen. Detta kan potentiellt öka återställningstiden för en replik efter en krasch.

Om du ställer in OptimizeForLowerDiskUsage till true kommer loggfilsutrymmet att överetablegas så att aktiva repliker kan lagra mer tillståndsinformation i sina loggfiler, medan inaktiva repliker använder mindre diskutrymme. Detta gör det möjligt att vara värd för fler repliker på en nod. Om du ställer in OptimizeForLowerDiskUsage till false skrivs tillståndsinformationen snabbare till loggfilerna.

Inställningen MaxRecordSizeInKB definierar den maximala storleken på en post som kan skrivas av replikatorn i loggfilen. I de flesta fall är standardpoststorleken på 1024 KB optimal. Men om tjänsten orsakar större dataobjekt att vara en del av tillståndsinformationen, kan det här värdet behöva ökas. Det finns liten nytta med att göra MaxRecordSizeInKB mindre än 1024, eftersom mindre poster bara använder det utrymme som behövs för den mindre posten. Vi förväntar oss att detta värde endast skulle behöva ändras i sällsynta fall.

Inställningarna SharedLogId och SharedLogPath används alltid tillsammans för att göra en tjänst använda en separat delad logg från standardloggen för noden. För bästa effektivitet bör så många tjänster som möjligt ange samma delade logg. Delade loggfiler bör placeras på diskar som endast används för den delade loggfilen, för att minska huvudförflyttningskonkurrensen. Vi förväntar oss att dessa värden endast skulle behöva ändras i sällsynta fall.

