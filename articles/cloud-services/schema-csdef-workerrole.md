---
title: Def. WorkerRole-schema för Azure Cloud Services | Microsoft-dokument
description: Azure-arbetarrollen används för allmän utveckling och kan utföra bakgrundsbearbetning för en webbroll. Lär dig mer om Azure-arbetsrollschemat.
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
ms.openlocfilehash: 26225442c72fb209bb1ac4cd2bf4777fb39542fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534379"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Definition av Azure Cloud Services-schema för arbetsroll
Azure-arbetarrollen är en roll som är användbar för allmän utveckling och som kan utföra bakgrundsbearbetning för en webbroll.

Standardtillägget för tjänstdefinitionsfilen är .csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Grundläggande tjänstdefinitionsschema för en arbetsroll.
Det grundläggande formatet för tjänstdefinitionsfilen som innehåller en arbetarroll är följande.

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

## <a name="schema-elements"></a>Schemaelement
Tjänstdefinitionsfilen innehåller dessa element, som beskrivs i detalj i efterföljande avsnitt i det här avsnittet:

[WorkerRole (Arbetarrolle)](#WorkerRole)

[Konfigurationsinställningar](#ConfigurationSettings)

[Inställning](#Setting)

[Lokala resurser](#LocalResources)

[Localstorage](#LocalStorage)

[Slutpunkter](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllokeraPublicPortFrom](#AllocatePublicPortFrom)

[FixedPort (fastport)](#FixedPort)

[FixedPortRange (FastPortRange)](#FixedPortRange)

[Certifikat](#Certificates)

[Certifikat](#Certificate)

[Import](#Imports)

[Importera](#Import)

[Körmiljö](#Runtime)

[Miljö](#Environment)

[EntryPoint (På)](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variabel](#Variable)

[RollInstanceVärde](#RoleInstanceValue)

[Start](#Startup)

[Aktivitet](#Task)

[Innehåll](#Contents)

[Innehåll](#Content)

[KällaKatalog](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a>WorkerRole (Arbetarrolle)
Elementet `WorkerRole` beskriver en roll som är användbar för allmän utveckling och kan utföra bakgrundsbearbetning för en webbroll. En tjänst kan innehålla noll eller fler arbetarroller.

I följande tabell beskrivs elementets `WorkerRole` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Namnet på arbetarrollen. Rollens namn måste vara unikt.|
|enableNativeCodeExecution enableNativeCodeExecution enableNativeCodeExecution enableN|boolean|Valfri. Standardvärdet är `true`; inbyggt kodkörning och fullständigt förtroende är aktiverade som standard. Ange det `false` här attributet för att inaktivera körning av inbyggd kod för arbetarrollen och använd Azure partiellt förtroende i stället.|
|vmsize vmsize vmsize vms|sträng|Valfri. Ange det här värdet om du vill ändra storleken på den virtuella datorn som tilldelas den här rollen. Standardvärdet är `Small`. En lista över möjliga storlekar för virtuella datorer och deras attribut finns i [Storlekar för virtuella datorer för molntjänster](cloud-services-sizes-specs.md).|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a>Konfigurationsinställningar
Elementet `ConfigurationSettings` beskriver samlingen av konfigurationsinställningar för en arbetsroll. Det här elementet `Setting` är överordnat elementet.

##  <a name="setting"></a><a name="Setting"></a>Inställningen
Elementet `Setting` beskriver ett namn och värdepar som anger en konfigurationsinställning för en förekomst av en roll.

I följande tabell beskrivs elementets `Setting` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för konfigurationsinställningen.|

Konfigurationsinställningarna för en roll är namn- och värdepar som deklareras i tjänstdefinitionsfilen och som anges i tjänstkonfigurationsfilen.

##  <a name="localresources"></a><a name="LocalResources"></a>Lokala resurser
Elementet `LocalResources` beskriver insamlingen av lokala lagringsresurser för en arbetarroll. Det här elementet `LocalStorage` är överordnat elementet.

##  <a name="localstorage"></a><a name="LocalStorage"></a>Localstorage
Elementet `LocalStorage` identifierar en lokal lagringsresurs som tillhandahåller filsystemutrymme för tjänsten vid körning. En roll kan definiera noll eller fler lokala lagringsresurser.

> [!NOTE]
>  Elementet `LocalStorage` kan visas som `WorkerRole` ett underordnat element för att stödja kompatibilitet med tidigare versioner av Azure SDK.

I följande tabell beskrivs elementets `LocalStorage` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för den lokala butiken.|
|cleanOnRoleRecycle|boolean|Valfri. Anger om det lokala arkivet ska rensas när rollen startas om. Standardvärdet `true`är .|
|sizeInMb|int|Valfri. Den önskade mängden lagringsutrymme att allokera för det lokala arkivet, i MB. Om inget anges är standardlagringsutrymmet 100 MB. Den minsta mängden lagringsutrymme som kan tilldelas är 1 MB.<br /><br /> Den maximala storleken på de lokala resurserna beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer för molntjänster](cloud-services-sizes-specs.md).|

Namnet på den katalog som allokerats till den lokala lagringsresursen motsvarar värdet som anges för namnattributet.

##  <a name="endpoints"></a><a name="Endpoints"></a>Slutpunkter
Elementet `Endpoints` beskriver insamlingen av indata (externa), interna och instansindataslutpunkter för en roll. Det här elementet `InputEndpoint`är `InternalEndpoint`överordnat elementen , och `InstanceInputEndpoint` elementen.

Indata och interna slutpunkter fördelas separat. En tjänst kan ha totalt 25 indata-, intern- och instansingslutpunkter som kan allokeras mellan de 25 roller som tillåts i en tjänst. Om du till exempel har 5 roller kan du allokera 5 indataslutpunkter per roll eller så kan du allokera 25 indataslutpunkter till en enda roll eller så kan du allokera 1 ingångsslutpunkt vardera till 25 roller.

> [!NOTE]
>  Varje distribuerad roll kräver en instans per roll. Standardetablering för en prenumeration är begränsad till 20 kärnor och är därför begränsad till 20 instanser av en roll. Om ditt program kräver fler instanser än vad som anges av standardetablering se [Fakturering, Prenumerationshantering och kvotstöd](https://azure.microsoft.com/support/options/) för mer information om hur du ökar din kvot.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a>InputEndpoint
Elementet `InputEndpoint` beskriver en extern slutpunkt till en arbetarroll.

Du kan definiera flera slutpunkter som är en kombination av HTTP-, HTTPS-, UDP- och TCP-slutpunkter. Du kan ange valfritt portnummer som du väljer för en indataslutpunkt, men portnumren som anges för varje roll i tjänsten måste vara unika. Om du till exempel anger att en roll använder port 80 för HTTP och port 443 för HTTPS kan du ange att en andra roll använder port 8080 för HTTP och port 8043 för HTTPS.

I följande tabell beskrivs elementets `InputEndpoint` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för den externa slutpunkten.|
|Protokollet|sträng|Krävs. Transportprotokollet för den externa slutpunkten. För en arbetarroll är `HTTP` `HTTPS`möjliga `UDP`värden `TCP`, , eller .|
|port|int|Krävs. Porten för den externa slutpunkten. Du kan ange valfritt portnummer som du väljer, men de portnummer som anges för varje roll i tjänsten måste vara unika.<br /><br /> Möjliga värden varierar mellan 1 och 65535, inklusive (Azure SDK version 1.7 eller senare).|
|certifikat|sträng|Krävs för en HTTPS-slutpunkt. Namnet på ett certifikat `Certificate` som definieras av ett element.|
|lokalPort|int|Valfri. Anger en port som används för interna anslutningar på slutpunkten. Attributet `localPort` mappar den externa porten på slutpunkten till en intern port på en roll. Detta är användbart i scenarier där en roll måste kommunicera med en intern komponent på en port som skiljer sig från den som exponeras externt.<br /><br /> Om inget anges `localPort` är värdet för samma `port` värde som attributet. Ange värdet `localPort` för att "*" för att automatiskt tilldela en oallokerad port som kan identifieras med hjälp av körnings-API:et.<br /><br /> Möjliga värden varierar mellan 1 och 65535, inklusive (Azure SDK version 1.7 eller senare).<br /><br /> Attributet `localPort` är endast tillgängligt med Azure SDK version 1.3 eller senare.|
|ignoreraRoleInstanceStatus|boolean|Valfri. När värdet för det här `true`attributet är inställt på ignoreras statusen för en tjänst och slutpunkten tas inte bort av belastningsutjämnaren. Ange det `true` här värdet för felsökning av upptagna instanser av en tjänst. Standardvärdet är `false`. **Anm.:** En slutpunkt kan fortfarande ta emot trafik även när rollen inte är i ett färdigt tillstånd.|
|loadBalancerProbe|sträng|Valfri. Namnet på belastningsutjämnaren som är associerad med ingångsslutpunkten. Mer information finns i [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a>InternEndpoint
Elementet `InternalEndpoint` beskriver en intern slutpunkt till en arbetarroll. En intern slutpunkt är endast tillgänglig för andra rollinstanser som körs inom tjänsten. Den är inte tillgänglig för kunder utanför tjänsten. En arbetarroll kan ha upp till fem interna slutpunkter för HTTP, UDP eller TCP.

I följande tabell beskrivs elementets `InternalEndpoint` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för den interna slutpunkten.|
|Protokollet|sträng|Krävs. Transportprotokollet för den interna slutpunkten. Möjliga värden `HTTP` `TCP`är `UDP`, `ANY`, eller .<br /><br /> Ett värde `ANY` av anger att alla protokoll, alla portar tillåts.|
|port|int|Valfri. Porten som används för interna belastningsbalanserade anslutningar på slutpunkten. En belastningsbalanserad slutpunkt använder två portar. Porten som används för den offentliga IP-adressen och porten som används på den privata IP-adressen. Vanligtvis är dessa är de inställda på samma, men du kan välja att använda olika portar.<br /><br /> Möjliga värden varierar mellan 1 och 65535, inklusive (Azure SDK version 1.7 eller senare).<br /><br /> Attributet `Port` är endast tillgängligt med Azure SDK version 1.3 eller senare.|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a>InstanceInputEndpoint
Elementet `InstanceInputEndpoint` beskriver en instansinmatningsslutpunkt till en arbetsroll. En instansinmatningsslutpunkt associeras med en specifik rollinstans med hjälp av portbefordring i belastningsutjämnaren. Varje instansindataslutpunkt mappas till en specifik port från en rad möjliga portar. Det här elementet `AllocatePublicPortFrom` är överordnat elementet.

Elementet `InstanceInputEndpoint` är endast tillgängligt med Azure SDK version 1.7 eller senare.

I följande tabell beskrivs elementets `InstanceInputEndpoint` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett unikt namn för slutpunkten.|
|lokalPort|int|Krävs. Anger den interna port som alla rollinstanser ska lyssna på för att ta emot inkommande trafik som vidarebefordras från belastningsutjämnaren. Möjliga värden varierar mellan 1 och 65535, inklusive.|
|Protokollet|sträng|Krävs. Transportprotokollet för den interna slutpunkten. Möjliga värden är `udp` eller `tcp`. Används `tcp` för http/https-baserad trafik.|

##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a>AllokeraPublicPortFrom
Elementet `AllocatePublicPortFrom` beskriver det offentliga portintervall som kan användas av externa kunder för att komma åt varje instansinmatningsslutpunkt. Det offentliga (VIP)portnumret allokeras från det här intervallet och tilldelas varje enskild rollinstansslutpunkt under klientdistribution och uppdatering. Det här elementet `FixedPortRange` är överordnat elementet.

Elementet `AllocatePublicPortFrom` är endast tillgängligt med Azure SDK version 1.7 eller senare.

##  <a name="fixedport"></a><a name="FixedPort"></a>FixedPort (fastport)
Elementet `FixedPort` anger porten för den interna slutpunkten, vilket möjliggör belastningsbalanserade anslutningar på slutpunkten.

Elementet `FixedPort` är endast tillgängligt med Azure SDK version 1.3 eller senare.

I följande tabell beskrivs elementets `FixedPort` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|port|int|Krävs. Porten för den interna slutpunkten. Detta har samma effekt `FixedPortRange` som att ställa in min och max till samma port.<br /><br /> Möjliga värden varierar mellan 1 och 65535, inklusive (Azure SDK version 1.7 eller senare).|

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a>FixedPortRange (FastPortRange)
`FixedPortRange` Elementet anger det portintervall som tilldelas den interna slutpunkten eller instansinmatningsslutpunkten och anger porten som används för belastningsbalanserade anslutningar på slutpunkten.

> [!NOTE]
>  Elementet `FixedPortRange` fungerar olika beroende på vilket element det finns. När `FixedPortRange` elementet finns `InternalEndpoint` i elementet öppnas alla portar på belastningsutjämnaren inom intervallet för attributen min och max för alla virtuella datorer som rollen körs på. När `FixedPortRange` elementet finns `InstanceInputEndpoint` i elementet öppnas bara en port inom intervallet för attributen min och max på varje virtuell dator som kör rollen.

Elementet `FixedPortRange` är endast tillgängligt med Azure SDK version 1.3 eller senare.

I följande tabell beskrivs elementets `FixedPortRange` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|min|int|Krävs. Minsta port i intervallet. Möjliga värden varierar mellan 1 och 65535, inklusive (Azure SDK version 1.7 eller senare).|
|Max|sträng|Krävs. Den maximala porten i intervallet. Möjliga värden varierar mellan 1 och 65535, inklusive (Azure SDK version 1.7 eller senare).|

##  <a name="certificates"></a><a name="Certificates"></a>Certifikat
Elementet `Certificates` beskriver insamlingen av certifikat för en arbetarroll. Det här elementet `Certificate` är överordnat elementet. En roll kan ha valfritt antal associerade certifikat. Mer information om hur du använder certifikatelementet finns i [Ändra servicedefinitionsfilen med ett certifikat](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a>Certifikat
Elementet `Certificate` beskriver ett certifikat som är associerat med en arbetsroll.

I följande tabell beskrivs elementets `Certificate` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Ett namn på det här certifikatet som används för att `InputEndpoint` referera till det när det är associerat med ett HTTPS-element.|
|storeLocation|sträng|Krävs. Platsen för certifikatarkivet där certifikatet finns på den lokala datorn. Möjliga värden `CurrentUser` `LocalMachine`är och .|
|storeName (storeName)|sträng|Krävs. Namnet på certifikatarkivet där certifikatet finns på den lokala datorn. Möjliga värden är de inbyggda `Root` `CA`butiksnamnen `TrustedPeople` `My` `TrustedPublisher`, `AuthRoot` `AddressBook`, `Trust`, `Disallowed`, , , , , , eller ett anpassat butiksnamn. Om ett anpassat butiksnamn anges skapas arkivet automatiskt.|
|behörighetNivå|sträng|Valfri. Anger de åtkomstbehörigheter som ges till rollprocesserna. Om du bara vill att förhöjda processer ska `elevated` kunna komma åt den privata nyckeln anger du behörighet. `limitedOrElevated`behörigheten gör att alla rollprocesser kan komma åt den privata nyckeln. Möjliga värden är `limitedOrElevated` eller `elevated`. Standardvärdet är `limitedOrElevated`.|

##  <a name="imports"></a><a name="Imports"></a>Import
Elementet `Imports` beskriver en samling importmoduler för en arbetarroll som lägger till komponenter i gästoperativsystemet. Det här elementet `Import` är överordnat elementet. Det här elementet är valfritt och en roll kan bara ha ett körningsblock.

Elementet `Imports` är endast tillgängligt med Azure SDK version 1.3 eller senare.

##  <a name="import"></a><a name="Import"></a>Importera
Elementet `Import` anger en modul som ska läggas till i gästoperativsystemet.

Elementet `Import` är endast tillgängligt med Azure SDK version 1.3 eller senare.

I följande tabell beskrivs elementets `Import` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|moduleName (modulNamn)|sträng|Krävs. Namnet på modulen som ska importeras. Giltiga importmoduler är:<br /><br /> - Fjärråtkomst<br />- RemoteForwarder<br />- Diagnostik<br /><br /> Med modulerna RemoteAccess och RemoteForwarder kan du konfigurera din rollinstans för fjärrskrivbordsanslutningar. Mer information finns i [Aktivera anslutning till fjärrskrivbord](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Med diagnostikmodulen kan du samla in diagnostikdata för en rollinstans|

##  <a name="runtime"></a><a name="Runtime"></a>Runtime
Elementet `Runtime` beskriver en samling inställningar för miljövariabel för en arbetsroll som styr körningsmiljön för Azure-värdprocessen. Det här elementet `Environment` är överordnat elementet. Det här elementet är valfritt och en roll kan bara ha ett körningsblock.

Elementet `Runtime` är endast tillgängligt med Azure SDK version 1.3 eller senare.

I följande tabell beskrivs elementets `Runtime` attribut:

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|körningKontext|sträng|Valfri. Anger i vilken kontext rollprocessen startas. Standardkontexten `limited`är .<br /><br /> -   `limited`– Processen startas utan administratörsbehörighet.<br />-   `elevated`– Processen startas med administratörsbehörighet.|

##  <a name="environment"></a><a name="Environment"></a>Miljö
Elementet `Environment` beskriver en samling inställningar för miljövariabel för en arbetsroll. Det här elementet `Variable` är överordnat elementet. En roll kan ha valfritt antal miljövariabler inställda.

##  <a name="variable"></a><a name="Variable"></a>Variabel
Elementet `Variable` anger en miljövariabel som ska anges i gästens verksamhet.

Elementet `Variable` är endast tillgängligt med Azure SDK version 1.3 eller senare.

I följande tabell beskrivs elementets `Variable` attribut:

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|namn|sträng|Krävs. Namnet på den miljövariabel som ska anges.|
|värde|sträng|Valfri. Det värde som ska anges för miljövariabeln. Du måste inkludera antingen ett `RoleInstanceValue` värdeattribut eller ett element.|

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a>RollInstanceVärde
Elementet `RoleInstanceValue` anger den xPath som variabeln ska hämtas från.

I följande tabell beskrivs elementets `RoleInstanceValue` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Xpath|sträng|Valfri. Platssökväg för distributionsinställningar för instansen. Mer information finns i [Konfigurationsvariabler med XPath](cloud-services-role-config-xpath.md).<br /><br /> Du måste inkludera antingen ett `RoleInstanceValue` värdeattribut eller ett element.|

##  <a name="entrypoint"></a><a name="EntryPoint"></a>EntryPoint (På)
Elementet `EntryPoint` anger startpunkten för en roll. Det här elementet `NetFxEntryPoint` är överordnat elementen. Med dessa element kan du ange ett annat program än standardinställningarna för WaWorkerHost.exe för att fungera som rollstartpunkt.

Elementet `EntryPoint` är endast tillgängligt med Azure SDK version 1.5 eller senare.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a>NetFxEntryPoint
Elementet `NetFxEntryPoint` anger vilket program som ska köras för en roll.

> [!NOTE]
>  Elementet `NetFxEntryPoint` är endast tillgängligt med Azure SDK version 1.5 eller senare.

I följande tabell beskrivs elementets `NetFxEntryPoint` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|assemblyName (sammansättningsnamn)|sträng|Krävs. Sökvägen och filnamnet på sammansättningen som innehåller startpunkten. Sökvägen är relativ till mappen `commandLine` ** \\%ROLEROOT%\Approot** (ange ** \\inte %ROLEROOT%\Approot** i , antas det). **%ROLEROOT%** är en miljövariabel som underhålls av Azure och den representerar rotmappens plats för din roll. Mappen ** \\%ROLEROOT%\Approot** representerar programmappen för din roll.|
|targetFrameworkVersion|sträng|Krävs. Den version av .NET-ramverket som sammansättningen byggdes på. Till exempel `targetFrameworkVersion="v4.0"`.|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a>ProgramEntryPoint
Elementet `ProgramEntryPoint` anger vilket program som ska köras för en roll. Med `ProgramEntryPoint` elementet kan du ange en programstartpunkt som inte baseras på en .NET-sammansättning.

> [!NOTE]
>  Elementet `ProgramEntryPoint` är endast tillgängligt med Azure SDK version 1.5 eller senare.

I följande tabell beskrivs elementets `ProgramEntryPoint` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Kommandorad|sträng|Krävs. Sökvägen, filnamnet och eventuella kommandoradsargument i programmet som ska köras. Sökvägen är relativ till mappen **%ROLEROOT%\Approt** (ange inte **%ROLEROOT%\Approot** i commandLine antas det). **%ROLEROOT%** är en miljövariabel som underhålls av Azure och den representerar rotmappens plats för din roll. Mappen **%ROLEROOT%\Approot** representerar programmappen för din roll.<br /><br /> Om programmet avslutas återvinns rollen, så i allmänhet ställa in programmet att fortsätta att köras, i stället för att vara ett program som bara startar upp och kör en begränsad uppgift.|
|setReadyOnProcessStart|boolean|Krävs. Anger om rollinstansen väntar på att kommandoradsprogrammet ska signalera att det startas. Det här värdet `true` måste anges till just nu. Ange värdet `false` till är reserverat för framtida bruk.|

##  <a name="startup"></a><a name="Startup"></a>Start
Elementet `Startup` beskriver en samling aktiviteter som körs när rollen startas. Det här elementet kan `Variable` vara överordnat elementet. Mer information om hur du använder rollstartuppgifterna finns i [Så här konfigurerar du startuppgifter](cloud-services-startup-tasks.md). Det här elementet är valfritt och en roll kan bara ha ett startblock.

I följande tabell beskrivs `Startup` elementets attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|prioritet|int|Endast för internt bruk.|

##  <a name="task"></a><a name="Task"></a>Uppgift
Elementet `Task` anger startaktivitet som äger rum när rollen startar. Startuppgifter kan användas för att utföra uppgifter som förbereder rollen för att köra sådana installationsprogramkomponenter eller köra andra program. Uppgifter körs i den ordning de `Startup` visas i elementblocket.

Elementet `Task` är endast tillgängligt med Azure SDK version 1.3 eller senare.

I följande tabell beskrivs elementets `Task` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|Kommandorad|sträng|Krävs. Ett skript, till exempel en CMD-fil, som innehåller de kommandon som ska köras. Startkommandot och batchfilerna måste sparas i ANSI-format. Filformat som anger en byte-order markör i början av filen kommer inte att bearbetas korrekt.|
|körningKontext|sträng|Anger i vilken kontext skriptet körs.<br /><br /> -   `limited`[Standard] – Kör med samma privilegier som rollen som värd för processen.<br />-   `elevated`– Kör med administratörsbehörighet.|
|taskType (aktivitetstyp)|sträng|Anger kommandots körningsbeteende.<br /><br /> -   `simple`[Standard] – Systemet väntar på att aktiviteten ska avslutas innan andra aktiviteter startas.<br />-   `background`– Systemet väntar inte på att aktiviteten ska avslutas.<br />-   `foreground`– Liknar bakgrunden, förutom att rollen inte startas om förrän alla förgrundsuppgifter har avslutats.|

##  <a name="contents"></a><a name="Contents"></a>Innehållet
Elementet `Contents` beskriver insamlingen av innehåll för en arbetarroll. Det här elementet `Content` är överordnat elementet.

Elementet `Contents` är endast tillgängligt med Azure SDK version 1.5 eller senare.

##  <a name="content"></a><a name="Content"></a>Innehåll
Elementet `Content` definierar källplatsen för innehåll som ska kopieras till den virtuella Azure-datorn och målsökvägen som den kopieras till.

Elementet `Content` är endast tillgängligt med Azure SDK version 1.5 eller senare.

I följande tabell beskrivs elementets `Content` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|mål|sträng|Krävs. Plats på den virtuella Azure-datorn som innehållet är placerat på. Den här platsen är relativ till mappen **%ROLEROOT%\Approot**.|

Det här elementet är `SourceDirectory` elementets överordnade element.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a>KällaKatalog
Elementet `SourceDirectory` definierar den lokala katalog från vilken innehåll kopieras. Använd det här elementet för att ange det lokala innehållet som ska kopieras till den virtuella Azure-datorn.

Elementet `SourceDirectory` är endast tillgängligt med Azure SDK version 1.5 eller senare.

I följande tabell beskrivs elementets `SourceDirectory` attribut.

| Attribut | Typ | Beskrivning |
| --------- | ---- | ----------- |
|path|sträng|Krävs. Relativ eller absolut sökväg till en lokal katalog vars innehåll kommer att kopieras till den virtuella Azure-datorn. Det stöds expansion av miljövariabler i katalogsökvägen.|

## <a name="see-also"></a>Se även
[Molntjänst (klassiskt) definitionsschema](schema-csdef-file.md)



