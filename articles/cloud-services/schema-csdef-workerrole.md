---
title: Azure Cloud Services Def. WorkerRole-Schema | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 90a11c5bb81a0d29f5f8a1c1696732453aa4b1ab
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095413"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Azure Cloud Services, Definition WorkerRole-Schema
Azure-arbetsroll är en roll som används för generaliserad utveckling och kan utföra Bakgrundsbearbetning för en webbroll.

Standardfilnamnstillägget för tjänstdefinitionsfilen är .csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Grundläggande tjänstdefinitionsschemat för en arbetsroll.
Det grundläggande formatet för tjänstdefinitionsfilen som innehåller en arbetsroll är som följer.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
    <LocalResources>
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    </LocalResources>
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    <Runtime executionContext="[limited|elevated]">
      <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
      </Environment>
      <EntryPoint>
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]" "/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>”>
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
        <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
        </Environment>
      </Task>
    </Startup>
    <Contents>
      <Content destination="<destination-folder-name>" >
        <SourceDirectory path="<local-source-directory>" />
      </Content>
    </Contents>
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schemaelement
Tjänstdefinitionsfilen innehåller de här elementen beskrivs detaljerat i följande avsnitt i det här avsnittet:

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Inställning](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Slutpunkter](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certifikat](#Certificates)

[Certifikat](#Certificate)

[Import](#Imports)

[Importera](#Import)

[Runtime](#Runtime)

[Miljö](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variabel](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Start](#Startup)

[Aktivitet](#Task)

[Innehållet](#Contents)

[Innehåll](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a> WorkerRole
Den `WorkerRole` element beskriver en roll som används för generaliserad utveckling och kan utföra Bakgrundsbearbetning för en webbroll. En tjänst kan innehålla noll eller flera worker-roller.

I följande tabell beskrivs attributen för den `WorkerRole` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|string|Krävs. Namn för arbetsrollen. Rollens namn måste vara unikt.|
|enableNativeCodeExecution|boolesk|Valfri. Standardvärdet är `true`; native fjärrkörning av kod och fullständigt förtroende är aktiverade som standard. Anger du attributet till `false` att inaktivera interna kodkörning för worker-roll och Använd Azure partiellt förtroende i stället.|
|vmsize|string|Valfri. Ange ett värde att ändra storleken på den virtuella dator som tilldelas till den här rollen. Standardvärdet är `Small`. En lista över möjliga VM-storlekar och deras attribut finns i [storlekar för virtuella datorer för Cloud Services](cloud-services-sizes-specs.md).|

##  <a name="ConfigurationSettings"></a> ConfigurationSettings
Den `ConfigurationSettings` element beskriver samling med konfigurationsinställningar för en arbetsroll. Det här elementet har överordnat den `Setting` element.

##  <a name="Setting"></a> Inställningen
Den `Setting` element beskriver ett namn och värde-par som anger en konfigurationsinställning för en instans av en roll.

I följande tabell beskrivs attributen för den `Setting` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|string|Krävs. Ett unikt namn för konfigurationsinställningen.|

Konfigurationsinställningarna för en roll är namn och värdepar som är deklarerad i tjänstdefinitionsfilen och ange i tjänstekonfigurationsfilen.

##  <a name="LocalResources"></a> LocalResources
Den `LocalResources` element beskriver insamling av lokala lagringsresurser för en arbetsroll. Det här elementet har överordnat den `LocalStorage` element.

##  <a name="LocalStorage"></a> LocalStorage
Den `LocalStorage` element som identifierar en resurs för lokal lagring som innehåller filsystemsutrymme för tjänsten när den körs. En roll kan definiera noll eller flera lokala lagringsresurser.

> [!NOTE]
>  Den `LocalStorage` element kan visas som en underordnad den `WorkerRole` elementet som stöder kompatibilitet med tidigare versioner av Azure SDK.

I följande tabell beskrivs attributen för den `LocalStorage` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|string|Krävs. Ett unikt namn för det lokala arkivet.|
|cleanOnRoleRecycle|boolesk|Valfri. Anger om det lokala arkivet ska rensas när rollen startas om. Standardvärdet är `true`.|
|sizeInMb|int|Valfri. Önskade mängden lagringsutrymme för att allokera för det lokala arkivet, i MB. Om inte anges är tilldelat lagringsutrymme standard 100 MB. Den minsta mängden lagringsutrymme som kan allokeras är 1 MB.<br /><br /> Den maximala storleken för de lokala resurserna beror på storleken på virtuella datorn. Mer information finns i [storlekar för virtuella datorer för Cloud Services](cloud-services-sizes-specs.md).|

Namnet på den katalog som tilldelats till resursen för lokal lagring motsvarar värdet som angetts för attributet name.

##  <a name="Endpoints"></a> Slutpunkter
Den `Endpoints` element beskriver insamling av indata (extern), intern och instans inkommande slutpunkter för en roll. Det här elementet har överordnat den `InputEndpoint`, `InternalEndpoint`, och `InstanceInputEndpoint` element.

Indata och interna slutpunkter tilldelas separat. En tjänst kan ha högst 25 indata, intern och instans inkommande slutpunkter som kan allokeras till över 25 roller som tillåts i en tjänst. Till exempel om du har 5 roller kan du allokera 5 inkommande slutpunkter per roll eller du kan allokera 25 inkommande slutpunkter till en enda roll eller så kan du allokera 1 indataslutpunkten varje 25 roller.

> [!NOTE]
>  Varje roll som distribuerats kräver en instans per roll. Standard etablering för en prenumeration är begränsat till 20 kärnor och därför är begränsat till 20 instanser av en roll. Om ditt program kräver fler instanser än som standard etablering Se [fakturering och prenumerationshantering kvot Support](https://azure.microsoft.com/support/options/) mer information om att öka din kvot.

##  <a name="InputEndpoint"></a> InputEndpoint
Den `InputEndpoint` element beskriver en extern slutpunkt till en arbetsroll.

Du kan definiera flera slutpunkter som är en kombination av HTTP, HTTPS, UDP och TCP-slutpunkter. Du kan ange ett portnummer som du väljer för en slutpunkt för indata, men de portnummer som anges för varje roll i tjänsten måste vara unikt. Till exempel om du anger att en roll använder port 80 för HTTP och port 443 för HTTPS, kan du sedan ange att en andra roll använder port 8080 för HTTP och port 8043 för HTTPS.

I följande tabell beskrivs attributen för den `InputEndpoint` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|string|Krävs. Ett unikt namn för den externa slutpunkten.|
|protokoll|string|Krävs. Transportprotokoll för den externa slutpunkten. För en arbetsroll, möjliga värden är `HTTP`, `HTTPS`, `UDP`, eller `TCP`.|
|port|int|Krävs. Porten för den externa slutpunkten. Du kan ange ett portnummer som du väljer, men de portnummer som anges för varje roll i tjänsten måste vara unikt.<br /><br /> Möjliga värden intervallet mellan 1 och med 65535 (Azure SDK version 1.7 eller senare).|
|certifikat|string|Krävs för en HTTPS-slutpunkt. Namnet på ett certifikat som definieras av en `Certificate` element.|
|localPort|int|Valfri. Anger en port som används för interna anslutningar på slutpunkten. Den `localPort` attribut mappar den externa porten på slutpunkten till en intern port för en roll. Detta är användbart i scenarier där en roll måste kommunicera med en intern komponent på en port att skiljer sig från det som är tillgänglig externt.<br /><br /> Om inte anges värdet för `localPort` är samma som den `port` attribut. Ange värdet för `localPort` till ”*” att automatiskt tilldela en oallokerad port som är synliga med runtime API.<br /><br /> Möjliga värden intervallet mellan 1 och med 65535 (Azure SDK version 1.7 eller senare).<br /><br /> Den `localPort` attributet är endast tillgängligt med hjälp av Azure SDK-version 1.3 eller högre.|
|ignoreRoleInstanceStatus|boolesk|Valfri. När värdet för det här attributet anges till `true`, status för en tjänst ignoreras och slutpunkten tas inte bort av belastningsutjämnaren. Värdet `true` användbart för felsökning av upptagen instanser av en tjänst. Standardvärdet är `false`. **Obs!** En slutpunkt kan fortfarande ta emot trafik även om rollen inte är i tillståndet redo.|
|loadBalancerProbe|string|Valfri. Namnet på belastningsutjämnaravsökningen som är associerade med slutpunkten för indata. Mer information finns i [LoadBalancerProbe-Schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="InternalEndpoint"></a> InternalEndpoint
Den `InternalEndpoint` element beskriver en intern slutpunkt till en arbetsroll. En intern slutpunkt är endast tillgängligt för andra rollinstanser som körs i tjänsten; Det är inte tillgängliga för klienter utanför tjänsten. En arbetsroll kan ha upp till fem HTTP, UDP eller TCP interna slutpunkter.

I följande tabell beskrivs attributen för den `InternalEndpoint` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|string|Krävs. Ett unikt namn för den interna slutpunkten.|
|protokoll|string|Krävs. Transportprotokoll för den interna slutpunkten. Möjliga värden är `HTTP`, `TCP`, `UDP`, eller `ANY`.<br /><br /> Värdet `ANY` anger att alla protokoll, alla portar tillåts.|
|port|int|Valfri. Den port som används för interna belastningsutjämnade anslutningar på slutpunkten. En belastningsutjämnade slutpunkten använder två portar. Den port som används för den offentliga IP-adressen och porten som används på privata IP-adress. Vanligtvis är dessa sätts de till samma, men du kan välja att använda olika portar.<br /><br /> Möjliga värden intervallet mellan 1 och med 65535 (Azure SDK version 1.7 eller senare).<br /><br /> Den `Port` attributet är endast tillgängligt med hjälp av Azure SDK-version 1.3 eller högre.|

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint
Den `InstanceInputEndpoint` element beskriver en slutpunkt för instans-indata till en arbetsroll. En instans indataslutpunkt är associerad med en viss roll-instans med hjälp av portvidarebefordran i belastningsutjämnaren. Varje instans indataslutpunkten mappas till en specifik port från flera olika möjliga portar. Det här elementet har överordnat den `AllocatePublicPortFrom` element.

Den `InstanceInputEndpoint` element är bara tillgängliga med hjälp av Azure SDK-version 1.7 eller senare.

I följande tabell beskrivs attributen för den `InstanceInputEndpoint` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|string|Krävs. Ett unikt namn för slutpunkten.|
|localPort|int|Krävs. Anger den interna porten som alla rollinstanser ska lyssna på för att kunna ta emot inkommande trafik vidarebefordras från belastningsutjämnaren. Möjliga värden intervallet mellan 1 och 65535, inklusive.|
|protokoll|string|Krävs. Transportprotokoll för den interna slutpunkten. Möjliga värden är `udp` eller `tcp`. Använd `tcp` för http/https-baserad trafik.|

##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom
Den `AllocatePublicPortFrom` element beskriver offentliga portintervallet som kan användas av externa kunder att få åtkomst till en slutpunkt för indata på varje instans. Det offentliga portnumret (VIP) är allokerade från det här intervallet och tilldelas varje enskild roll instans slutpunkt under distribution för klienter och uppdatera. Det här elementet har överordnat den `FixedPortRange` element.

Den `AllocatePublicPortFrom` element är bara tillgängliga med hjälp av Azure SDK-version 1.7 eller senare.

##  <a name="FixedPort"></a> FixedPort
Den `FixedPort` elementet anger porten för den interna slutpunkten, vilken kan läsa in belastningsutjämnade anslutningar på slutpunkten.

Den `FixedPort` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller högre.

I följande tabell beskrivs attributen för den `FixedPort` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|port|int|Krävs. Porten för den interna slutpunkten. Detta har det samma effekten som den `FixedPortRange` min och max till samma port.<br /><br /> Möjliga värden intervallet mellan 1 och med 65535 (Azure SDK version 1.7 eller senare).|

##  <a name="FixedPortRange"></a> FixedPortRange
Den `FixedPortRange` elementet anger intervallet för de portar som är tilldelade till den interna slutpunkten eller instans indataslutpunkten och porten som används för den belastningsutjämnade anslutningar på slutpunkten.

> [!NOTE]
>  Den `FixedPortRange` elementet fungerar på olika sätt beroende på vilket element som det finns. När den `FixedPortRange` elementet har statusen på `InternalEndpoint` elementet öppnas alla portar på belastningsutjämnaren inom intervallet min och max attribut för alla virtuella datorer som rollen körs. När den `FixedPortRange` elementet har statusen på `InstanceInputEndpoint` element, öppnas det endast en port inom intervallet med min och max attribut för varje virtuell dator som kör rollen.

Den `FixedPortRange` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller högre.

I följande tabell beskrivs attributen för den `FixedPortRange` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|min.|int|Krävs. Den minsta porten i intervallet. Möjliga värden intervallet mellan 1 och med 65535 (Azure SDK version 1.7 eller senare).|
|max|string|Krävs. Den maximala porten i intervallet. Möjliga värden intervallet mellan 1 och med 65535 (Azure SDK version 1.7 eller senare).|

##  <a name="Certificates"></a> Certifikat
Den `Certificates` element beskriver insamling av certifikat för en arbetsroll. Det här elementet har överordnat den `Certificate` element. En roll kan ha valfritt antal associerade certifikat. Läs mer om hur du använder certifikat elementet [ändra filen tjänstdefinitionen med ett certifikat](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> Certifikat
Den `Certificate` element beskriver ett certifikat som är associerad med en arbetsroll.

I följande tabell beskrivs attributen för den `Certificate` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|string|Krävs. Ett namn för det här certifikatet som används för att se den när den är associerad med en HTTPS `InputEndpoint` element.|
|storeLocation|string|Krävs. Platsen för arkivet där det här certifikatet kan hittas på den lokala datorn. Möjliga värden är `CurrentUser` och `LocalMachine`.|
|storeName|string|Krävs. Namnet på arkivet där det här certifikatet finns på den lokala datorn. Några möjliga värden är de inbyggda arkivnamn `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, eller ett eget Arkiv-namn. Om ett eget Arkiv-namn har angetts, skapas automatiskt i store.|
|permissionLevel|string|Valfri. Anger de åtkomstbehörigheter som ges till processerna som rollen. Om du vill att endast utökade processer för att kunna komma åt den privata nyckeln och sedan ange `elevated` behörighet. `limitedOrElevated` tillåter alla rollen processer att få åtkomst till den privata nyckeln. Möjliga värden är `limitedOrElevated` eller `elevated`. Standardvärdet är `limitedOrElevated`.|

##  <a name="Imports"></a> Import
Den `Imports` element beskriver en uppsättning importera moduler för en arbetsroll som Lägg till komponenter till gästoperativsystemet. Det här elementet har överordnat den `Import` element. Det här elementet är valfritt och en roll kan ha endast en runtime-block.

Den `Imports` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller högre.

##  <a name="Import"></a> Importera
Den `Import` elementet anger en modul för att lägga till i gästoperativsystemet.

Den `Import` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller högre.

I följande tabell beskrivs attributen för den `Import` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|moduleName|string|Krävs. Namnet på modulen att importera. Giltigt importera moduler är:<br /><br /> -RemoteAccess<br />-   RemoteForwarder<br />-Diagnostik<br /><br /> RemoteAccess- och RemoteForwarder-moduler kan du konfigurera din rollinstans för anslutningar till fjärrskrivbord. Mer information finns i [aktivera anslutning till fjärrskrivbord](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Modulen diagnostik kan du samla in diagnostikdata för en rollinstans|

##  <a name="Runtime"></a> Runtime
Den `Runtime` element beskriver en uppsättning miljövariabelinställningar för en arbetsroll som styr körningsmiljö processens Azure-värd. Det här elementet har överordnat den `Environment` element. Det här elementet är valfritt och en roll kan ha endast en runtime-block.

Den `Runtime` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller högre.

I följande tabell beskrivs attributen för den `Runtime` element:

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|executionContext|string|Valfri. Anger kontexten där roll-processen startas. Standardkontexten är `limited`.<br /><br /> -   `limited` – Processen startas utan administratörsbehörighet.<br />-   `elevated` – Processen startas med administratörsbehörighet.|

##  <a name="Environment"></a> Miljö
Den `Environment` element beskriver en uppsättning miljövariabelinställningar för en arbetsroll. Det här elementet har överordnat den `Variable` element. En roll kan ha valfritt antal miljövariabler som anges.

##  <a name="Variable"></a> Variabel
Den `Variable` elementet anger en miljövariabel för att ange i gästoperativsystemprofilen.

Den `Variable` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller högre.

I följande tabell beskrivs attributen för den `Variable` element:

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|string|Krävs. Namnet på miljövariabeln för att ställa in.|
|value|string|Valfri. Värdet som angetts för miljövariabeln. Du måste inkludera värdeattributet eller en `RoleInstanceValue` element.|

##  <a name="RoleInstanceValue"></a> RoleInstanceValue
Den `RoleInstanceValue` elementet anger xPath som du kan hämta värdet för variabeln.

I följande tabell beskrivs attributen för den `RoleInstanceValue` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|XPath|string|Valfri. Sökvägen till distributionsinställningar för instansen. Mer information finns i [Configuration variabler med XPath](cloud-services-role-config-xpath.md).<br /><br /> Du måste inkludera värdeattributet eller en `RoleInstanceValue` element.|

##  <a name="EntryPoint"></a> EntryPoint
Den `EntryPoint` elementet anger startpunkten för en roll. Det här elementet har överordnat den `NetFxEntryPoint` element. De här elementen kan du ange ett annat program än standard WaWorkerHost.exe kan fungera som startpunkt för rollen.

Den `EntryPoint` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint
Den `NetFxEntryPoint` elementet anger program ska köras för en roll.

> [!NOTE]
>  Den `NetFxEntryPoint` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

I följande tabell beskrivs attributen för den `NetFxEntryPoint` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|assemblyName|string|Krävs. Sökvägen och namnet på den sammansättning som innehåller startpunkten. Sökvägen är i förhållande till mappen  **\\%ROLEROOT%\Approot** (ange  **\\%ROLEROOT%\Approot** i `commandLine`, förutsätts). **% ROLEROOT %** är en miljövariabel som underhålls av Azure och rollen representerar roten mappens plats för din roll. Den  **\\%ROLEROOT%\Approot** mappen representerar programmappen för din roll.|
|targetFrameworkVersion|string|Krävs. Version av .NET framework som sammansättningen har skapats. Till exempel `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a> ProgramEntryPoint
Den `ProgramEntryPoint` elementet anger program ska köras för en roll. Den `ProgramEntryPoint` element kan du ange en startpunkt för program som inte är baserad på en .NET-sammansättning.

> [!NOTE]
>  Den `ProgramEntryPoint` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

I följande tabell beskrivs attributen för den `ProgramEntryPoint` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Kommandorad|string|Krävs. Sökvägen, filnamnet och de kommandoradsargument för att köra programmet. Sökvägen är i förhållande till mappen **%ROLEROOT%\Approot** (ange **%ROLEROOT%\Approot** i kommandorad, förutsätts). **% ROLEROOT %** är en miljövariabel som underhålls av Azure och rollen representerar roten mappens plats för din roll. Den **%ROLEROOT%\Approot** mappen representerar programmappen för din roll.<br /><br /> Om programmet har avslutats återanvänds rollen, vanligtvis programmet ska fortsätta att köras i stället för som ett program som bara startar och kör en begränsad aktivitet.|
|setReadyOnProcessStart|boolesk|Krävs. Anger om rollinstansen ska vänta tills kommandoraden ska signalera den startas. Det här värdet måste anges till `true` just nu. Ställer in värdet på `false` är reserverad för framtida användning.|

##  <a name="Startup"></a> Start
Den `Startup` element beskriver en uppsättning aktiviteter som körs när rollen har startats. Det här elementet kan vara överordnad till den `Variable` element. Läs mer om hur du använder startåtgärder rollen [så här konfigurerar du startåtgärder](cloud-services-startup-tasks.md). Det här elementet är valfritt och en roll kan ha endast en start-block.

I följande tabell beskrivs attributet för den `Startup` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|prioritet|int|Endast för internt bruk.|

##  <a name="Task"></a> Uppgift
Den `Task` elementet anger startåtgärd som sker när rollen startar. Startåtgärder kan användas för att utföra uppgifter som förbereder rollen för att köra sådana installation programvarukomponenter eller köra andra program. Uppgifter som utförs i den ordning som de visas inom den `Startup` elementblock.

Den `Task` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller högre.

I följande tabell beskrivs attributen för den `Task` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Kommandorad|string|Krävs. Ett skript, till exempel en CMD-fil som innehåller kommandona som ska köras. Kommandot och batch startfiler måste sparas i ANSI-format. Filformat som en byte-ordningsmarkering markör i början av filen bearbetas inte korrekt.|
|executionContext|string|Anger kontext där skriptet körs.<br /><br /> -   `limited` [Standard] – köras med samma behörigheter som rollen som är värd för processen.<br />-   `elevated` – Kör med administratörsbehörighet.|
|taskType|string|Anger körningsbeteende av kommandot.<br /><br /> -   `simple` [Standard] – systemet väntar tills aktiviteten för att avsluta innan andra aktiviteter startas.<br />-   `background` – System väntar inte aktiviteten för att avsluta.<br />-   `foreground` – Liknar bakgrund, förutom rollen inte startas förrän alla uppgifter i förgrunden avsluta.|

##  <a name="Contents"></a> Innehållet
Den `Contents` element beskriver det innehåll för en arbetsroll. Det här elementet har överordnat den `Content` element.

Den `Contents` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

##  <a name="Content"></a> Innehåll
Den `Content` elementet definierar källplatsen för innehåll som ska kopieras till Azure-dator och den målsökväg som kopieras.

Den `Content` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

I följande tabell beskrivs attributen för den `Content` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|mål|string|Krävs. Plats på virtuella Azure-datorer som innehållet ska placeras. Den här platsen är i förhållande till mappen **%ROLEROOT%\Approot**.|

Det här elementet har det överordnade elementet i den `SourceDirectory` element.

##  <a name="SourceDirectory"></a> SourceDirectory
Den `SourceDirectory` elementet definierar den lokala katalogen där kopieras innehållet. Använd det här elementet för att ange lokala innehållet att kopiera till Azure-dator.

Den `SourceDirectory` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

I följande tabell beskrivs attributen för den `SourceDirectory` element.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|path|string|Krävs. Relativ eller absolut sökväg till en lokal katalog vars innehåll ska kopieras till Azure-dator. Utökningen av miljövariabler i sökvägen till stöds.|

## <a name="see-also"></a>Se även
[Molnet (klassisk) Tjänstdefinitionsschemat](schema-csdef-file.md)