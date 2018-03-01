---
title: Azure Service Fabric CLI - sfctl program | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl program-kommandon.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 3a10437d0a2d680e586ada6a87750a69453c1f0c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-application"></a>sfctl application
Skapa, ta bort och hantera program och programtyper.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| skapa       | Skapar ett Service Fabric-program med den angivna beskrivningen.|
| radera       | Tar bort ett befintligt Service Fabric-program.|
| Distribueras     | Hämtar information om ett program distribueras på ett Service Fabric-nod.|
| deployed-health | Hämtar information om hälsotillståndet för ett program som distribuerats på en Service Fabric-nod.|
| distribuerat lista| Hämtar listan över program som distribueras på ett Service Fabric-nod.|
| hälsa       | Hämtar hälsotillståndet för service fabric-programmet.|
| info         | Hämtar information om ett Service Fabric-program.|
| lista         | Hämtar listan över program som har skapats i Service Fabric-klustret som matchar filtren anges som parameter.|
| läsa in | Hämtar att läsa in information om ett Service Fabric-program. |
| Manifestet     | Hämtar manifestet som beskriver en typ av program.|
| Etablera    | Tillhandahåller eller registrerar skriver ett Service Fabric-program med klustret med .sfpkg paketet i externa store eller använda programpaketet i image store.|
| report-health| Skickar en hälsorapport för Service Fabric-programmet.|
| typ         | Hämtar listan över programtyper i Service Fabric-kluster som matchar exakt det angivna namnet.|
| Ange-lista    | Hämtar listan över programtyper i Service Fabric-klustret.|
| Avetablera  | Tar bort eller Avregistrerar en Service Fabric programtyp från klustret.|
| Uppgradera      | Börja uppgradera ett program i Service Fabric-klustret.|
| återuppta uppgradering  | Återupptar uppgradera ett program i Service Fabric-klustret.|
| upgrade-rollback| Startar återställer för närvarande löpande uppgradering av ett program i Service Fabric-klustret.|
| uppgradera status  | Hämtar information för den senaste uppgraderingen utförs på det här programmet.|
| Ladda upp       | Kopiera ett Service Fabric-programpaket till image store.|

## <a name="sfctl-application-create"></a>Skapa sfctl program
Skapar ett Service Fabric-program med den angivna beskrivningen.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| – appens namn-[krävs]| Namnet på programmet, inklusive den ”fabric:' URI-schema.|
| appen--typ [krävs]| Typnamn program finns i programmanifestet.|
| --app-versionen [krävs]| Versionen av programtyp som definierats i programmanifestet.|
| --max-node-count     | Maximalt antal noder att Service Fabric reserverar kapacitet för det här programmet. Detta innebär inte att tjänster i det här programmet är placerade på alla dessa noder.|
| --mått            | En JSON-kodade lista över program kapacitet mått beskrivningar. Ett mått har definierats som ett namn som är associerad med en uppsättning kapaciteter för varje nod som programmet finns på.|
| --min nodsantalet     | Det minsta antalet noder att Service Fabric reserverar kapacitet för det här programmet. Detta innebär inte att tjänster i det här programmet är placerade på alla dessa noder.|
| --parameters         | En JSON-kodade lista över program parametern åsidosättningar för att användas när du skapar programmet.|
| --timeout -t         | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug              | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h            | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o          | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga              | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose            | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-delete"></a>ta bort sfctl-program
Tar bort ett befintligt Service Fabric-program.

Tar bort ett befintligt Service Fabric-program. Ett program måste skapas innan den kan tas bort. Tar bort ett program alla tjänster som ingår i programmet. Som standard försöker Service Fabric Stäng tjänsten repliker på ett korrekt sätt och ta bort tjänsten. Men om tjänsten har problem med att stänga repliken, delete-åtgärden kan ta lång tid eller fastnar. Använda flaggan valfria ForceRemove hoppa över korrekt sekvensstängning och ta bort programmet och alla dess tjänster tvång.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs]| Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Om programnamnet är till exempel ”fabric: / myapp/app1”, programidentiteten skulle vara ”myapp ~ app1” i 6.0 + och ”myapp/app1” i tidigare versioner.|
| --force-remove          | Ta bort ett Service Fabric-program eller tjänst tvång utan att gå igenom alla korrekt avslutning. Den här parametern kan användas för att tvång ta bort ett program eller tjänst för vilka borttagning är avbryts på grund av problem i den kod som förhindrar korrekt Stäng av replikerna.|
| --timeout -t            | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                 | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h               | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o             | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                 | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose               | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-deployed"></a>sfctl program distribueras
Hämtar information om ett program distribueras på ett Service Fabric-nod.

Hämtar information om ett program distribueras på ett Service Fabric-nod.  Den här frågan returnerar information om programmet om det program-ID som angetts för system-program. Resultaten omfattar distribuerade program i aktiva, aktivera och hämtar tillstånd. Den här frågan kräver att nodnamnet motsvarar en nod i klustret. Frågan misslyckas om det angivna nodnamnet inte pekar på någon aktiv Service Fabric-noder i klustret.
     
### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs]| Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Om programnamnet är till exempel ”fabric: / myapp/app1”, programidentiteten skulle vara ”myapp ~ app1” i 6.0 + och ”myapp/app1” i tidigare versioner.|
| --nodnamn [krävs]| Namnet på noden.|
| --timeout -t            | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                 | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h               | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o             | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                 | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose               | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-health"></a>sfctl programs hälsa
Hämtar hälsotillståndet för service fabric-programmet.

Returnerar hed tillståndet för service fabric-programmet. Svaret rapporterar hälsotillståndet Ok, fel eller varning. Om enheten inte hittas i health store, returnerar fel.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs]| Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Om programnamnet är till exempel ”fabric: / myapp/app1”, programidentiteten skulle vara ”myapp ~ app1” i 6.0 + och ”myapp/app1” i tidigare versioner.|
| --deployed-applications-health-state-filter| Tillåter filtrering av distribuerade program hälsa tillstånd objekt returneras i resultatet av programmets hälsotillstånd fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast distribuerade program som matchar filtret kommer att returneras. Alla distribuerade program används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 hälsotillståndet för distribuerade program med HealthState värdet OK (2) och varning (4) returneras. -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
| --events-health-state-filter            | Tillåter filtrering objektsamlingen HealthEvent returnerade baseras på hälsotillståndet. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Händelser som matchar filtret returneras. Alla händelser som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras alla händelser med HealthState värdet OK (2) och varning (4). -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
| --Utelämna hälsostatistik | Anger om hälsostatistik ska returneras som en del av frågeresultatet. FALSKT som standard. Statistik visar antalet underordnade entiteter i hälsotillstånd Ok, varning och fel.|
| --services-health-state-filter          | Tillåter filtrering av tjänster hälsa tillstånd objekt returneras i resultatet av tjänster hälsa fråga baserat på deras hälsostatus. De möjliga värdena för den här parametern innehåller heltalsvärdet för något av följande hälsotillstånd. Endast tjänster som matchar filtret returneras. Alla tjänster som används för att utvärdera aggregerade hälsotillståndet. Om inget anges returneras alla poster. Värdena är uppräkning med flaggan så värdet kan vara en kombination av dessa värden som erhålls med hjälp av en Bitvis ”OR-operator. Till exempel om det angivna värdet är 6 returneras sedan hälsotillståndet för tjänster med HealthState värdet OK (2) och varning (4). -Standard - standardvärde. Matchar alla HealthState. Värdet är noll. -Ingen - Filter som inte matchar något värde för HealthState. Används för att returnera resultat på en viss samling av tillstånd. Värdet är 1. -Filtrera ok - som matchar matas in med HealthState värde Ok. Värdet är 2. -Varning - Filter som matchar med HealthState inmatningsvärdet varning. Värdet är 4. -Fel - Filter som matchar indata med HealthState värdet fel. Värdet är 8. -Alla - Filter som matchar indata med ett värde för HealthState. Värdet är 65535.|
| --timeout -t                            | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                                 | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                               | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                             | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                                 | JMESPath frågesträngen. Mer information finns i http://jmespath.org/.|
| -verbose                               | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-info"></a>sfctl programinformation
Hämtar information om ett Service Fabric-program.

Returnerar information om det program som har skapats eller håller på att skapas i Service Fabric-kluster och vars namn matchar den som anges som parameter. Svaret innehåller namn, typ, status, parametrar och annan information om programmet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-id [krävs]| Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Om programnamnet är till exempel ”fabric: / myapp/app1”, programidentiteten skulle vara ”myapp ~ app1” i 6.0 + och ”myapp/app1” i tidigare versioner.|
| --exclude-application-parameters| Flagga som anger om programmet parametrar kommer att uteslutas från resultatet.|
| --timeout -t                 | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                      | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                    | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                  | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.             Standard: json.|
| --fråga                      | JMESPath frågesträngen. Mer information finns i http://jmespath.org/.|
| -verbose                    | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-list"></a>sfctl lista
Hämtar listan över program som har skapats i Service Fabric-klustret som matchar filtren anges som parameter.

Hämtar information om de program som har skapats eller håller på att skapas i Service Fabric-kluster och matchar filtren anges som parameter. Svaret innehåller namn, typ, status, parametrar och annan information om programmet. Om programmen inte får plats på en sida, returneras en sida av resultat samt fortsättningstoken som kan användas för att hämta nästa sida. Filter ApplicationTypeName och ApplicationDefinitionKindFilter kan inte anges samtidigt.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
|--application-definition-kind-filter| Används för att filtrera på ApplicationDefinitionKind som är en mekanism som används för att definiera ett Service Fabric-program. -Standard - standardvärde som utför samma funktion som du väljer ”alla”. Värdet är 0. -Alla - Filter som matchar indata med ett ApplicationDefinitionKind-värde. Värdet är 65535. -ServiceFabricApplicationDescription - Filter som matchar indata med ApplicationDefinitionKind värdet ServiceFabricApplicationDescription. Värdet är 1. -Skriv - Filter som matchar indata med ApplicationDefinitionKind värdet Compose. Värdet är 2.|
| --application-type-name      | Programnamnet för typ som används för att filtrera de program som ska fråga efter. Det här värdet får inte innehålla typen programversionen.|
| --continuation-token         | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --exclude-application-parameters| Flagga som anger om programmet parametrar är undantagna från resultatet.|
| --max-results|Högsta antal resultat ska returneras som en del av växlingsbar frågor. Den här parametern anger den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan vara mindre än de angivna maximala resultat om de inte passar i meddelandet enligt max meddelandet storlek restriktioner definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkludera växlingsbara frågorna så många resultat som möjligt som ryms i svarsmeddelandet.|
| --timeout -t                 | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                      | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                    | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                  | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.             Standard: json.|
| --fråga                      | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose                    | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-load"></a>sfctl programinläsning
Hämtar att läsa in information om ett Service Fabric-program.

Returnerar läsa in information om programmet som har skapats eller håller på att skapas i Service Fabric-kluster och vars namn matchar den som anges som parameter. Svaret innehåller namnet, minsta noder, maximalt antal noder, antalet noder som appen för närvarande använder och Läs in mått programinformation om programmet.

### <a name="arguments"></a>Argument
|Argumentet|Beskrivning|
| --- | --- |
|--program-id [krävs]| Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ”~” tecken. Om programnamnet är till exempel ”fabric: / myapp/app1”, programidentiteten skulle vara ”myapp ~ app1” i 6.0 + och ”myapp/app1” i tidigare versioner. |
| --timeout -t               | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument
|Argumentet|Beskrivning|
| --- | --- |
|--debug                    | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
    --hjälp -h                  | Visa den här hjälpmeddelandet och avsluta.|
    --utdata -o                | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
    --fråga                    | JMESPath frågesträngen. Mer information finns i http://jmespath.org/.|
    -verbose                  | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-manifest"></a>sfctl programmanifestet
Hämtar manifestet som beskriver en typ av program.
        
Hämtar manifestet som beskriver en typ av program. Svaret innehåller programmanifestet XML som en sträng.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-typnamn [krävs]| Namnet på programtyp.|
| --program-type-version [krävs]| Versionen av programtypen.|
| --timeout -t                      | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                           | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                         | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                       | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.                  Standard: json.|
| --fråga                           | JMESPath frågesträngen. Mer information finns i http://jmespath.org/.|
| -verbose                         | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-provision"></a>sfctl programmet etablera
Tillhandahåller eller registrerar skriver ett Service Fabric-program med klustret med SFPKG paketet i externa store eller använda programpaketet i image store.

Etablerar ett Service Fabric programtyp med klustret. Detta krävs innan några nya program kan initieras. Etableringsåtgärden kan utföras antingen på programpaket som anges av relativePathInImageStore eller med hjälp av URI för externa SFPKG. Om inte--externa etablera anges förväntar det här kommandot avbildningsarkivet

etablera.
        


### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --application-package-download-uri| Sökvägen till '.sfpkg' programpaket varifrån programpaketet kan hämtas med hjälp av HTTP eller HTTPS-protokoll. För att etablera från en extern butik. Programpaketet kan lagras i en extern butik som innehåller GET-åtgärden för att hämta filen. Protokoll som stöds är HTTP och HTTPS och sökvägen måste tillåta läsåtkomst.|
| --application-type-build-path       | För att etablera kind avbildningsarkivet endast. Den relativa sökvägen för programpaket i image store anges under föregående överföringen. |
| --application-type-name| För att etablera från en extern butik. Namnet på programmet står namnet på programtyp som hittades i programmanifestet.|
| --application-type-version| För att etablera från en extern butik. Programmets Typversion representerar versionen av programtyp som hittades i programmanifestet.|
| --externa etablera| Platsen där programpaket kan registreras eller etableras. Anger att tillhandahållande av ett programpaket som tidigare har överförts till en extern butik. Programpaketet slutar med tillägget *.sfpkg.|
| --no-wait| Anger huruvida etablering ska ske asynkront.  Om värdet är true, etableringsåtgärd returnerar fortsätter när begäran har godkänts av systemet och etableringsåtgärden utan timeout-gränsen. Standardvärdet är false. För stora programpaket rekommenderar vi att ange värdet till true.|
| --timeout -t                      | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                              | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                            | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                          | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga                              | JMESPath frågesträngen. Mer information finns i http://jmespath.org/.|
| -verbose                            | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-type"></a>sfctl programtyp

Hämtar listan över programtyper i Service Fabric-kluster som matchar exakt det angivna namnet.

Returnerar information om vilka programtyper av som tillhandahålls eller håller på att tillhandahålls i Service Fabric-klustret. De här resultaten är programtyper vars namn matchar exakt angiven som parameter och som uppfyller de angivna frågeparametrarna. Alla versioner av programtyp som matchar namnet på programmet returneras för varje version som returneras som en typ av program. Svaret innehåller namn, version, status och annan information om vilken programtyp. Detta är en växlingsbar fråga, vilket innebär att som om inte alla programtyper får plats i en sida, returneras en sida med resultat samt fortsättningstoken som kan användas för att hämta nästa sida. Till exempel är om det finns 10 programtyper men en sida passar bara de första 3 programtyperna eller om max resultat är inställda på 3, 3 returneras. Hämta de efterföljande sidorna med returnerade fortsättningstoken i nästa fråga för att komma åt resten av resultaten. En tom fortsättningstoken returneras om det finns inga kommande sidor.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --application-type-name [Required]| Namnet på programtyp.|
| --application-type-version        | Versionen av programtypen.|
| --continuation-token           | Parametern fortsättning token för att hämta nästa uppsättning resultat. En fortsättningstoken med ett icke-tom värde ingår i svaret API när resultaten från systemet inte ryms i ett enda svar. När det här värdet skickas till nästa API-anrop till API Returnerar nästa uppsättning resultat. Om det finns inga ytterligare resultat, sedan innehåller fortsättningstoken inte något värde. Värdet för den här parametern får inte vara kodad URL.|
| --exclude-application-parameters  | Flagga som anger om programmet parametrar kommer att uteslutas från resultatet.|
| --max-results                  | Högsta antal resultat ska returneras som en del av växlingsbar frågor. Den här parametern anger den övre gränsen för antalet resultat som returneras. Resultatet som returneras kan vara mindre än de angivna maximala resultat om de inte passar i meddelandet enligt max meddelandet storlek restriktioner definieras i konfigurationen. Om den här parametern är noll eller inte har angetts, inkluderar växlingsbara frågan så många resultat som möjligt som ryms i svarsmeddelandet.|
| --timeout -t                   | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                        | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                      | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                    | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.               Standard: json.|
| --fråga                        | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose                      | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-unprovision"></a>sfctl program avetablera
Tar bort eller Avregistrerar en Service Fabric programtyp från klustret.

Tar bort eller Avregistrerar en Service Fabric programtyp från klustret. Den här åtgärden kan endast utföras om alla programinstans av typen programmet har tagits bort. När programtypen är avregistrerade, kan inga nya programinstansen skapas för den här typen av program.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --program-typnamn [krävs]| Namnet på programtyp.|
| --program-type-version [krävs]| Versionen av programtyp som definierats i programmanifestet.|
|--async-parameter                    | Flagga som anger huruvida avetablera ska ske asynkront. Om värdet är true och returnerar avetablera igen fortsätter när begäran har godkänts av systemet och avetablera åtgärden utan timeout-gränsen. Standardvärdet är false. Vi rekommenderar dock ställas till true för stora programpaket som etablerades.|
| --timeout -t                      | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                           | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                         | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                       | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.                  Standard: json.|
| --fråga                           | JMESPath frågesträngen. Mer information finns i http://jmespath.org/.|
| -verbose                         | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-upgrade"></a>uppgradering av sfctl programmet
Börja uppgradera ett program i Service Fabric-klustret.

Validerar angivna uppgradera applikationsparametrarna och börja uppgradera programmet om parametrarna är giltiga. Uppgradera beskrivning ersätter Programbeskrivningen av befintliga. Det innebär att om parametrarna inte har angetts, befintliga parametrar på program skrivs över med listan tom parametrar. Detta resulterar i program med hjälp av standardvärdet för parametrarna från programmanifestet.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --app-id [krävs]| Identiteten för programmet. Detta är vanligtvis det fullständiga namnet på programmet utan den ”fabric:' URI-schema. Från och med version 6.0, hierarkiska namn är avgränsade med den ' ~' tecken. Om programnamnet är till exempel ”fabric: / myapp/app1”, programidentiteten skulle vara ' myapp ~ app1' i 6.0 + och ' myapp/app1 ”i tidigare versioner.|
| --app-versionen [krävs]| Målversionen för programmet.|
| --Parametrar [krävs]| En JSON-kodade lista över program parametern åsidosättningar för att användas när du uppgraderar programmet.|
| --default-service-health-policy| JSON-kodade specificering av hälsoprincipen används som standard för att utvärdera hälsan för en tjänsttyp.|
| --åtgärd vid            | Åtgärden som ska utföras vid en uppgradering av övervakade övervakning princip- eller principöverträdelser.|
| --force-restart             | Starta tvång om processer under uppgraderingen även när code-versionen inte har ändrats.|
| --health-check-retry-timeout| Hur lång tid att försöka hälsa utvärderingar när programmet eller kluster är felfri innan den misslyckade åtgärden körs. Mätt i millisekunder.  Standard: PT0H10M0S.|
| --health-check-stable-duration | Hur lång tid att programmet eller klustret måste vara felfria innan uppgraderingen fortsätter till nästa uppgraderingsdomän.            Mätt i millisekunder.  Standard: PT0H2M0S.|
| --health-check-wait-duration| Mängden väntetiden när du har slutfört en uppgraderingsdomän innan du tillämpar hälsoprinciper. Mätt i millisekunder.            Standard: 0.|
| --max ohälsosamt appar        | Maximalt tillåten procentandel av felaktiga distribuerade program. Representeras som ett tal mellan 0 och 100.|
| --läge                      | Det läge som används för att övervaka hälsa under en uppgradering.            Standard: UnmonitoredAuto.|
| --replica-set-check-timeout | Längsta tid att blockera bearbetning av en uppgraderingsdomän och förhindra förlust av tillgänglighet när det uppstår oväntade problem. Mäts i sekunder.|
| --service-health-policy     | JSON-kodade karta med tjänsten typen hälsoprincip per tjänstnamnet typen. Kartan är tom vara standard.|
| --timeout -t                | Servern tidsgräns i sekunder.  Standard: 60.|
| --upgrade-domain-timeout    | Hur lång tid varje uppgraderingsdomänen måste slutföras innan FailureAction körs. Mätt i millisekunder.  Default:            P10675199DT02H48M05.4775807S.|
| --upgrade-timeout           | Hur lång tid övergripande uppgraderingen har slutförts innan FailureAction körs. Mätt i millisekunder.  Default:            P10675199DT02H48M05.4775807S.|
| --warning-as-error          | Behandla hälsovarningar i utvärderingen med samma allvarlighetsgrad som fel.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug                     | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h                   | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o                 | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.            Standard: json.|
| --fråga                     | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose                   | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-application-upload"></a>sfctl program överför
Kopiera ett Service Fabric-programpaket till image store.

Du kan också visa överföringen pågår för varje fil i paketet. Ladda upp förloppet skickas till `stderr`.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --sökväg [krävs]| Sökvägen till lokala programpaket.|
|--imagestore-string| Mål-avbildningen lagra för att överföra programpaket till.  Standard: fabric: Avbildningsarkiv.|
| – Visa förlopp  | Visa förlopp för överföring av filen för stora paket.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| --debug       | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h     | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o   | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga       | JMESPath frågesträngen. Mer information finns i http://jmespath.org/.|
| -verbose     | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).
