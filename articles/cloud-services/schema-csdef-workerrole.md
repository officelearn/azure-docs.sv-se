---
title: Azure Cloud Services Def. WorkerRole-schema | Microsoft Docs
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
author: tgore03
ms.author: tagore
ms.openlocfilehash: 518fa0b64277d056796669a3c9f93c9c22325d91
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449025"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>WorkerRole-schema för Azure Cloud Services-definition
Azure Worker-rollen är en roll som är användbar för generaliserad utveckling och som kan utföra bakgrunds bearbetning för en webbroll.

Standard tillägget för tjänst definitions filen är. csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Grundläggande tjänst definitions schema för en arbets roll.
Det grundläggande formatet för tjänst definitions filen som innehåller en arbets roll är följande.

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
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
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

## <a name="schema-elements"></a>Schema element
Tjänst definitions filen innehåller dessa element, som beskrivs i detalj i följande avsnitt i det här avsnittet:

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Inställning](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Slut punkter](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certifikat](#Certificates)

[Certifikat](#Certificate)

[Kina](#Imports)

[Importera](#Import)

[Flöde](#Runtime)

[Miljö](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variabel](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Nystartade företag](#Startup)

[Aktivitet](#Task)

[Innehåller](#Contents)

[Innehåll](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a>WorkerRole
I `WorkerRole`-elementet beskrivs en roll som är användbar för generaliserad utveckling och som kan utföra bakgrunds bearbetning för en webb roll. En tjänst kan innehålla noll eller flera arbets roller.

I följande tabell beskrivs attributen för `WorkerRole`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Namnet på arbets rollen. Rollens namn måste vara unikt.|
|enableNativeCodeExecution|boolean|Valfri. Standardvärdet är `true`; inbyggd kod körning och fullständigt förtroende är aktiverat som standard. Ange det här attributet till `false` för att inaktivera intern kod körning för arbets rollen och Använd delvis förtroende för Azure i stället.|
|vmsize|sträng|Valfri. Ange det här värdet om du vill ändra storleken på den virtuella dator som har tilldelats den här rollen. Standardvärdet är `Small`. En lista över möjliga storlekar för virtuella datorer och deras attribut finns i [storlekar för virtuella datorer för Cloud Services](cloud-services-sizes-specs.md).|

##  <a name="ConfigurationSettings"></a>ConfigurationSettings
`ConfigurationSettings`-elementet beskriver samlingen av konfigurations inställningar för en arbets roll. Det här elementet är överordnat `Setting`-elementet.

##  <a name="Setting"></a>Inställningen
I `Setting`-elementet beskrivs ett namn-och värdepar som anger en konfigurations inställning för en instans av en roll.

I följande tabell beskrivs attributen för `Setting`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för konfigurations inställningen.|

Konfigurations inställningarna för en roll är namn-och värdepar som deklareras i tjänst definitions filen och anges i tjänst konfigurations filen.

##  <a name="LocalResources"></a>LocalResources
`LocalResources`-elementet beskriver samlingen av lokala lagrings resurser för en arbets roll. Det här elementet är överordnat `LocalStorage`-elementet.

##  <a name="LocalStorage"></a>LocalStorage
`LocalStorage`-elementet identifierar en lokal lagrings resurs som tillhandahåller fil system utrymme för tjänsten vid körning. En roll kan definiera noll eller flera lokala lagrings resurser.

> [!NOTE]
>  `LocalStorage`-elementet kan visas som underordnat det `WorkerRole`-elementet för att ge stöd för kompatibilitet med tidigare versioner av Azure SDK.

I följande tabell beskrivs attributen för `LocalStorage`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för det lokala arkivet.|
|cleanOnRoleRecycle|boolean|Valfri. Anger om det lokala arkivet ska rensas när rollen startas om. Standardvärdet är `true`.|
|sizeInMb|int|Valfri. Den önskade mängden lagrings utrymme som ska allokeras för det lokala arkivet, i MB. Om inget värde anges är det allokerade standard lagrings utrymmet 100 MB. Den minsta mängd lagrings utrymme som kan allokeras är 1 MB.<br /><br /> Den maximala storleken på de lokala resurserna är beroende av storleken på den virtuella datorn. Mer information finns i [storlekar för virtuella datorer för Cloud Services](cloud-services-sizes-specs.md).|

Namnet på den katalog som allokeras till den lokala lagrings resursen motsvarar det värde som anges för attributet name.

##  <a name="Endpoints"></a>Slut punkter
`Endpoints`-elementet beskriver insamlingen av indatamängds-, intern och instans slut punkter för en roll. Det här elementet är överordnat `InputEndpoint`, `InternalEndpoint`och `InstanceInputEndpoint` element.

Inmatade och interna slut punkter tilldelas separat. En tjänst kan ha totalt 25 slut punkter för indatatyper, interna och instans ingångar som kan allokeras mellan de 25 roller som tillåts i en tjänst. Om du till exempel har 5 roller kan du allokera 5 ingångs slut punkter per roll, eller så kan du allokera 25 ingångs slut punkter till en enda roll, eller så kan du allokera 1-ingångs slut punkt till 25 roller.

> [!NOTE]
>  Varje roll som distribueras kräver en instans per roll. Standard etableringen för en prenumeration är begränsad till 20 kärnor och är därför begränsad till 20 instanser av en roll. Om ditt program kräver fler instanser än vad som tillhandahålls av standard etableringen [, se fakturering, prenumerations hantering och kvot stöd](https://azure.microsoft.com/support/options/) för mer information om hur du ökar din kvot.

##  <a name="InputEndpoint"></a>InputEndpoint
`InputEndpoint`-elementet beskriver en extern slut punkt för en arbets roll.

Du kan definiera flera slut punkter som är en kombination av HTTP-, HTTPS-, UDP-och TCP-slutpunkter. Du kan ange vilket port nummer du väljer för en ingångs slut punkt, men port numren som anges för varje roll i tjänsten måste vara unika. Om du till exempel anger att en roll använder port 80 för HTTP och port 443 för HTTPS, kan du ange att en andra roll använder port 8080 för HTTP och port 8043 för HTTPS.

I följande tabell beskrivs attributen för `InputEndpoint`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för den externa slut punkten.|
|protocol|sträng|Krävs. Transport protokollet för den externa slut punkten. För en arbets roll är möjliga värden `HTTP`, `HTTPS`, `UDP`eller `TCP`.|
|port|int|Krävs. Porten för den externa slut punkten. Du kan ange vilket port nummer du vill, men port numren som anges för varje roll i tjänsten måste vara unika.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).|
|certifikat|sträng|Krävs för en HTTPS-slutpunkt. Namnet på ett certifikat som definieras av ett `Certificate`-element.|
|localPort|int|Valfri. Anger en port som används för interna anslutningar på slut punkten. Attributet `localPort` mappar den externa porten på slut punkten till en intern port för en roll. Detta är användbart i scenarier där en roll måste kommunicera med en intern komponent på en annan port än den som exponeras externt.<br /><br /> Om inget värde anges är värdet för `localPort` detsamma som attributet `port`. Ange värdet för `localPort` till "*" för att automatiskt tilldela en ej tilldelad port som kan upptäckas med körnings-API: et.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).<br /><br /> Attributet `localPort` är bara tillgängligt med Azure SDK version 1,3 eller senare.|
|ignoreRoleInstanceStatus|boolean|Valfri. När värdet för det här attributet är inställt på `true`ignoreras statusen för en tjänst och slut punkten tas inte bort av belastningsutjämnaren. Ange det här värdet till `true` användbart för fel sökning av upptagna instanser av en tjänst. Standardvärdet är `false`. **Obs:** En slut punkt kan fortfarande ta emot trafik även när rollen inte är i ett klart tillstånd.|
|loadBalancerProbe|sträng|Valfri. Namnet på belastnings Utjämnings avsökningen som är associerad med slut punkten för indatakälla. Mer information finns i [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="InternalEndpoint"></a>InternalEndpoint
`InternalEndpoint`-elementet beskriver en intern slut punkt för en arbets roll. En intern slut punkt är endast tillgänglig för andra roll instanser som körs i tjänsten. den är inte tillgänglig för klienter utanför tjänsten. En arbets roll kan ha upp till fem interna HTTP-, UDP-eller TCP-slutpunkter.

I följande tabell beskrivs attributen för `InternalEndpoint`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för den interna slut punkten.|
|protocol|sträng|Krävs. Transport protokollet för den interna slut punkten. Möjliga värden är `HTTP`, `TCP`, `UDP`eller `ANY`.<br /><br /> Värdet `ANY` anger att valfritt protokoll, vilken port som helst tillåts.|
|port|int|Valfri. Den port som används för interna belastningsutjämnade anslutningar på slut punkten. En belastningsutjämnad slut punkt använder två portar. Den port som används för den offentliga IP-adressen och porten som används på den privata IP-adressen. Dessa är vanligt vis inställda på samma, men du kan välja att använda olika portar.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).<br /><br /> Attributet `Port` är bara tillgängligt med Azure SDK version 1,3 eller senare.|

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint
I `InstanceInputEndpoint`-elementet beskrivs en slut punkt för instans ineffekt till en arbets roll. En instans av indatamängden är associerad med en speciell roll instans med hjälp av vidarebefordran av port i belastningsutjämnaren. Varje instans av ingångs slut punkt mappas till en speciell port från ett antal möjliga portar. Det här elementet är överordnat `AllocatePublicPortFrom`-elementet.

`InstanceInputEndpoint`-elementet är bara tillgängligt med Azure SDK version 1,7 eller senare.

I följande tabell beskrivs attributen för `InstanceInputEndpoint`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för slut punkten.|
|localPort|int|Krävs. Anger den interna port som alla roll instanser kommer att lyssna på för att ta emot inkommande trafik som vidarebefordras från belastningsutjämnaren. Möjliga värden ligger mellan 1 och 65535.|
|protocol|sträng|Krävs. Transport protokollet för den interna slut punkten. Möjliga värden är `udp` eller `tcp`. Använd `tcp` för http/https-baserad trafik.|

##  <a name="AllocatePublicPortFrom"></a>AllocatePublicPortFrom
I `AllocatePublicPortFrom`-elementet beskrivs det offentliga port intervall som kan användas av externa kunder för att få åtkomst till varje instans ingångs slut punkt. Det offentliga (VIP) port numret tilldelas från det här intervallet och tilldelas till varje enskild roll instans slut punkt under klient distribution och uppdatering. Det här elementet är överordnat `FixedPortRange`-elementet.

`AllocatePublicPortFrom`-elementet är bara tillgängligt med Azure SDK version 1,7 eller senare.

##  <a name="FixedPort"></a>FixedPort
`FixedPort`-elementet anger porten för den interna slut punkten som aktiverar belastningsutjämnade anslutningar på slut punkten.

`FixedPort`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `FixedPort`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|port|int|Krävs. Porten för den interna slut punkten. Detta har samma resultat som när du ställer in `FixedPortRange` min och Max på samma port.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).|

##  <a name="FixedPortRange"></a>FixedPortRange
`FixedPortRange`-elementet anger det port intervall som är kopplat till den interna slut punkten eller instansens ingångs slut punkt och anger den port som används för belastningsutjämnade anslutningar på slut punkten.

> [!NOTE]
>  `FixedPortRange`-elementet fungerar på olika sätt beroende på vilket element det finns i. När `FixedPortRange`-elementet finns i `InternalEndpoint`-elementet, öppnar det alla portar i belastningsutjämnaren inom intervallet för de minsta och högsta attributen för alla virtuella datorer som rollen körs på. När `FixedPortRange`-elementet finns i `InstanceInputEndpoint`-elementet, öppnar det bara en port inom intervallet för de minsta och högsta attributen på varje virtuell dator som kör rollen.

`FixedPortRange`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `FixedPortRange`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|min.|int|Krävs. Den minsta porten i intervallet. Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).|
|max|sträng|Krävs. Den maximala porten i intervallet. Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).|

##  <a name="Certificates"></a>Intyg
I `Certificates`-elementet beskrivs en samling certifikat för en arbets roll. Det här elementet är överordnat `Certificate`-elementet. En roll kan ha valfritt antal associerade certifikat. Mer information om hur du använder certifikat elementet finns i [ändra tjänst definitions filen med ett certifikat](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Certifikatmallens
`Certificate`-elementet beskriver ett certifikat som är associerat med en arbets roll.

I följande tabell beskrivs attributen för `Certificate`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett namn för det här certifikatet, som används för att referera till det när det är associerat med ett HTTPS `InputEndpoint`-element.|
|storeLocation|sträng|Krävs. Platsen för det certifikat arkiv där det här certifikatet finns på den lokala datorn. Möjliga värden är `CurrentUser` och `LocalMachine`.|
|storeName|sträng|Krävs. Namnet på det certifikat arkiv där det här certifikatet finns på den lokala datorn. Möjliga värden är de inbyggda lagrings namnen `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`eller något anpassat lagrings namn. Om du anger ett namn på en anpassad butik skapas butiken automatiskt.|
|permissionLevel|sträng|Valfri. Anger de åtkomst behörigheter som tilldelas roll processerna. Om du bara vill att utökade processer ska kunna komma åt den privata nyckeln anger du `elevated`-behörighet. `limitedOrElevated` behörighet tillåter alla roll processer att komma åt den privata nyckeln. Möjliga värden är `limitedOrElevated` eller `elevated`. Standardvärdet är `limitedOrElevated`.|

##  <a name="Imports"></a>Kina
I `Imports`-elementet beskrivs en samling import-moduler för en arbets roll som lägger till komponenter i gäst operativ systemet. Det här elementet är överordnat `Import`-elementet. Det här elementet är valfritt och en roll kan bara ha ett körnings block.

`Imports`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

##  <a name="Import"></a>Export
`Import`-elementet anger en modul som ska läggas till i gäst operativ systemet.

`Import`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `Import`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|moduleName|sträng|Krävs. Namnet på den modul som ska importeras. Giltiga import moduler är:<br /><br /> -RemoteAccess<br />- RemoteForwarder<br />– Diagnostik<br /><br /> Med RemoteAccess-och RemoteForwarder-modulerna kan du konfigurera roll instansen för fjärr skrivbords anslutningar. Mer information finns i [aktivera anslutning till fjärrskrivbord](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Med modulen diagnostik kan du samla in diagnostikdata för en roll instans|

##  <a name="Runtime"></a>Flöde
I `Runtime`-elementet beskrivs en samling miljö variabel inställningar för en arbets roll som styr körnings miljön för Azure-värd processen. Det här elementet är överordnat `Environment`-elementet. Det här elementet är valfritt och en roll kan bara ha ett körnings block.

`Runtime`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `Runtime`-elementet:

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|executionContext|sträng|Valfri. Anger kontexten där roll processen startas. Standard kontexten är `limited`.<br /><br /> -   `limited` – processen startas utan administratörs behörighet.<br />-   `elevated` – processen startas med administratörs behörighet.|

##  <a name="Environment"></a>Miljö
I `Environment`-elementet beskrivs en samling miljö variabel inställningar för en arbets roll. Det här elementet är överordnat `Variable`-elementet. En roll kan ha valfritt antal miljövariabler inställda.

##  <a name="Variable"></a>Variabel
`Variable`-elementet anger en miljö variabel som ska anges i gäst operativ systemet.

`Variable`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `Variable`-elementet:

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Namnet på den miljö variabel som ska anges.|
|värde|sträng|Valfri. Värdet som ska anges för miljö variabeln. Du måste inkludera antingen ett Value-attribut eller ett `RoleInstanceValue`-element.|

##  <a name="RoleInstanceValue"></a>RoleInstanceValue
`RoleInstanceValue`-elementet anger den xPath som variabelns värde ska hämtas från.

I följande tabell beskrivs attributen för `RoleInstanceValue`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|XPath|sträng|Valfri. Sökväg för distributions inställningar för instansen. Mer information finns i [konfigurations variabler med XPath](cloud-services-role-config-xpath.md).<br /><br /> Du måste inkludera antingen ett Value-attribut eller ett `RoleInstanceValue`-element.|

##  <a name="EntryPoint"></a>EntryPoint
`EntryPoint`-elementet anger en Rolls start punkt. Det här elementet är överordnat `NetFxEntryPoint` elementen. Med dessa element kan du ange ett annat program än standard-WaWorkerHost. exe som ska fungera som roll start punkt.

`EntryPoint`-elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint
`NetFxEntryPoint`-elementet anger vilket program som ska köras för en roll.

> [!NOTE]
>  `NetFxEntryPoint`-elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

I följande tabell beskrivs attributen för `NetFxEntryPoint`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|AssemblyName|sträng|Krävs. Sökväg och fil namn för sammansättningen som innehåller start punkten. Sökvägen är relativ till mappen **\\%ROLEROOT%\Approot** (ange inte **\\%ROLEROOT%\Approot** i `commandLine`, den antas). **% ROLEROOT%** är en miljö variabel som underhålls av Azure och den representerar rot katalogen för din roll. Mappen **\\%ROLEROOT%\Approot** representerar programmappen för din roll.|
|targetFrameworkVersion|sträng|Krävs. Den version av .NET Framework som sammansättningen skapats på. Till exempel `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a>ProgramEntryPoint
`ProgramEntryPoint`-elementet anger vilket program som ska köras för en roll. Med hjälp av `ProgramEntryPoint`-elementet kan du ange en start punkt för programmet som inte baseras på en .NET-sammansättning.

> [!NOTE]
>  `ProgramEntryPoint`-elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

I följande tabell beskrivs attributen för `ProgramEntryPoint`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Raden|sträng|Krävs. Sökvägen, fil namnet och alla kommando rads argument för programmet som ska köras. Sökvägen är relativ till mappen **%ROLEROOT%\Approot** (ange inte **%ROLEROOT%\Approot** i kommando raden, men den antas). **% ROLEROOT%** är en miljö variabel som underhålls av Azure och den representerar rot katalogen för din roll. Mappen **%ROLEROOT%\Approot** representerar programmappen för din roll.<br /><br /> Om programmet avslutas återvinns rollen, så normalt anger du att programmet ska fortsätta att köras, i stället för ett program som bara startar och kör en begränsad uppgift.|
|setReadyOnProcessStart|boolean|Krävs. Anger om roll instansen ska vänta på kommando rads programmet för att signalera att den har startats. Värdet måste anges till `true` för tillfället. Att ställa in värdet på `false` är reserverat för framtida användning.|

##  <a name="Startup"></a>Startade
I `Startup`-elementet beskrivs en samling uppgifter som körs när rollen startas. Det här elementet kan vara överordnat `Variable`-elementet. Mer information om hur du använder roll start åtgärder finns i [så här konfigurerar du Start åtgärder](cloud-services-startup-tasks.md). Det här elementet är valfritt och en roll kan bara ha ett start block.

I följande tabell beskrivs attributet för `Startup`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|prioritet|int|Endast för internt bruk.|

##  <a name="Task"></a>Uppgift
`Task`-elementet anger start aktivitet som äger rum när rollen startas. Start uppgifter kan användas för att utföra uppgifter som förbereder rollen för att köra sådana installations program komponenter eller köra andra program. Aktiviteter körs i den ordning som de visas i det `Startup` element blocket.

`Task`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `Task`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Raden|sträng|Krävs. Ett skript, till exempel en CMD-fil, som innehåller de kommandon som ska köras. Start kommando och kommandofiler måste sparas i ANSI-format. Fil format som anger en byte-ordning markör vid början av filen kommer inte att bearbetas korrekt.|
|executionContext|sträng|Anger kontexten som skriptet körs i.<br /><br /> -   `limited` [standard] – kör med samma behörigheter som den roll som är värd för processen.<br />-   `elevated` – kör med administratörs behörighet.|
|taskType|sträng|Anger kommandots körnings beteende.<br /><br /> -   `simple` [standard] – systemet väntar på att aktiviteten avslutas innan andra aktiviteter startas.<br />-   `background` – systemet väntar inte på att uppgiften ska avslutas.<br />-   `foreground` – liknar bakgrunden, förutom att rollen inte startas om förrän alla förgrunds aktiviteter avslutas.|

##  <a name="Contents"></a>Innehåller
I `Contents`-elementet beskrivs innehålls samlingen för en arbets roll. Det här elementet är överordnat `Content`-elementet.

`Contents`-elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

##  <a name="Content"></a>Innehåll
`Content`-elementet definierar käll platsen för det innehåll som ska kopieras till den virtuella Azure-datorn och mål Sök vägen som den kopieras till.

`Content`-elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

I följande tabell beskrivs attributen för `Content`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|destination|sträng|Krävs. Plats på den virtuella Azure-dator som innehållet placeras på. Den här platsen är relativ till mappen **%ROLEROOT%\Approot**.|

Det här elementet är det överordnade elementet i `SourceDirectory`-elementet.

##  <a name="SourceDirectory"></a>SourceDirectory
`SourceDirectory`-elementet definierar den lokala katalogen som innehållet kopieras från. Använd det här elementet för att ange det lokala innehåll som ska kopieras till den virtuella Azure-datorn.

`SourceDirectory`-elementet är bara tillgängligt med Azure SDK version 1,5 eller senare.

I följande tabell beskrivs attributen för `SourceDirectory`-elementet.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Sökväg|sträng|Krävs. Relativ eller absolut sökväg till en lokal katalog vars innehåll ska kopieras till den virtuella Azure-datorn. Det finns stöd för att utöka miljövariabler i katalog Sök vägen.|

## <a name="see-also"></a>Se även
[Definitions schema för moln tjänst (klassisk)](schema-csdef-file.md)



