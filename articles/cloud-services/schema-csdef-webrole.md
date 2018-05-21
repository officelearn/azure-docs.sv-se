---
title: Azure Cloud Services Def. WebRole schemat | Microsoft Docs
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 6db3edef937dc0b5cdd805d0045897e02b985e7b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Azure Cloud Services Definition WebRole Schema
Azure-webbroll är en roll som är anpassade för web application programming som stöds av IIS 7, till exempel ASP.NET, PHP, Windows Communication Foundation och FastCGI.

Standardfilnamnstillägget för tjänstdefinitionsfilen är .csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Grundläggande service definition schemat för en webbroll  
Det grundläggande formatet för en tjänstdefinitionsfilen som innehåller en webbroll är som följer.

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
         <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
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

## <a name="schema-elements"></a>Schemaelement  
Tjänstdefinitionsfilen innehåller de här elementen beskrivs detaljerat i följande avsnitt i det här avsnittet:  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Inställning](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Slutpunkter](#Endpoints)

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

[variabeln](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[webbplatser](#Sites)

[plats](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Bindningar](#Bindings)

[Bindning](#Binding)

[Start](#Startup)

[Aktivitet](#Task)

[Innehållet](#Contents)

[Innehåll](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> WebRole  
Den `WebRole` element beskriver en roll som är anpassad för web application programming som stöds av IIS 7- och ASP.NET. En tjänst kan innehålla noll eller flera web-roller.

I följande tabell beskrivs attributen för den `WebRole` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Namnet för webbrollen. Det rollnamn måste vara unika.|  
|enableNativeCodeExecution|boolesk|Valfri. Standardvärdet är `true`; intern körning och fullständigt förtroende är aktiverade som standard. Ange det här attributet `false` inaktivera körning av intern kod för webbrollen och i stället använda Azure partiellt förtroende.|  
|vmsize|sträng|Valfri. Ange ett värde att ändra storleken på den virtuella datorn tilldelas till rollen. Standardvärdet är `Small`. Mer information finns i [storlekar för virtuella datorer för molntjänster](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a> ConfigurationSettings  
Den `ConfigurationSettings` element beskriver samling med konfigurationsinställningar för en webbroll. Det här elementet är överordnad den `Setting` element.

##  <a name="Setting"></a> Inställningen  
Den `Setting` element beskriver ett namn och värde-par som anger en konfigurationsinställning för en instans av en roll.

I följande tabell beskrivs attributen för den `Setting` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett unikt namn för konfigurationsinställningen.|  

Konfigurationsinställningarna för en roll är namn och värdepar som deklarerats i tjänstdefinitionsfilen och anges i tjänstekonfigurationsfilen.

##  <a name="LocalResources"></a> LocalResources  
Den `LocalResources` element beskriver insamling av lokala lagringsresurser för en webbroll. Det här elementet är överordnad den `LocalStorage` element.

##  <a name="LocalStorage"></a> LocalStorage  
Den `LocalStorage` element som identifierar en resurs för lokal lagring som innehåller filsystemsutrymme för tjänsten vid körning. En roll kan definiera noll eller flera lokala lagringsresurser.

> [!NOTE]
>  Den `LocalStorage` elementet kan finnas som underordnad till den `WebRole` elementet som stöder kompatibilitet med tidigare versioner av Azure SDK.

I följande tabell beskrivs attributen för den `LocalStorage` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett unikt namn för det lokala arkivet.|  
|cleanOnRoleRecycle|boolesk|Valfri. Anger om det lokala arkivet ska rensas när rollen har startats om. Standardvärdet är `true`.|  
|sizeInMb|int|Valfri. Önskad mängd lagringsutrymme för att allokera för det lokala arkivet i MB. Om inget anges är standardvärdet lagringsutrymme som allokerats 100 MB. Den minsta mängden lagringsutrymme som kan allokeras är 1 MB.<br /><br /> Den maximala storleken på lokala resurser är beroende av storleken på virtuella datorn. Mer information finns i [storlekar för virtuella datorer för molntjänster](cloud-services-sizes-specs.md).|  
  
Namnet på den katalog som tilldelats till resursen för lokal lagring motsvarar värdet som angetts för attributet name.

##  <a name="Endpoints"></a> Slutpunkter  
Den `Endpoints` element beskriver insamling av indata (externa) internt och instans inkommande slutpunkter för en roll. Det här elementet är överordnad den `InputEndpoint`, `InternalEndpoint`, och `InstanceInputEndpoint` element.

Indata- och interna slutpunkter som tilldelas separat. En tjänst kan ha högst 25 indata, internt, och instansen inkommande slutpunkter som kan fördelas mellan 25 roller som tillåts i en tjänst. Till exempel om ha 5 roller kan du allokera 5 inkommande slutpunkter per roll eller du kan allokera 25 inkommande slutpunkter till en roll eller du kan allokera 1 slutpunkten för indata varje 25 roller.

> [!NOTE]
>  Varje roll distribueras kräver en instans per roll. Standard-etablering för en prenumeration är begränsad till 20 kärnor och därför är begränsad till 20 instanser av en roll. Om ditt program kräver fler förekomster än som standard etablering Se [fakturering och prenumerationshantering kvoten stöd](https://azure.microsoft.com/support/options/) för mer information om hur du ökar din kvot.

##  <a name="InputEndpoint"></a> InputEndpoint  
Den `InputEndpoint` element beskriver en extern slutpunkt till en webbroll.

Du kan definiera flera slutpunkter som är en kombination av HTTP, HTTPS, UDP och TCP-slutpunkter. Du kan ange ett annat portnummer som du väljer för en slutpunkt för indata, men de portnummer som angetts för varje roll i tjänsten måste vara unika. Till exempel om du anger att en webbroll använder port 80 för HTTP och port 443 för HTTPS, kan du sedan ange att en andra webbroll använder port 8080 för HTTP och port 8043 för HTTPS.

I följande tabell beskrivs attributen för den `InputEndpoint` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett unikt namn för den externa slutpunkten.|  
|protokoll|sträng|Krävs. Transportprotokollet för den externa slutpunkten. För en webbroll möjliga värden är `HTTP`, `HTTPS`, `UDP`, eller `TCP`.|  
|port|int|Krävs. Porten för den externa slutpunkten. Du kan ange ett annat portnummer som du väljer, men de portnummer som angetts för varje roll i tjänsten måste vara unika.<br /><br /> Möjliga värden mellan 1 och med 65535 (Azure SDK 1.7 eller högre).|  
|certifikat|sträng|Krävs för en HTTPS-slutpunkt. Namnet på ett certifikat som definieras av en `Certificate` element.|  
|lokal port|int|Valfri. Anger en port som används för interna anslutningar på slutpunkten. Den `localPort` -attributet mappar den externa porten på slutpunkten till en intern port på en roll. Detta är användbart i scenarier där en roll måste kommunicera på ett internt komponent på en port som skiljer sig från det som är exponerad externt.<br /><br /> Om inget anges värdet för `localPort` är samma som den `port` attribut. Ange värdet för `localPort` till ”*” att automatiskt tilldela en lediga port som går att identifiera med hjälp av körningsmiljön API.<br /><br /> Möjliga värden mellan 1 och med 65535 (Azure SDK 1.7 eller högre).<br /><br /> Den `localPort` attributet är endast tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.|  
|ignoreRoleInstanceStatus|boolesk|Valfri. När värdet för det här attributet anges till `true`, status för en tjänst ignoreras och slutpunkten tas inte bort av belastningsutjämnaren. Om det här värdet anges till `true` användbart för felsökning upptagen instanser av en tjänst. Standardvärdet är `false`. **Obs:** en slutpunkt kan fortfarande ta emot trafik, även om rollen inte är klar.|  
|loadBalancerProbe|sträng|Valfri. Namnet på belastningsutjämningsavsökning som är associerad med slutpunkten för indata. Mer information finns i [LoadBalancerProbe schemat](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a> InternalEndpoint  
Den `InternalEndpoint` element beskriver en intern slutpunkt till en webbroll. En intern slutpunkt är bara tillgänglig för andra rollinstanser som körs i tjänsten; Det är inte tillgängliga för klienter utanför tjänsten. Webb-roller som inte innehåller den `Sites` element kan bara ha en enda HTTP, UDP eller TCP intern slutpunkt.

I följande tabell beskrivs attributen för den `InternalEndpoint` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett unikt namn för den interna slutpunkten.|  
|protokoll|sträng|Krävs. Transportprotokollet för den interna slutpunkten. Möjliga värden är `HTTP`, `TCP`, `UDP`, eller `ANY`.<br /><br /> Värdet `ANY` anger att alla protokoll, portar är tillåtna.|  
|port|int|Valfri. Porten som används för interna belastningsutjämnade anslutningar på slutpunkten. En belastningsutjämnade slutpunkten använder två portar. Porten som används för den offentliga IP-adressen och porten som används av privata IP-adressen. Vanligtvis är dessa dessa anges till samma, men du kan välja att använda olika portar.<br /><br /> Möjliga värden mellan 1 och med 65535 (Azure SDK 1.7 eller högre).<br /><br /> Den `Port` attributet är endast tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.|  

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
Den `InstanceInputEndpoint` element beskriver en slutpunkt för instans-indata till en webbroll. En slutpunkt för indata instans är associerad med en viss roll-instans med hjälp av port vidarebefordran i belastningsutjämnaren. Varje instans indataslutpunkten mappas till en specifik port från ett intervall av möjliga portar. Det här elementet är överordnad den `AllocatePublicPortFrom` element.

Den `InstanceInputEndpoint` element är bara tillgängliga med hjälp av Azure SDK-version 1.7 eller högre.

I följande tabell beskrivs attributen för den `InstanceInputEndpoint` element.
  
| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett unikt namn för slutpunkten.|  
|lokal port|int|Krävs. Anger den interna porten som alla rollinstanser ska lyssna på för att kunna ta emot inkommande trafik vidarebefordras från belastningsutjämnaren. Möjliga värden mellan 1 och 65535, inklusive.|  
|protokoll|sträng|Krävs. Transportprotokollet för den interna slutpunkten. Möjliga värden är `udp` eller `tcp`. Använd `tcp` för http/https-baserad trafik.|  
  
##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
Den `AllocatePublicPortFrom` element beskriver offentliga portintervallet som kan användas av externa kunder att komma åt slutpunkten för indata varje instans. Offentliga (VIP)-portnumret allokerats från det här intervallet och tilldelas varje enskild roll instans slutpunkt vid distribution för klienter och uppdatera. Det här elementet är överordnad den `FixedPortRange` element.

Den `AllocatePublicPortFrom` element är bara tillgängliga med hjälp av Azure SDK-version 1.7 eller högre.

##  <a name="FixedPort"></a> FixedPort  
Den `FixedPort` elementet anger porten för intern slutpunkt, vilken gör det möjligt att läsa in belastningsutjämnade anslutningar på slutpunkten.

Den `FixedPort` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

I följande tabell beskrivs attributen för den `FixedPort` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|port|int|Krävs. Port för den interna slutpunkten. Detta har samma effekt som det `FixedPortRange` min och max till samma port.<br /><br /> Möjliga värden mellan 1 och med 65535 (Azure SDK 1.7 eller högre).|  

##  <a name="FixedPortRange"></a> FixedPortRange  
Den `FixedPortRange` element anger intervallet för de portar som är kopplade till den interna slutpunkten eller instans indataslutpunkten och anger porten som används för balanserade anslutningar på slutpunkten.

> [!NOTE]
>  Den `FixedPortRange` element fungerar på olika sätt beroende på elementet där den finns. När den `FixedPortRange` elementet har statusen på `InternalEndpoint` element, öppnas alla portar på belastningsutjämnaren inom intervallet min och max attribut för alla virtuella datorer som körs på rollen. När den `FixedPortRange` elementet har statusen på `InstanceInputEndpoint` element, öppnar en port inom intervallet för min och max-attribut på varje virtuell dator som kör rollen.

Den `FixedPortRange` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

I följande tabell beskrivs attributen för den `FixedPortRange` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|min.|int|Krävs. Den minsta porten inom intervallet. Möjliga värden mellan 1 och med 65535 (Azure SDK 1.7 eller högre).|  
|max|sträng|Krävs. Den maximala porten inom intervallet. Möjliga värden mellan 1 och med 65535 (Azure SDK 1.7 eller högre).|  

##  <a name="Certificates"></a> Certifikat  
Den `Certificates` element beskriver insamling av certifikat för en webbroll. Det här elementet är överordnad den `Certificate` element. En roll kan ha valfritt antal associerade certifikat. Mer information om hur du använder certifikat elementet finns [ändra definitionsfilen för tjänsten med ett certifikat](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> certifikat  
Den `Certificate` element beskriver ett certifikat som är associerad med en webbroll.

I följande tabell beskrivs attributen för den `Certificate` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Ett namn för det här certifikatet som används för att se den när den är associerad med en HTTPS `InputEndpoint` element.|  
|storeLocation|sträng|Krävs. Platsen för certifikatarkivet där det här certifikatet kan hittas på den lokala datorn. Möjliga värden är `CurrentUser` och `LocalMachine`.|  
|storeName|sträng|Krävs. Namnet på certifikatarkivet där det här certifikatet finns på den lokala datorn. Möjliga värden är inbyggda store namnen `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, eller ett eget Arkiv-namn. Om ett eget arkivnamn anges skapas automatiskt arkivet.|  
|permissionLevel|sträng|Valfri. Anger de åtkomstbehörigheter som tilldelas rollen processer. Om du vill att endast utökade processer för att kunna komma åt den privata nyckeln och sedan ange `elevated` behörighet. `limitedOrElevated` tillåter alla roll-processer att komma åt den privata nyckeln. Möjliga värden är `limitedOrElevated` eller `elevated`. Standardvärdet är `limitedOrElevated`.|  

##  <a name="Imports"></a> Import  
Den `Imports` element beskriver en samling importera moduler för en webbroll som lägger till komponenter i gästoperativsystemet. Det här elementet är överordnad den `Import` element. Det här elementet är valfritt och en roll kan ha endast en import-block. 

Den `Imports` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

##  <a name="Import"></a> Importera  
Den `Import` element anger en modul som ska läggas till gästoperativsystemet.

Den `Import` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

I följande tabell beskrivs attributen för den `Import` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|Modulnamn|sträng|Krävs. Namnet på modulen som ska importeras. Giltig importera moduler är:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Diagnostik<br /><br /> RemoteAccess- och RemoteForwarder-modulerna kan du konfigurera din instans av serverroll för anslutning till fjärrskrivbord. Mer information finns i [aktivera anslutning till fjärrskrivbord](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Diagnostik-modulen kan du samla in diagnostikdata för en rollinstans.|  

##  <a name="Runtime"></a> Runtime  
Den `Runtime` element beskriver en samling inställningar för miljövariabeln en webbroll som styr körningsmiljön processens Azure-värd. Det här elementet är överordnad den `Environment` element. Det här elementet är valfritt och en roll kan ha endast en runtime-block.

Den `Runtime` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

I följande tabell beskrivs attributen för den `Runtime` element:  

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|executionContext|sträng|Valfri. Anger kontext där rollen processen startas. Standardkontexten är `limited`.<br /><br /> -   `limited` – Processen startas utan administratörsbehörighet.<br />-   `elevated` – Processen startas med administratörsbehörighet.|  

##  <a name="Environment"></a> Miljö  
Den `Environment` element beskriver en samling inställningar för en webbroll miljövariabeln. Det här elementet är överordnad den `Variable` element. En roll kan ha valfritt antal miljö variabler uppsättningen.

##  <a name="Variable"></a> variabeln  
Den `Variable` element anger en miljövariabel som du anger i gästoperativsystemet.

Den `Variable` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

I följande tabell beskrivs attributen för den `Variable` element:  

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Namnet på miljövariabeln för att ange.|  
|värde|sträng|Valfri. Värdet som angetts för miljövariabeln. Du måste inkludera värdeattributet eller en `RoleInstanceValue` element.|  

##  <a name="RoleInstanceValue"></a> RoleInstanceValue  
Den `RoleInstanceValue` elementet anger xPath som du vill hämta värdet för variabeln.

I följande tabell beskrivs attributen för den `RoleInstanceValue` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|XPath|sträng|Valfri. Sökvägen till distributionsinställningar för instansen. Mer information finns i [Configuration variabler med XPath](cloud-services-role-config-xpath.md).<br /><br /> Du måste inkludera värdeattributet eller en `RoleInstanceValue` element.|  

##  <a name="EntryPoint"></a> EntryPoint  
Den `EntryPoint` element anger startpunkten för en roll. Det här elementet är överordnad den `NetFxEntryPoint` element. De här elementen kan du ange ett annat program än standard WaWorkerHost.exe ska fungera som startpunkt för rollen.

Den `EntryPoint` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
Den `NetFxEntryPoint` element anger att programmet kan köras för en roll.

> [!NOTE]
>  Den `NetFxEntryPoint` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

I följande tabell beskrivs attributen för den `NetFxEntryPoint` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|AssemblyName|sträng|Krävs. Sökvägen och namnet på den sammansättning som innehåller startpunkten. Sökvägen är i förhållande till mappen  **\\%ROLEROOT%\Approot** (Ange inte  **\\%ROLEROOT%\Approot** i `commandLine`, förutsätts). **% ROLEROOT %** är en miljövariabel som underhålls av Azure och det representerar roten mappen för din roll. Den  **\\%ROLEROOT%\Approot** som representerar programmappen för din roll.<br /><br /> För HWC roller är alltid förhållande till den  **\\%ROLEROOT%\Approot\bin** mapp.<br /><br /> För fullständig IIS och IIS Express web roller, om det inte går att hitta sammansättningen förhållande till  **\\%ROLEROOT%\Approot** mapp, den  **\\%ROLEROOT%\Approot\bin** söks igenom.<br /><br /> Den här faller tillbaka beteendet för fullständig IIS är inte bästa praxis rekommenderar och kan ta bort i framtida versioner.|  
|targetFrameworkVersion|sträng|Krävs. Versionen av .NET framework när sammansättningen skapades. Till exempel `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a> webbplatser  
Den `Sites` element beskriver en uppsättning webbplatser och webbprogram program som finns i en webbroll. Det här elementet är överordnad den `Site` element. Om du inte anger en `Sites` elementet webbrollen finns som äldre webbroll och du kan bara ha en webbplats som finns i din webbroll. Det här elementet är valfritt och en roll kan ha bara ett block av platser.

Den `Sites` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

##  <a name="Site"></a> plats  
Den `Site` element anger webbplatsen eller programmet som ingår i rollen webbserver.

Den `Site` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

I följande tabell beskrivs attributen för den `Site` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Namnet på webbplatsen eller programmet.|  
|physicalDirectory|sträng|Platsen för innehållskatalogen för platsens rot. Platsen kan anges som en absolut sökväg eller i förhållande till .csdef-plats.|  

##  <a name="VirtualApplication"></a> VirtualApplication  
Den `VirtualApplication` elementet definierar ett program i Internet Information Services (IIS) 7 är en grupp med filer som levererar innehåll eller tillhandahåller tjänster över protokoll, t.ex HTTP. När du skapar ett program i IIS 7 blir programmets sökväg en del av webbplatsens URL.

Den `VirtualApplication` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

I följande tabell beskrivs attributen för den `VirtualApplication` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Anger ett namn som identifierar det virtuella programmet.|  
|physicalDirectory|sträng|Krävs. Anger en sökväg på utvecklingsdator som innehåller det virtuella programmet. IIS har konfigurerats för att hämta innehåll från den här platsen i beräkningsemulatorn. När du distribuerar till Azure är innehållet i den fysiska katalogen packade tillsammans med resten av tjänsten. IIS har konfigurerats med platsen för packa upp innehållet när tjänstepaketet distribueras till Azure.|  

##  <a name="VirtualDirectory"></a> VirtualDirectory  
Den `VirtualDirectory` element anger namnet på en katalog (kallas även sökväg) som du anger i IIS och mappa till en fysisk katalog på en lokal eller fjärransluten server.

Den `VirtualDirectory` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

I följande tabell beskrivs attributen för den `VirtualDirectory` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Anger ett namn som identifierar den virtuella katalogen.|  
|värde|physicalDirectory|Krävs. Anger en sökväg på utvecklingsdator som innehåller den webbplats eller det virtuella kataloginnehållet. IIS har konfigurerats för att hämta innehåll från den här platsen i beräkningsemulatorn. När du distribuerar till Azure är innehållet i den fysiska katalogen packade tillsammans med resten av tjänsten. IIS har konfigurerats med platsen för packa upp innehållet när tjänstepaketet distribueras till Azure.|  

##  <a name="Bindings"></a> Bindningar  
Den `Bindings` element beskriver en samling av bindningar för en webbplats. Det är det överordnade elementet för den `Binding` element. Elementet krävs för varje `Site` element. Mer information om hur du konfigurerar slutpunkter finns [aktivera kommunikation för Rollinstanser](cloud-services-enable-communication-role-instances.md).

Den `Bindings` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

##  <a name="Binding"></a> Bindning  
Den `Binding` elementet anger konfigurationsinformation som krävs för begäranden att kommunicera med webbplatsen eller programmet.

Den `Binding` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|namn|sträng|Krävs. Anger ett namn som identifierar bindningen.|  
|EndpointName|sträng|Krävs. Anger namnet på slutpunkten ska bindas till.|  
|Värdhuvud|sträng|Valfri. Anger ett värdnamn som gör det möjligt för flera platser med olika värdnamn, på en enda antalet IP-adress och Port-kombination.|  

##  <a name="Startup"></a> Start  
Den `Startup` element beskriver en uppsättning uppgifter som körs när rollen har startats. Det här elementet kan vara överordnad de `Variable` element. Mer information om hur du använder rollen startade uppgifter finns [hur du konfigurerar Start uppgifter](cloud-services-startup-tasks.md). Det här elementet är valfritt och en roll kan ha endast en start-block.

I följande tabell beskrivs attributet för den `Startup` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|prioritet|int|Endast för intern användning.|  

##  <a name="Task"></a> Aktivitet  
Den `Task` element anger startaktivitet som sker när rollen startar. Start-aktiviteter kan användas för att utföra uppgifter som förbereder rollen och köra sådana installera programvarukomponenter eller köra andra program. Uppgifter som utförs i den ordning som de visas inom den `Startup` elementblock.

Den `Task` element är bara tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

I följande tabell beskrivs attributen för den `Task` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|Kommandorad|sträng|Krävs. Ett skript, till exempel en CMD-fil som innehåller kommandona som ska köras. Kommandot och batch startfiler måste sparas i ANSI-format. Filformat som en byte-ordning markering i början av filen bearbetas inte korrekt.|  
|executionContext|sträng|Anger kontext där skriptet körs.<br /><br /> -   `limited` [Standard] – köras med samma behörigheter som rollen värd för processen.<br />-   `elevated` – Kör med administratörsbehörighet.|  
|taskType|sträng|Anger beteendet för körning av kommandot.<br /><br /> -   `simple` [Standard] – systemet väntar på aktiviteten för att avsluta innan andra uppgifter startas.<br />-   `background` – Systemet väntar inte aktiviteten för att avsluta.<br />-   `foreground` – Liknar bakgrund, förutom rollen inte startas förrän alla aktiviteter i förgrunden avsluta.|  

##  <a name="Contents"></a> Innehållet  
Den `Contents` element beskriver det innehåll för en webbroll. Det här elementet är överordnad den `Content` element.

Den `Contents` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

##  <a name="Content"></a> Innehåll  
Den `Content` elementet definierar källplats för innehåll som ska kopieras till den virtuella Azure-datorn och målsökvägen som kopieras.

Den `Content` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

I följande tabell beskrivs attributen för den `Content` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|Mål|sträng|Krävs. Plats på den virtuella Azure-datorn som innehållet ska placeras. Den här platsen är i förhållande till mappen **%ROLEROOT%\Approot**.|  

Det här elementet har det överordnade elementet för den `SourceDirectory` element.

##  <a name="SourceDirectory"></a> SourceDirectory  
Den `SourceDirectory` elementet definierar lokal katalog där innehållet ska kopieras. Använd det här elementet för att ange lokala innehållet ska kopieras till den virtuella Azure-datorn.

Den `SourceDirectory` element är bara tillgängliga med hjälp av Azure SDK-version 1.5 eller senare.

I följande tabell beskrivs attributen för den `SourceDirectory` element.

| Attribut | Typ | Beskrivning |  
| --------- | ---- | ----------- |  
|sökväg|sträng|Krävs. Relativ eller absolut sökväg till en lokal katalog som innehållet ska kopieras till den virtuella Azure-datorn. Utökningen av miljövariabler i sökvägen stöds.|  
  
## <a name="see-also"></a>Se även
[Cloud Service (klassiskt) Definition av schemat](schema-csdef-file.md)
