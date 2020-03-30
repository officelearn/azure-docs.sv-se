---
title: Konfigurera tillförlitliga tjänster för Azure-tjänstinfrastruktur
description: Lär dig mer om hur du konfigurerar tillståndskänsliga reliable services i ett Azure Service Fabric-program globalt och för en enda tjänst.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 9743213394b59af701b25b8be9dd48cf4310b499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645522"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurera tillståndskänsliga tillförlitliga tjänster
Det finns två uppsättningar konfigurationsinställningar för tillförlitliga tjänster. En uppsättning är global för alla tillförlitliga tjänster i klustret medan den andra uppsättningen är specifik för en viss tillförlitlig tjänst.

## <a name="global-configuration"></a>Global konfiguration
Den globala tillförlitliga tjänstkonfigurationen anges i klustermanifestet för klustret under avsnittet KtlLogger. Det möjliggör konfiguration av den delade loggplatsen och storleken plus de globala minnesgränser som används av loggern. Klustermanifestet är en enda XML-fil som innehåller inställningar och konfigurationer som gäller för alla noder och tjänster i klustret. Filen kallas vanligtvis ClusterManifest.xml. Du kan se klustermanifestet för ditt kluster med kommandot Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Anmärkningar |
| --- | --- | --- | --- |
| SkrivBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minsta antal KB att allokera i kernel-läge för logger skriva buffert minne pool. Den här minnespoolen används för cachelagringstillståndsinformation innan du skriver till disk. |
| SkrivBufferMemoryPoolMaximumInKB |Kilobyte |Obegränsat |Maximal storlek som logger skriva buffert minne pool kan växa. |
| SharedLogId |GUID |"" |Anger ett unikt GUID som ska användas för att identifiera standarddokumenterade loggfilen som används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogId i sin tjänstspecifika konfiguration. Om SharedLogId har angetts måste även SharedLogPath anges. |
| SharedLogPath |Fullständigt kvalificerat sökvägsnamn |"" |Anger den fullständigt kvalificerade sökvägen där den delade loggfilen som används av alla tillförlitliga tjänster på alla noder i klustret som inte anger SharedLogPath i sin tjänstspecifika konfiguration. Men om SharedLogPath anges måste SharedLogId också anges. |
| SharedLogSizeInMB |Megabyte |8192 |Anger antalet MB diskutrymme som ska allokeras statiskt för den delade loggen. Värdet måste vara 2048 eller större. |

I Azure ARM eller lokal JSON-mall visar exemplet nedan hur du ändrar den delade transaktionsloggen som skapas för att stödja alla tillförlitliga samlingar för tillståndskänsliga tjänster.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Exempel på manifestavsnitt för lokalt utvecklarkluster
Om du vill ändra detta i din lokala utvecklingsmiljö måste du redigera den lokala clustermanifest.xml-filen.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Anmärkningar
Loggern har en global minnespool som allokerats från icke-sökt kernelminne som är tillgänglig för alla tillförlitliga tjänster på en nod för cachelagring av tillståndsdata innan den skrivs till den dedikerade loggen som är associerad med den tillförlitliga tjänstrepliken. Poolstorleken styrs av inställningarna WriteBufferMemoryPoolMinimumInKB och WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB anger både den ursprungliga storleken på den här minnespoolen och den lägsta storleken som minnespoolen kan krympa till. WriteBufferMemoryPoolMaximumInKB är den högsta storleken som minnespoolen kan växa till. Varje tillförlitlig tjänst replik som öppnas kan öka storleken på minnespoolen med ett system bestämd belopp upp till WriteBufferMemoryPoolMaximumInKB. Om det finns större efterfrågan på minne från minnespoolen än vad som är tillgängligt, kommer begäranden om minne att fördröjas tills minnet är tillgängligt. Därför om skrivbuffertminnespoolen är för liten för en viss konfiguration kan prestanda uppstå.

Inställningarna SharedLogId och SharedLogPath används alltid tillsammans för att definiera GUID och plats för standardloggen för alla noder i klustret. Standardloggen för delad logg används för alla tillförlitliga tjänster som inte anger inställningarna i settings.xml för den specifika tjänsten. För bästa prestanda bör delade loggfiler placeras på diskar som endast används för den delade loggfilen för att minska konkurrens.

SharedLogSizeInMB anger hur mycket diskutrymme som ska förallokeras för standarddelloggen för alla noder.  SharedLogId och SharedLogPath behöver inte anges för att SharedLogSizeInMB ska kunna anges.

## <a name="service-specific-configuration"></a>Tjänstspecifik konfiguration
Du kan ändra tillståndskänsliga Reliable Services standardkonfigurationer med hjälp av konfigurationspaketet (Config) eller tjänstimplementeringen (koden).

* **Config** - Konfiguration via config-paketet utförs genom att ändra filen Settings.xml som genereras i Microsoft Visual Studio-paketroten under config-mappen för varje tjänst i programmet.
* **Kod** - Konfiguration via kod uppnås genom att skapa en ReliableStateManager med hjälp av ett ReliableStateManagerConfiguration-objekt med lämplig alternativuppsättning.

Som standard söker Azure Service Fabric-körningen efter fördefinierade avsnittsnamn i filen Settings.xml och använder konfigurationsvärdena samtidigt som de underliggande körningskomponenterna skapas.

> [!NOTE]
> Ta **inte** bort avsnittsnamnen för följande konfigurationer i filen Settings.xml som genereras i Visual Studio-lösningen om du inte planerar att konfigurera tjänsten via kod.
> Om du byter namn på konfigurationspaketet eller avsnittsnamnen krävs en kodändring när ReliableStateManager konfigureras.
> 
> 

### <a name="replicator-security-configuration"></a>Säkerhetskonfiguration för Replikator
Replikatorsäkerhetskonfigurationer används för att skydda kommunikationskanalen som används under replikering. Detta innebär att tjänsterna inte kommer att kunna se varandras replikeringstrafik, vilket säkerställer att de data som görs mycket tillgängliga också är säkra. Som standard förhindrar ett tomt säkerhetskonfigurationsavsnitt replikeringssäkerhet.

> [!IMPORTANT]
> På Linux-noder måste certifikaten PEM-formaterade. Mer information om hur du hittar och konfigurerar certifikat för Linux finns i [Konfigurera certifikat på Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Standardavsnittsnamn
ReplicatorSecurityConfig

> [!NOTE]
> Om du vill ändra det här avsnittsnamnet åsidosätter du parametern replicatorSecuritySectionName till ReliableStateManagerConfiguration constructor när reliablestatemanager skapas för den här tjänsten.
> 
> 

### <a name="replicator-configuration"></a>Konfiguration av Replikator
Replikatorkonfigurationer konfigurerar replikatorn som är ansvarig för att göra den tillståndskänsliga tillförlitliga tjänstens tillstånd mycket tillförlitligt genom att replikera och bevara tillståndet lokalt.
Standardkonfigurationen genereras av Visual Studio-mallen och bör räcka. I det här avsnittet beskrivs ytterligare konfigurationer som är tillgängliga för att finjustera replikatorn.

### <a name="default-section-name"></a>Standardavsnittsnamn
ReplikatorKonfig

> [!NOTE]
> Om du vill ändra det här avsnittsnamnet åsidosätter du parametern replicatorSettingsSectionName till ReliableStateManagerConfiguration constructor när reliablestatemanager för den här tjänsten skapas.
> 
> 

### <a name="configuration-names"></a>Konfigurationsnamn
| Namn | Enhet | Standardvärde | Anmärkningar |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekunder |0.015 |Tidsperiod för vilken replikatorn vid den sekundära väntar efter att ha fått en åtgärd innan du skickar tillbaka en bekräftelse till den primära. Alla andra bekräftelser som ska skickas för åtgärder som bearbetas inom det här intervallet skickas som ett svar. |
| ReplicatorEndpoint |Ej tillämpligt |Ingen parameter som krävs för standard |IP-adress och port som den primära/sekundära replikatorn använder för att kommunicera med andra replikatorer i replikuppsättningen. Detta bör referera till en TCP-resursslutpunkt i tjänstmanifestet. Se [Tjänstmanifestresurser](service-fabric-service-manifest-resources.md) om du vill läsa mer om hur du definierar slutpunktsresurser i ett tjänstmanifest. |
| MaxPrimaryReplicationQueueSize |Antal operationer |8192 |Maximalt antal operationer i den primära kön. En åtgärd frigörs när den primära replikatorn tar emot en bekräftelse från alla sekundära replikatorer. Det här värdet måste vara större än 64 och en effekt på 2. |
| MaxSecondaryReplicationQueueSize |Antal operationer |16384 |Maximalt antal operationer i den sekundära kön. En operation frigörs efter att ha gjort sitt tillstånd mycket tillgängligt genom uthållighet. Det här värdet måste vara större än 64 och en effekt på 2. |
| CheckpointThresholdInMB |MB |50 |Mängden loggfilsutrymme efter vilket tillståndet är kontrollområde. |
| MaxRecordSizeInKB |KB |1024 |Största poststorlek som replikatorn kan skriva i loggen. Det här värdet måste vara en multipel av 4 och större än 16. |
| MinLogSizeInMB |MB |0 (systemet bestämt) |Minsta storlek på transaktionsloggen. Loggen kommer inte att tillåtas att trunkera till en storlek under den här inställningen. 0 anger att replikatorn bestämmer den minsta loggstorleken. Om du ökar det här värdet ökar möjligheten att göra partiella kopior och inkrementella säkerhetskopior eftersom risken för att relevanta loggposter trunkeras sänks. |
| TrunkeringThresholdFactor |Faktor |2 |Bestämmer vid vilken storlek loggen är, kommer trunkering att utlösas. Trunkering tröskel bestäms av MinLogSizeInMB multiplicerat med TruncationThresholdFactor. TrunkeringThresholdFactor måste vara större än 1. MinLogSizeInMB * TrunkeringThresholdFactor måste vara mindre än MaxStreamSizeInMB. |
| StrypningThresholdFactor |Faktor |4 |Bestämmer vid vilken storlek loggen är, kommer repliken att börja begränsas. Begränsningströskeln (i MB) bestäms av Max((MinLogSizeInMB * BegränsningThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Begränsningströskeln (i MB) måste vara större än trunkeringströskeln (i MB). Trunkeringströskel (i MB) måste vara mindre än MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Max ackumulerad storlek (i MB) av backup loggar i en viss backup loggkedja. En inkrementell säkerhetskopieringsbegäranden misslyckas om den inkrementella säkerhetskopieringen skulle generera en säkerhetskopia som skulle orsaka att de ackumulerade säkerhetskopieringsloggarna eftersom den relevanta fullständiga säkerhetskopian är större än den här storleken. I sådana fall krävs användaren för att göra en fullständig säkerhetskopiering. |
| SharedLogId |GUID |"" |Anger ett unikt GUID som ska användas för att identifiera den delade loggfilen som används med den här repliken. Vanligtvis bör tjänster inte använda den här inställningen. Men om SharedLogId anges måste SharedLogPath också anges. |
| SharedLogPath |Fullständigt kvalificerat sökvägsnamn |"" |Anger den fullständigt kvalificerade sökvägen där den delade loggfilen för den här repliken ska skapas. Vanligtvis bör tjänster inte använda den här inställningen. Men om SharedLogPath anges måste SharedLogId också anges. |
| SlowApiMonitoringDuration |Sekunder |300 |Anger övervakningsintervallet för hanterade API-anrop. Exempel: användaren som backup motringningsfunktion. När intervallet har passerat skickas en varningshälsorapport till hälsohanteraren. |
| LogTruncationIntervalSeconds |Sekunder |0 |Konfigurerbart intervall med vilket logg trunkering initieras på varje replik. Det används för att säkerställa loggen är också trunkeras baserat på tid istället för bara loggstorlek. Den här inställningen tvingar också rensning av borttagna poster i tillförlitlig ordlista. Därför kan den användas för att säkerställa att borttagna objekt rensas i tid. |
| EnableStableReads |Boolean |False |Om du aktiverar stabila läsningar begränsas sekundära repliker till att returnera värden som har kvorum-acked. |

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
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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


### <a name="remarks"></a>Anmärkningar
BatchAcknowledgementInterval styr replikeringsfördröjningen. Värdet "0" resulterar i lägsta möjliga latens, på bekostnad av dataflöde (eftersom fler bekräftelsemeddelanden måste skickas och bearbetas, var och en med färre bekräftelser).
Ju större värde för BatchAcknowledgementInterval, desto högre är det totala replikeringsdataflödet, till kostnaden för högre operationsfördröjning. Detta översätts direkt till svarstiden för transaktionsförseningar.

Värdet för CheckpointThresholdInMB styr mängden diskutrymme som replikatorn kan använda för att lagra tillståndsinformation i replikens dedikerade loggfil. Om du ökar detta till ett högre värde än standardvärdet kan det leda till snabbare omkonfigurationstider när en ny replik läggs till i uppsättningen. Detta beror på den partiella tillståndsöverföringen som sker på grund av tillgängligheten för mer historik över åtgärder i loggen. Detta kan potentiellt öka återställningstiden för en replik efter en krasch.

Inställningen MaxRecordSizeInKB definierar den maximala storleken på en post som kan skrivas av replikatorn i loggfilen. I de flesta fall är standardpoststorleken på 1024 KB optimal. Men om tjänsten orsakar större dataobjekt att vara en del av tillståndsinformationen, kan det här värdet behöva ökas. Det finns liten nytta med att göra MaxRecordSizeInKB mindre än 1024, eftersom mindre poster bara använder det utrymme som behövs för den mindre posten. Vi förväntar oss att detta värde skulle behöva ändras i endast sällsynta fall.

Inställningarna SharedLogId och SharedLogPath används alltid tillsammans för att göra en tjänst använda en separat delad logg från standardloggen för noden. För bästa effektivitet bör så många tjänster som möjligt ange samma delade logg. Delade loggfiler bör placeras på diskar som endast används för den delade loggfilen för att minska huvudförflyttningskonkurrensen. Vi förväntar oss att detta värde skulle behöva ändras i endast sällsynta fall.

## <a name="next-steps"></a>Nästa steg
* [Felsöka ditt Service Fabric-program i Visual Studio](service-fabric-debugging-your-application.md)
* [Utvecklarreferens för reliable services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

