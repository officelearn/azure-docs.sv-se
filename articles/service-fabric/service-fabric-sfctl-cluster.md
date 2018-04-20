---
title: Azure Service Fabric CLI - sfctl klustret | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl cluster-kommandon.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: c83dc3eeb6ca0d66b0c70236354fd7bab80f355f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-cluster"></a>sfctl cluster
Välj, hantera och driva Service Fabric-kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
|    koden-versioner| Hämtar en lista över fabric code-versioner som tillhandahålls i ett Service Fabric-kluster.|
|    config-versioner | Hämtar en lista över fabric config-versioner som tillhandahålls i ett Service Fabric-kluster.|
|    hälsa       | Hämtar hälsotillståndet för ett Service Fabric-kluster.|
|    Manifestet     | Hämta Service Fabric-klustermanifestet.|
|    Åtgärden Avbryt| Avbryter en egen användaren felåtgärd.|
|    operationgit | Hämtar en lista över användare framkallas fel åtgärder filtreras efter angivna indata.|
|    Etablera     | Etablera kod eller konfigurationsfil paket i ett Service Fabric-kluster.|
|    recover-system  | Anger att den ska försöka återställa systemtjänster som för närvarande har fastnat i kvorumförlust till Service Fabric-klustret.|
|rapporten hälsa   | Skickar en hälsorapport på Service Fabric-klustret.|
|    välj       | Ansluter till en slutpunkt för Service Fabric-klustret.|
| Avetablera     | Avetablera kod eller konfigurationsfil paket i ett Service Fabric-kluster.|
|    Uppgradera         | Börja uppgradera kod eller konfigurationsfil version av Service Fabric-klustret.|
|    återuppta uppgradering  | Se klusteruppgradering gå vidare till nästa uppgraderingsdomän.|
|    uppgraderingen återställning| Återställa uppgradering av Service Fabric-klustret.|
|    uppgradera status  | Hämtar status för klustret uppgraderingen.|
|uppgraderingen-uppdatering  | Uppdatera uppgradera parametrarna för en uppgradering av Service Fabric-klustret.|


## <a name="sfctl-cluster-health"></a>sfctl klustret hälsa
Hämtar hälsotillståndet för ett Service Fabric-kluster.

Hämtar hälsotillståndet för ett Service Fabric-kluster. Använd EventsHealthStateFilter för att filtrera insamling av hälsotillstånd händelser som rapporterats i klustret som baseras på hälsotillståndet. På liknande sätt använder NodesHealthStateFilter ApplicationsHealthStateFilter att filtrera uppsättning noder och program som returneras baserat på deras aggregerade hälsotillstånd. 

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --applications-health-state-filter| Tillåter filtrering av programmets hälsotillstånd tillstånd objekten som returneras i resultatet av klustret hälsa fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärde från medlemmar eller binär åtgärder på medlemmar i HealthStateFilter-uppräkningen. Endast program som matchar filtret returneras.  Alla program som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är flaggan baserade uppräkning, så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för program med HealthState värdet OK (2) och varning (4) returneras. -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
| --events-health-state-filter   | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är flaggan - baserade uppräkning, så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4). -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning.  Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
|--Utelämna hälsostatistik                   | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel.|
 |   --include-system-application-health-statistics| Anger om hälsostatistik bör innehålla i fabric: / System hälsostatistik för programmet. FALSKT som standard. Om IncludeSystemApplicationHealthStatistics har angetts till true, hälsotillståndet statistiken omfattar de enheter som tillhör i fabric: / systemprogram. Frågeresultatet innehåller annars hälsostatistik endast för program. Hälsostatistik måste inkluderas i frågeresultatet för den här parametern ska tillämpas.|
| --nodes-health-state-filter    | Tillåter filtrering av noden hälsa tillstånd objekten som returneras i resultatet av klustret hälsa fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast de noder som matchar filtret returneras. Alla noder för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är ”6” hälsotillståndet för noder med HealthState värdet OK (2) och varning (4) returneras. -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning.  Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
| --timeout -t                   | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning                        | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                      | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                    | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.                    Standard: json.|
| --fråga                        | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose                      | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-cluster-manifest"></a>sfctl klustermanifestet
Hämta Service Fabric-klustermanifestet.

Hämta Service Fabric-klustermanifestet. Klustermanifestet innehåller egenskaper för klustret med olika nodtyper på klustret, säkerhetskonfigurationer, fel och uppgraderingsdomänen topologier osv. Dessa egenskaper anges som en del av filen ClusterConfig.JSON vid distribution av ett fristående kluster. Men de flesta av informationen i klustermanifestet genereras internt av service fabric under Klusterdistribution i andra scenarier för distribution (till exempel när du använder den [Azure-portalen](https://portal.azure.com)). Innehållet i klustermanifestet är endast i informationssyfte och användare förväntas inte koppla ett beroende på formatet på filens innehåll eller dess tolkning. 

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --timeout -t| Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning  | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h| Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga  | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose| Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-cluster-provision"></a>sfctl klustret etablera
Etablera kod eller konfigurationsfil paket i ett Service Fabric-kluster.
Validera och etablera kod eller konfigurationsfil paket i ett Service Fabric-kluster.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
|--kluster-manifest--filsökväg| Klustret manifestfilen sökvägen.|
|    --koden filsökväg            | Filsökvägen för klustret kod paketet.|
|    --timeout -t                | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h  | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o| Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose  | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-cluster-select"></a>sfctl kluster väljer
Ansluter till en slutpunkt för Service Fabric-klustret.

Om ansluter till säker kluster, ange ett certifikat (.crt) och nyckelfilen (.key) eller en enstaka fil med båda (.pem). Ange inte båda. Du kan också om ansluter till en säker klustret, ska du även ange en sökväg till en CA paketfil eller katalog för certifikat för betrodd Certifikatutfärdare.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --endpoint [krävs]| Kluster slutpunkts-URL, inklusive porten och HTTP eller HTTPS-prefix.|
| --aad             | Använda Azure Active Directory för autentisering.|
| --certifikatutfärdare              | Sökvägen till CA-certifikat directory ska behandlas som giltiga eller Kanada paketfil.|
| --cert            | Sökvägen till en klient-certifikatfil.|
| --nyckel             | Sökvägen till nyckelfilen för klientens certifikat.|
| – Kontrollera Nej       | Inaktivera verifiering för certifikat när du använder HTTPS, Observera: Detta är en osäker alternativ och ska inte användas i produktionsmiljöer.|
| --pem             | Sökvägen till klientcertifikat som en PEM-filen.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning           | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h         | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o       | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga           | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose         | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-cluster-unprovision"></a>sfctl klustret avetablera
Avetablera kod eller konfigurationsfil paket i ett Service Fabric-kluster.

Avetablera kod eller konfigurationsfil paket i ett Service Fabric-kluster. Det går för att avetablera koden och konfiguration separat.

### <a name="arguments"></a>Argument
|Argumentet|Beskrivning|
| --- | --- |
|--code-version  | Paketversion för klustret kod.|
|    --config-version| Klustermanifestversionen.|
|    --timeout -t    | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument
|Argumentet|Beskrivning|
| --- | --- |
|– Felsökning         | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
 |   --hjälp -h       | Visa den här hjälpmeddelandet och avsluta.|
 |   --utdata -o     | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
 |   --fråga         | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
 |   -verbose       | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|


## <a name="sfctl-cluster-upgrade"></a>sfctl klusteruppgradering
Börja uppgradera kod eller konfigurationsfil version av Service Fabric-klustret.
Validera de angivna parametrarna för uppgraderingen och starta uppgraderingen kod eller konfigurationsfil version av Service Fabric-klustret om parametrarna är giltiga.

### <a name="arguments"></a>Argument
|Argumentet|Beskrivning|
| --- | --- |
|    --app hälsa mappning                      | JSON-kodade ordlista med par av namn och Maxprocent ohälsosamt innan du höjer fel.|
 |   --app typ-hälsa-mappning                 | JSON-kodade uppslagslista av par med programmets typnamn och Maxprocent ohälsosamt innan du höjer fel.|
 |   --code-version                        | Klustret code-versionen.|
 |   --config-version                      | Konfigurationsversionen för klustret.|
 |   --delta klienthälsa             | Aktiverar delta hälsoutvärderingen i stället för absoluta hälsoutvärderingen efter varje domän har slutförts.|
 |   --delta felaktiga noder               | Maximalt tillåten procentandel av noder hälsa försämras under uppgraderingar.  Standard: 10. Delta mäts mellan tillståndet i noderna i början av uppgradering och tillståndet i noderna vid tiden för utvärderingen hälsa. Kontrollen utförs efter varje uppgradera uppgraderingsdomänen har slutförts och kontrollera globalt tillstånd i klustret är tillåten begränsas.|
 |   --åtgärd vid                      | Möjliga värden är: 'Ogiltig ”,” återställa ”,” manuell ”.|
 |   --force-restart                       | Tvinga fram omstart.|
 |   --hälsa-check-återförsök                  | Health check försök timeout mätt i millisekunder.|
 |   --health check stabilt                 | Hälsokontroll stabil tid, mätt i millisekunder.|
  |  --hälsokontroll-                   | Health check vänta varaktighet mätt i millisekunder.|
  |  --replik-set-check-timeout           | Uppgradera replikuppsättningen Kontrollera timeout mätt i sekunder.|
 |   --löpande uppgradering-läge                | Möjliga värden är: ”Ogiltig', 'UnmonitoredAuto', 'UnmonitoredManual', 'Övervakade'.  Standard: UnmonitoredAuto.|
  |  --timeout -t                          | Servern tidsgräns i sekunder.  Standard: 60.|
  |  --felaktiga program              | Maximalt tillåten procentandel av felaktiga program innan ett fel rapporteras. Om du vill tillåta 10% av program feltillstånd exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av program som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst ett feltillstånd program, utvärderas hälsa som varning. Detta beräknas genom att dividera antalet felaktiga program över det totala antalet programinstanser i klustret, exklusive programtyper som ingår i ApplicationTypeHealthPolicyMap-applikationer. Beräkningen Avrundar uppåt till tolererar att ett fel på mindre antal program.|
 |   --felaktiga noder                     | Maximalt tillåten procentandel av noder med fel innan den rapporterar ett fel. Om du vill tillåta 10% av noder feltillstånd, exempelvis är det här värdet 10. Procentandelen representerar maximalt tillåten procentandel av noder som kan vara felaktiga innan klustret anses vara fel. Om procentandelen följs men det finns minst en nod i feltillstånd, utvärderas hälsa som varning. Procentandelen beräknas genom att dividera antalet felaktiga noder över det totala antalet noder i klustret. Beräkningen Avrundar uppåt till tolererar att ett fel på litet antal noder. I stora kluster kommer vissa noder alltid att ned eller ut för reparationer, så den här procentandelen ska konfigureras för att tolerera som.|
 |   --upgrade-domain-delta-unhealthy-nodes| Största tillåtna procentandelen uppgraderingsdomänen noder hälsa försämras under uppgraderingar. Standard: 15. Delta mäts mellan tillståndet i uppgraderingsdomänen noderna i början av uppgradering och tillståndet för uppgraderingsdomänen noder vid tiden för utvärderingen hälsa. Kontrollen utförs när varje uppgraderingsdomänen uppgradera slutförande för alla slutförts uppgraderingsdomäner att kontrollera tillståndet för uppgraderingsdomäner är tillåten begränsas.|
 |   --uppgraderingen domäntidsgräns              | Uppgraderingsdomänen mätt i millisekunder.|
 |   --uppgraderingen-timeout                     | Tidsgränsen för uppgradering mätt i millisekunder.|
 |   --warning-as-error                    | Varningar behandlas med samma allvarlighetsgrad som fel.|

### <a name="global-arguments"></a>Globala argument
|Argumentet|Beskrivning|
| --- | --- |
|– Felsökning                               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
|    --hjälp -h                             | Visa den här hjälpmeddelandet och avsluta.|
|    --utdata -o                           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs. Standard: json.|
|    --fråga                               | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
|    -verbose                             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).