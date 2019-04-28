---
title: Vad är en tjänst i molnet och paketet | Microsoft Docs
description: Beskriver cloud tjänstmodellen (.csdef, .cscfg) och paketet (.cspkg) i Azure
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 9c9f7dfd9ecbf085da19fc010e497caef8c18629
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432644"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Vad är Cloud Service-modellen och hur jag paketera den?
En molnbaserad tjänst har skapats från tre komponenter, tjänstdefinitionen *(.csdef)*, service-config *(.cscfg)*, och inget tjänstepaket *(.cspkg)*. Både den **ServiceDefinition.csdef** och **ServiceConfig.cscfg** filer är XML-baserade och beskriver strukturen för Molntjänsten och hur den är konfigurerad; kallade modellen. Den **ServicePackage.cspkg** är en zip-fil som skapas från den **ServiceDefinition.csdef** och bland annat innehåller alla nödvändiga binary-baserade beroenden. Azure skapar en molnbaserad tjänst från både den **ServicePackage.cspkg** och **ServiceConfig.cscfg**.

När Molntjänsten körs i Azure kan du konfigurera den via den **ServiceConfig.cscfg** filen, men du kan inte ändra definitionen.

## <a name="what-would-you-like-to-know-more-about"></a>Vad vill du veta mer om?
* Jag vill veta mer om den [ServiceDefinition.csdef](#csdef) och [ServiceConfig.cscfg](#cscfg) filer.
* Jag redan vet om det, ge mig [några exempel](#next-steps) på vad jag kan konfigurera.
* Jag vill skapa den [ServicePackage.cspkg](#cspkg).
* Jag använder Visual Studio och jag vill...
  * [Skapa en molntjänst][vs_create]
  * [Konfigurera om en befintlig molntjänst][vs_reconfigure]
  * [Distribuera en Cloud Service-projekt][vs_deploy]
  * [Fjärrskrivbord i en molntjänstinstans][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
Den **ServiceDefinition.csdef** filen anger de inställningar som används av Azure för att konfigurera en molntjänst. Den [Azure Tjänstdefinitionsschemat (.csdef-filen)](/previous-versions/azure/reference/ee758711(v=azure.100)) innehåller tillåten formatet för en tjänstdefinitionsfilen. I följande exempel visas de inställningar som kan definieras för webb- och Worker-roller:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Du kan referera till den [Tjänstdefinitionsschemat](/previous-versions/azure/reference/ee758711(v=azure.100)) för en bättre förståelse för XML-schema används här, men här är en snabb förklaring av vissa element:

**Platser**  
Innehåller definitioner för webbplatser eller webbprogram program som finns i IIS7.

**InputEndpoints**  
Innehåller definitioner för slutpunkter som används för att kontakta Molntjänsten.

**InternalEndpoints**  
Innehåller definitioner för slutpunkter som används av rollinstanser för att kommunicera med varandra.

**ConfigurationSettings**  
Innehåller inställningen definitionerna för funktioner för en viss roll.

**Certifikat**  
Innehåller definitioner för certifikat som behövs för en roll. Det förra exemplet visar ett certifikat som används för konfigurationen av Azure Connect.

**LocalResources**  
Innehåller definitioner för lokala lagringsresurser. En resurs för lokal lagring är en reserverad katalog i filsystemet för den virtuella datorn som kör en instans av en roll.

**Import**  
Innehåller definitioner för importerade moduler. Det förra exemplet visar moduler för anslutning till fjärrskrivbord och Azure Connect.

**Start**  
Innehåller uppgifter som körs när rollen startar. Uppgifterna har definierats i en .cmd eller en körbar fil.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
Konfigurationen av inställningarna för din molntjänst fastställs av värdena i den **ServiceConfiguration.cscfg** fil. Du kan ange antalet instanser som du vill distribuera för varje roll i den här filen. Värdena för de konfigurationsinställningar som du definierade i tjänstdefinitionsfilen läggs till i tjänstekonfigurationsfilen. Tumavtryck för av hanteringscertifikat som är associerade med Molntjänsten läggs också till filen. Den [Azure-Tjänstkonfigurationens Schema (.cscfg-filen)](/previous-versions/azure/reference/ee758710(v=azure.100)) innehåller tillåten formatet för en tjänstkonfigurationsfil.

Tjänstkonfigurationsfilen levereras inte med programmet, men har överförts till Azure som en separat fil och används för att konfigurera Molntjänsten. Du kan ladda upp en ny konfigurationsfil för tjänsten utan att omdistribuera din molntjänst. Konfigurationsvärden för Molntjänsten kan ändras när Molntjänsten körs. I följande exempel visas de konfigurationsinställningar som kan definieras för webb- och Worker-roller:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Du kan referera till den [Tjänstkonfigurationens Schema](/previous-versions/azure/reference/ee758710(v=azure.100)) för bättre förstå XML-schema används här, men här är en snabb förklaring av element:

**instanser**  
Konfigurerar antalet instanser för rollen som körs. Om du vill förhindra att din molntjänst potentiellt blir otillgänglig under uppgraderingar, bör du distribuera fler än en instans av webbservergrupper web-roller. Genom att distribuera fler än en instans, du följa riktlinjerna i den [Azure Compute serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/), som garanterar 99,95% extern anslutning för Internet-riktade roller när två eller fler rollinstanser distribueras för en tjänst.

**ConfigurationSettings**  
Konfigurerar inställningar för instanser som körs för en roll. Namnet på den `<Setting>` element måste matcha inställningen definitionerna i tjänstdefinitionsfilen.

**Certifikat**  
Konfigurerar de certifikat som används av tjänsten. Det förra exemplet visar hur du definierar certifikat för fjärråtkomst-modulen. Värdet för den *tumavtryck* attributet måste anges till tumavtrycket för certifikatet som du använder.

<p/>

> [!NOTE]
> Tumavtrycket för certifikatet kan läggas till konfigurationsfilen med hjälp av en textredigerare. Eller värdet kan läggas till på den **certifikat** fliken den **egenskaper** sidan av rollen i Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definiera portar för rollinstanser
Azure kan bara en startpunkt för en webbroll. Vilket innebär att all trafik sker via en IP-adress. Du kan konfigurera dina webbplatser för att dela en port genom att konfigurera värdhuvud för att dirigera begäran till rätt plats. Du kan också konfigurera dina program för att lyssna på välkända portar på IP-adress.

I följande exempel visar konfigurationen för en webbroll med ett webbplatsen och program. Webbplatsen är konfigurerad som standardplatsen för posten på port 80 och webbprogram som är konfigurerade för att ta emot förfrågningar från en rubrik av alternativa värden som kallas ”mail.mysite.cloudapp.net”.

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostheader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Ändra konfigurationen för en roll
Du kan uppdatera konfigurationen för din molntjänst när den körs i Azure, utan att koppla tjänsten. Om du vill ändra konfigurationsinformation du antingen ladda upp en ny konfigurationsfil eller redigera konfigurationsfilen på plats och tillämpa den på din tjänsten som körs. Följande ändringar kan göras i konfigurationen av en tjänst:

* **Ändring av värdena för konfigurationsinställningar**  
  När en konfigurationsinställning ändringar, en rollinstans kan välja att tillämpa ändringen medan instansen är online, eller om du vill återanvända instansen smidigt och tillämpa ändringen när instansen är offline.
* **Ändra tjänst-topologi rollinstanser**  
  Ändringar i nätverkstopologin påverkar inte instanser som körs, utom där en instans tas bort. Alla återstående instanser i allmänhet behöver inte återvinnas; Du kan dock välja att återvinna rollinstanser som svar på en topologiändring.
* **Ändra certifikatets tumavtryck**  
  Du kan bara uppdatera ett certifikat när en rollinstans är offline. Om ett certifikat läggs till, ta bort eller ändra när en rollinstans är online, tar Azure smidigt instansen offline och uppdatera certifikatet som du kan ta den online igen när ändringen har gjorts.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Hantering av ändringar i konfigurationen med körning av tjänsten-händelser
Den [Azure Runtime Library](/previous-versions/azure/reference/mt419365(v=azure.100)) innehåller den [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) namnområde som innehåller klasser för att interagera med Azure-miljön från en roll. Den [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) definierar följande händelser som har skapats före och efter en konfigurationsändring i klassen:

* **[Ändra](/previous-versions/azure/reference/ee758134(v=azure.100)) händelse**  
  Detta inträffar innan konfigurationsändringen tillämpas på en angiven instans av en roll som ger dig möjlighet att ta bort rollinstanserna om det behövs.
* **[Ändra](/previous-versions/azure/reference/ee758129(v=azure.100)) händelse**  
  Inträffar när konfigurationsändringen används på en angiven instans av en roll.

> [!NOTE]
> Eftersom certifikatändringar tar alltid instanser av en roll som är offline, öka de inte RoleEnvironment.Changing eller RoleEnvironment.Changed händelser.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Om du vill distribuera ett program som en molntjänst i Azure måste du först paketera programmet i rätt format. Du kan använda den **CSPack** kommandoradsverktyget (installeras med den [Azure SDK](https://azure.microsoft.com/downloads/)) att skapa paketfilen som ett alternativ till Visual Studio.

**CSPack** används innehållet i tjänstdefinitionsfilen och konfigurationsfilen för tjänsten för att definiera innehållet i paketet. **CSPack** genererar en programpaketfil (.cspkg) som du kan överföra till Azure med hjälp av den [Azure-portalen](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Som standard heter paketet `[ServiceDefinitionFileName].cspkg`, men du kan ange ett annat namn med hjälp av den `/out` möjlighet att **CSPack**.

**CSPack** finns i  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (på windows) är tillgänglig genom att köra den **Kommandotolken för Microsoft Azure** genväg som installeras med SDK: N.  
> 
> Kör programmet CSPack.exe ensamt finns i dokumentationen om alla möjliga växlar och kommandon.
> 
> 

<p />

> [!TIP]
> Kör din molntjänst lokalt i den **Microsoft Azure Compute Emulator**, använda den **/copyonly** alternativet. Det här alternativet kopierar de binära filerna för programmet till en katalog layout där de kan köras i compute-emulatorn.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Exempelkommando för att paketera en molntjänst
I följande exempel skapas ett programpaket som innehåller information för en webbroll. Kommandot anger tjänstdefinitionsfilen att använda den katalog där binära filer kan hittas, och namnet på paketfilen.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Om programmet innehåller både en webbroll och en arbetsroll, används följande kommando:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Där variablerna definieras enligt följande:

| Variabel | Värde |
| --- | --- |
| \[DirectoryName\] |Underkatalog under projektets rotkatalog som innehåller den .csdef-filen för Azure-projekt. |
| \[ServiceDefinition\] |Namnet på tjänstdefinitionsfilen. Som standard heter den här filen ServiceDefinition.csdef. |
| \[OutputFileName\] |Namnet på den genererade paketfilen. Detta är normalt inställt på namnet på programmet. Om inget filnamn har angetts, skapas programpaketet som \[ApplicationName\].cspkg. |
| \[RoleName\] |Namnet på rollen som definierats i tjänstdefinitionsfilen. |
| \[RoleBinariesDirectory] |Platsen för binärfilerna för rollen. |
| \[VirtualPath\] |Fysiska kataloger för varje virtuell sökväg som definierats i avsnittet platser i tjänstdefinitionen. |
| \[PhysicalPath\] |Innehållet för varje virtuell sökväg som definierats i platsnoden i tjänstdefinitionen fysiska kataloger. |
| \[RoleAssemblyName\] |Namnet på den binära filen för rollen. |

## <a name="next-steps"></a>Nästa steg
Jag skapar ett molntjänstpaket och jag vill...

* [Konfigurera Fjärrskrivbord för en molntjänstinstans][remotedesktop]
* [Distribuera en Cloud Service-projekt][deploy]

Jag använder Visual Studio och jag vill...

* [Skapa en ny molntjänst][vs_create]
* [Konfigurera om en befintlig molntjänst][vs_reconfigure]
* [Distribuera en Cloud Service-projekt][vs_deploy]
* [Konfigurera Fjärrskrivbord för en molntjänstinstans][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
