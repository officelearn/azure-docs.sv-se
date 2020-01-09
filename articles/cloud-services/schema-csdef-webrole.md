---
title: Schema för Azure Cloud Services Def. webrole | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: tgore03
ms.author: tagore
ms.openlocfilehash: 1e4413f3495790e5e477f424bb5debcfbc186ade
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385500"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Azure Cloud Services definition webrole-schemat
Azure-webbrollen är en roll som är anpassad för webb programs programmering som stöds av IIS 7, till exempel ASP.NET, PHP, Windows Communication Foundation och FastCGI.

Standard tillägget för tjänst definitions filen är. csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Grundläggande tjänst definitions schema för en webb roll  
Det grundläggande formatet för en tjänst definitions fil som innehåller en webb roll är följande.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
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
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
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
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Schema element  
Tjänst definitions filen innehåller dessa element, som beskrivs i detalj i följande avsnitt i det här avsnittet:  

[Webroll](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Inställning](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Slut punkter](#Endpoints)

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

[Variabel](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Stationer](#Sites)

[Plats](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Bindningar](#Bindings)

[Bindningen](#Binding)

[Nystartade företag](#Startup)

[Aktivitet](#Task)

[Innehåller](#Contents)

[Innehåll](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a>Webroll  
I `WebRole`-elementet beskrivs en roll som är anpassad för webb programs programmering, som stöds av IIS 7 och ASP.NET. En tjänst kan innehålla noll eller flera webb roller.

I följande tabell beskrivs attributen för `WebRole`-elementet.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Namnet på webb rollen. Rollens namn måste vara unikt.|  
|enableNativeCodeExecution|boolean|Valfri. Standardvärdet är `true`; inbyggd kod körning och fullständigt förtroende är aktiverat som standard. Ange det här attributet till `false` för att inaktivera intern kod körning för webb rollen och Använd ett delvis förtroende för Azure i stället.|  
|vmsize|sträng|Valfri. Ange det här värdet om du vill ändra storleken på den virtuella dator som har tilldelats rollen. Standardvärdet är `Small`. Mer information finns i [storlekar för virtuella datorer för Cloud Services](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a>ConfigurationSettings  
`ConfigurationSettings`-elementet beskriver samlingen av konfigurations inställningar för en webbroll. Det här elementet är överordnat `Setting`-elementet.

##  <a name="Setting"></a>Inställningen  
I `Setting`-elementet beskrivs ett namn-och värdepar som anger en konfigurations inställning för en instans av en roll.

I följande tabell beskrivs attributen för `Setting`-elementet.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett unikt namn för konfigurations inställningen.|  

Konfigurations inställningarna för en roll är namn-och värdepar som deklareras i tjänst definitions filen och anges i tjänst konfigurations filen.

##  <a name="LocalResources"></a>LocalResources  
`LocalResources`-elementet beskriver samlingen av lokala lagrings resurser för en webbroll. Det här elementet är överordnat `LocalStorage`-elementet.

##  <a name="LocalStorage"></a>LocalStorage  
`LocalStorage`-elementet identifierar en lokal lagrings resurs som tillhandahåller fil system utrymme för tjänsten vid körning. En roll kan definiera noll eller flera lokala lagrings resurser.

> [!NOTE]
>  `LocalStorage`-elementet kan visas som underordnat det `WebRole`-elementet för att ge stöd för kompatibilitet med tidigare versioner av Azure SDK.

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
`InputEndpoint`-elementet beskriver en extern slut punkt för en webbroll.

Du kan definiera flera slut punkter som är en kombination av HTTP-, HTTPS-, UDP-och TCP-slutpunkter. Du kan ange vilket port nummer du väljer för en ingångs slut punkt, men port numren som anges för varje roll i tjänsten måste vara unika. Om du till exempel anger att en webbroll använder port 80 för HTTP och port 443 för HTTPS, kan du ange att en andra webb roll använder port 8080 för HTTP och port 8043 för HTTPS.

I följande tabell beskrivs attributen för `InputEndpoint`-elementet.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett unikt namn för den externa slut punkten.|  
|protocol|sträng|Krävs. Transport protokollet för den externa slut punkten. För en webb roll är möjliga värden `HTTP`, `HTTPS`, `UDP`eller `TCP`.|  
|port|int|Krävs. Porten för den externa slut punkten. Du kan ange vilket port nummer du vill, men port numren som anges för varje roll i tjänsten måste vara unika.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).|  
|certifikat|sträng|Krävs för en HTTPS-slutpunkt. Namnet på ett certifikat som definieras av ett `Certificate`-element.|  
|localPort|int|Valfri. Anger en port som används för interna anslutningar på slut punkten. Attributet `localPort` mappar den externa porten på slut punkten till en intern port för en roll. Detta är användbart i scenarier där en roll måste kommunicera med en intern komponent på en annan port än den som exponeras externt.<br /><br /> Om inget värde anges är värdet för `localPort` detsamma som attributet `port`. Ange värdet för `localPort` till "*" för att automatiskt tilldela en ej tilldelad port som kan upptäckas med körnings-API: et.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).<br /><br /> Attributet `localPort` är bara tillgängligt med Azure SDK version 1,3 eller senare.|  
|ignoreRoleInstanceStatus|boolean|Valfri. När värdet för det här attributet är inställt på `true`ignoreras statusen för en tjänst och slut punkten tas inte bort av belastningsutjämnaren. Ange det här värdet till `true` användbart för fel sökning av upptagna instanser av en tjänst. Standardvärdet är `false`. **Obs:**  En slut punkt kan fortfarande ta emot trafik även när rollen inte är i ett klart tillstånd.|  
|loadBalancerProbe|sträng|Valfri. Namnet på belastnings Utjämnings avsökningen som är associerad med slut punkten för indatakälla. Mer information finns i [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a>InternalEndpoint  
`InternalEndpoint`-elementet beskriver en intern slut punkt för en webbroll. En intern slut punkt är endast tillgänglig för andra roll instanser som körs i tjänsten. den är inte tillgänglig för klienter utanför tjänsten. Webb roller som inte innehåller `Sites`-elementet får bara ha en intern HTTP-, UDP-eller TCP-slutpunkt.

I följande tabell beskrivs attributen för `InternalEndpoint`-elementet.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett unikt namn för den interna slut punkten.|  
|protocol|sträng|Krävs. Transport protokollet för den interna slut punkten. Möjliga värden är `HTTP`, `TCP`, `UDP`eller `ANY`.<br /><br /> Värdet `ANY` anger att valfritt protokoll, vilken port som helst tillåts.|  
|port|int|Valfri. Den port som används för interna belastningsutjämnade anslutningar på slut punkten. En belastningsutjämnad slut punkt använder två portar. Den port som används för den offentliga IP-adressen och porten som används på den privata IP-adressen. Dessa är vanligt vis inställda på samma, men du kan välja att använda olika portar.<br /><br /> Möjliga värden är mellan 1 och 65535, inklusive (Azure SDK version 1,7 eller högre).<br /><br /> Attributet `Port` är bara tillgängligt med Azure SDK version 1,3 eller senare.|  

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint  
I `InstanceInputEndpoint`-elementet beskrivs en slut punkt för instans ineffekt till en webbroll. En instans av indatamängden är associerad med en speciell roll instans med hjälp av vidarebefordran av port i belastningsutjämnaren. Varje instans av ingångs slut punkt mappas till en speciell port från ett antal möjliga portar. Det här elementet är överordnat `AllocatePublicPortFrom`-elementet.

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
I `Certificates`-elementet beskrivs en samling certifikat för en webbroll. Det här elementet är överordnat `Certificate`-elementet. En roll kan ha valfritt antal associerade certifikat. Mer information om hur du använder certifikat elementet finns i [ändra tjänst definitions filen med ett certifikat](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Certifikatmallens  
`Certificate`-elementet beskriver ett certifikat som är associerat med en webbroll.

I följande tabell beskrivs attributen för `Certificate`-elementet.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett namn för det här certifikatet, som används för att referera till det när det är associerat med ett HTTPS `InputEndpoint`-element.|  
|storeLocation|sträng|Krävs. Platsen för det certifikat arkiv där det här certifikatet finns på den lokala datorn. Möjliga värden är `CurrentUser` och `LocalMachine`.|  
|storeName|sträng|Krävs. Namnet på det certifikat arkiv där det här certifikatet finns på den lokala datorn. Möjliga värden är de inbyggda lagrings namnen `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`eller något anpassat lagrings namn. Om du anger ett namn på en anpassad butik skapas butiken automatiskt.|  
|permissionLevel|sträng|Valfri. Anger de åtkomst behörigheter som tilldelas roll processerna. Om du bara vill att utökade processer ska kunna komma åt den privata nyckeln anger du `elevated`-behörighet. `limitedOrElevated` behörighet tillåter alla roll processer att komma åt den privata nyckeln. Möjliga värden är `limitedOrElevated` eller `elevated`. Standardvärdet är `limitedOrElevated`.|  

##  <a name="Imports"></a>Kina  
I `Imports`-elementet beskrivs en samling import-moduler för en webbroll som lägger till komponenter i gäst operativ systemet. Det här elementet är överordnat `Import`-elementet. Det här elementet är valfritt och en roll kan bara ha ett import block. 

`Imports`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

##  <a name="Import"></a>Export  
`Import`-elementet anger en modul som ska läggas till i gäst operativ systemet.

`Import`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `Import`-elementet.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|moduleName|sträng|Krävs. Namnet på den modul som ska importeras. Giltiga import moduler är:<br /><br /> -RemoteAccess<br />- RemoteForwarder<br />– Diagnostik<br /><br /> Med RemoteAccess-och RemoteForwarder-modulerna kan du konfigurera roll instansen för fjärr skrivbords anslutningar. Mer information finns i [aktivera anslutning till fjärrskrivbord](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Med modulen diagnostik kan du samla in diagnostikdata för en roll instans.|  

##  <a name="Runtime"></a>Flöde  
I `Runtime`-elementet beskrivs en samling miljö variabel inställningar för en webbroll som styr körnings miljön för Azure-värd processen. Det här elementet är överordnat `Environment`-elementet. Det här elementet är valfritt och en roll kan bara ha ett körnings block.

`Runtime`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `Runtime`-elementet:  

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|executionContext|sträng|Valfri. Anger kontexten där roll processen startas. Standard kontexten är `limited`.<br /><br /> -   `limited` – processen startas utan administratörs behörighet.<br />-   `elevated` – processen startas med administratörs behörighet.|  

##  <a name="Environment"></a>Miljö  
I `Environment`-elementet beskrivs en samling miljö variabel inställningar för en webbroll. Det här elementet är överordnat `Variable`-elementet. En roll kan ha valfritt antal miljövariabler inställda.

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
|AssemblyName|sträng|Krävs. Sökväg och fil namn för sammansättningen som innehåller start punkten. Sökvägen är relativ till mappen **\\%ROLEROOT%\Approot** (ange inte **\\%ROLEROOT%\Approot** i `commandLine`, den antas). **% ROLEROOT%** är en miljö variabel som underhålls av Azure och den representerar rot katalogen för din roll. Mappen **\\%ROLEROOT%\Approot** representerar programmappen för din roll.<br /><br /> För INSTANSEN-roller är sökvägen alltid relativ i förhållande till **\\%ROLEROOT%\Approot\bin** -mappen.<br /><br /> För fullständiga IIS-och IIS Express-webbroller, om det inte går att hitta sammansättningen i förhållande till **\\%ROLEROOT%\Approot** -mappen, genomsöks **\\%ROLEROOT%\Approot\bin** .<br /><br /> Detta beteende för fullständig IIS är inte en rekommenderad metod och kan komma att tas bort i framtida versioner.|  
|targetFrameworkVersion|sträng|Krävs. Den version av .NET Framework som sammansättningen skapats på. Till exempel `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a>Stationer  
I `Sites`-elementet beskrivs en samling webbplatser och webb program som finns i en webbroll. Det här elementet är överordnat `Site`-elementet. Om du inte anger ett `Sites`-element, är din webb roll värd som en äldre webb roll och du kan bara ha en webbplats som finns i din webb roll. Det här elementet är valfritt och en roll kan bara ha ett blockerade platser.

`Sites`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

##  <a name="Site"></a>Plats  
`Site`-elementet anger en webbplats eller ett webb program som är en del av webb rollen.

`Site`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `Site`-elementet.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Namnet på webbplatsen eller programmet.|  
|physicalDirectory|sträng|Platsen för innehålls katalogen för plats roten. Platsen kan anges som en absolut sökväg eller i förhållande till. csdef-platsen.|  

##  <a name="VirtualApplication"></a>VirtualApplication  
`VirtualApplication`-elementet definierar ett program i Internet Information Services (IIS) 7 är en gruppering av filer som levererar innehåll eller tillhandahåller tjänster över protokoll, till exempel HTTP. När du skapar ett program i IIS 7 blir programmets sökväg en del av webbplatsens URL.

`VirtualApplication`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `VirtualApplication`-elementet.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Anger ett namn för att identifiera det virtuella programmet.|  
|physicalDirectory|sträng|Krävs. Anger sökvägen till utvecklings datorn som innehåller det virtuella programmet. I Compute-emulatorn har IIS kon figurer ATS för att hämta innehåll från den här platsen. När du distribuerar till Azure paketeras innehållet i den fysiska katalogen tillsammans med resten av tjänsten. När tjänst paketet distribueras till Azure konfigureras IIS med platsen för det uppackade innehållet.|  

##  <a name="VirtualDirectory"></a>VirtualDirectory  
`VirtualDirectory`-elementet anger ett katalog namn (även kallat sökväg) som du anger i IIS och mappar till en fysisk katalog på en lokal server eller fjärrserver.

`VirtualDirectory`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

I följande tabell beskrivs attributen för `VirtualDirectory`-elementet.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Anger ett namn för att identifiera den virtuella katalogen.|  
|värde|physicalDirectory|Krävs. Anger sökvägen till utvecklings datorn som innehåller webbplatsen eller innehållet i den virtuella katalogen. I Compute-emulatorn har IIS kon figurer ATS för att hämta innehåll från den här platsen. När du distribuerar till Azure paketeras innehållet i den fysiska katalogen tillsammans med resten av tjänsten. När tjänst paketet distribueras till Azure konfigureras IIS med platsen för det uppackade innehållet.|  

##  <a name="Bindings"></a>Bindningar  
`Bindings`-elementet beskriver en samling bindningar för en webbplats. Det är det överordnade elementet i `Binding`-elementet. Elementet krävs för varje `Site`-element. Mer information om hur du konfigurerar slut punkter finns i [Aktivera kommunikation för roll instanser](cloud-services-enable-communication-role-instances.md).

`Bindings`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

##  <a name="Binding"></a>Enheten  
`Binding`-elementet anger den konfigurations information som krävs för att begär Anden ska kunna kommunicera med en webbplats eller ett webb program.

`Binding`-elementet är bara tillgängligt med Azure SDK version 1,3 eller senare.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Anger ett namn för att identifiera bindningen.|  
|endpointName|sträng|Krävs. Anger slut punkts namnet som ska bindas till.|  
|Huvud|sträng|Valfri. Anger ett värdnamn som låter dig vara värd för flera platser, med olika värdnamn, på en enskild IP-adress/port nummer kombination.|  

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
I `Contents`-elementet beskrivs innehålls samlingen för en webbroll. Det här elementet är överordnat `Content`-elementet.

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




