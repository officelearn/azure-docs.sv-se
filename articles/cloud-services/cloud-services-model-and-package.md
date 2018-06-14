---
title: Vad är en tjänst i molnet och paketet | Microsoft Docs
description: Beskriver molnet tjänstmodellen (.csdef, .cscfg) och paketet (.cspkg) i Azure
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 0589f2efeaaafc35bcb9d869c391a0533fe6e502
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
ms.locfileid: "29876567"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Vad är Cloud Service-modell och hur jag paketera den?
En molnbaserad tjänst skapas från tre komponenter, tjänstdefinitionen *(.csdef)*, service-config *(.cscfg)*, och inget tjänstepaket *(.cspkg)*. Både den **ServiceDefinition.csdef** och **ServiceConfig.cscfg** filer är XML-baserade och beskriver strukturen för Molntjänsten och hur den är konfigurerad, kallade modellen. Den **ServicePackage.cspkg** är en zip-fil som skapas från den **ServiceDefinition.csdef** och bland annat innehåller alla nödvändiga binary-baserade beroenden. Azure skapar en molnbaserad tjänst från både den **ServicePackage.cspkg** och **ServiceConfig.cscfg**.

När Molntjänsten körs i Azure kan du konfigurera det via den **ServiceConfig.cscfg** filen, men du kan inte ändra definitionen.

## <a name="what-would-you-like-to-know-more-about"></a>Vad vill du veta mer om?
* Jag vill veta mer om den [ServiceDefinition.csdef](#csdef) och [ServiceConfig.cscfg](#cscfg) filer.
* Jag redan vet att ge mig [några exempel](#next-steps) på kan jag konfigurera.
* Jag vill skapa den [ServicePackage.cspkg](#cspkg).
* Jag använder Visual Studio och vill...
  * [Skapa en molntjänst][vs_create]
  * [Konfigurera om en befintlig molntjänst][vs_reconfigure]
  * [Distribuera en Cloud Service-projekt][vs_deploy]
  * [Fjärrskrivbord till en tjänstinstans för molnet][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
Den **ServiceDefinition.csdef** filen anger de inställningar som används av Azure för att konfigurera en molntjänst. Den [Azure Service Definition schemat (.csdef-fil)](https://msdn.microsoft.com/library/azure/ee758711.aspx) ger tillåtna format för en tjänstdefinitionsfilen. I följande exempel visas de inställningar som kan definieras för webb- och arbetsroller roller:

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

Du kan referera till den [Service Definition schemat](https://msdn.microsoft.com/library/azure/ee758711.aspx) för en bättre förståelse av XML-schemat som används här, men här är en snabb förklaring av vissa element:

**webbplatser**  
Innehåller definitioner för webbplatser eller web program som finns i IIS7.

**InputEndpoints**  
Innehåller definitioner för slutpunkter som används för att kontakta Molntjänsten.

**InternalEndpoints**  
Innehåller definitioner för slutpunkter som används av rollinstanser för att kommunicera med varandra.

**ConfigurationSettings**  
Innehåller inställningsdefinitioner för funktioner för en viss roll.

**Certifikat**  
Innehåller definitioner för certifikat som behövs för en roll. Det föregående exemplet visar ett certifikat som används för konfigurationen av Azure Connect.

**LocalResources**  
Innehåller definitioner för lokala lagringsresurser. En resurs för lokal lagring är en reserverad katalog på filsystemet på den virtuella datorn som kör en instans av en roll.

**Import**  
Innehåller definitioner för importerade moduler. Det föregående exemplet visar moduler för anslutning till fjärrskrivbord och Azure Connect.

**Start**  
Innehåller uppgifter som körs när rollen startar. Aktiviteter har definierats i en .cmd eller en körbar fil.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
Konfigurationen av inställningarna för din molntjänst bestäms av värdena i den **ServiceConfiguration.cscfg** fil. Du kan ange antalet instanser som du vill distribuera för varje roll i den här filen. Värden för de konfigurationsinställningar som du har definierats i tjänstdefinitionsfilen läggs till i tjänstekonfigurationsfilen. Tumavtryck för av hanteringscertifikat som är associerade med Molntjänsten läggs också till filen. Den [Konfigurationsschemat för Azure-tjänsten (.cscfg-filen)](https://msdn.microsoft.com/library/azure/ee758710.aspx) ger tillåtna format för en konfigurationsfil för tjänsten.

Tjänstkonfigurationsfilen levereras inte med programmet, men har överförts till Azure som en separat fil och används för att konfigurera Molntjänsten. Du kan ladda upp en ny konfigurationsfil för tjänsten utan att omdistribuera din tjänst i molnet. Konfigurationsvärdena för Molntjänsten kan ändras när Molntjänsten körs. I följande exempel visas de konfigurationsinställningar som kan definieras för webb- och arbetsroller roller:

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

Du kan referera till den [Service Konfigurationsschemat](https://msdn.microsoft.com/library/azure/ee758710.aspx) för bättre förstå XML-schemat som används här, men här är en snabb förklaring av element:

**Instanser**  
Konfigurerar antalet instanser för rollen som körs. Förhindra att din molntjänst potentiellt blir otillgänglig under uppgraderingar, bör du distribuera mer än en instans av web-riktade-roller. Genom att distribuera mer än en instans du uppfyller riktlinjerna i den [Azure Compute serviceavtalet (SLA)](http://azure.microsoft.com/support/legal/sla/), vilket garanterar 99,95% extern anslutning för Internet-riktade roller när två eller flera rollinstanser distribueras för en tjänst.

**ConfigurationSettings**  
Konfigurerar inställningar för instanser för en roll. Namnet på den `<Setting>` element måste matcha inställningsdefinitioner i tjänstdefinitionsfilen.

**Certifikat**  
Konfigurerar de certifikat som används av tjänsten. Det föregående exemplet visar hur du definierar certifikat för fjärråtkomst-modulen. Värdet för den *tumavtrycket* attributet måste anges till tumavtrycket för certifikatet som ska användas.

<p/>

> [!NOTE]
> Tumavtrycket för certifikatet kan läggas till konfigurationsfilen med hjälp av en textredigerare. Eller värdet kan läggas till på den **certifikat** för den **egenskaper** sidan av rollen i Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definiera portar för rollinstanser
Azure kan bara en startpunkt till en webbroll. Vilket innebär att all trafik sker via en IP-adress. Du kan konfigurera din webbplatser för att dela en port genom att konfigurera värdhuvud för att dirigera om begäran till rätt plats. Du kan också konfigurera dina program för att lyssna på välkända portar på IP-adress.

I följande exempel visar konfiguration för en webbroll med ett webbplatsen och program. Webbplatsen är konfigurerad som standard posten på port 80 och webbprogram har konfigurerats för att ta emot förfrågningar från ett huvud för alternativa värden som kallas ”mail.mysite.cloudapp.net”.

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
Du kan uppdatera konfigurationen av din molntjänst när den körs i Azure, utan att koppla från tjänsten. Om du vill ändra konfigurationsinformation du antingen ladda upp en ny konfigurationsfil, eller redigera konfigurationsfilen på plats och gäller för din tjänst som körs. Följande ändringar kan göras i konfigurationen av en tjänst:

* **Ändra värdena för konfigurationsinställningar**  
  När en konfigurationsinställning ändras, en rollinstans kan välja att tillämpa ändringen medan instansen är online, eller om du vill återvinna instansen avslutas och tillämpa ändringen när instansen är offline.
* **Ändra service topologin för rollinstanser**  
  Ändringar i nätverkstopologin påverkar inte instanser, utom där en instans tas bort. Alla återstående instanser normalt behöver inte återvinnas; Du kan dock välja att återvinna rollinstanser i topologin ändras.
* **Ändra certifikatets tumavtryck**  
  Du kan bara uppdatera certifikat när en rollinstans är offline. Om ett certifikat läggs till, tas bort eller ändras medan en rollinstans är online, tar Azure smidigt instansen offline och uppdatera certifikatet som du kan ta den online igen efter att ändringen har gjorts.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Konfigurationsändringar för hantering med körning av tjänsten-händelser
Den [Azure Runtime Library](https://msdn.microsoft.com/library/azure/mt419365.aspx) innehåller den [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) namnområdet, som innehåller klasser för att interagera med Azure-miljön från en roll. Den [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) definierar följande händelser som aktiveras före och efter en konfigurationsändring i klassen:

* **[Ändra](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) händelse**  
  Detta inträffar innan konfigurationsändringen används till en viss instans av en roll som ger dig möjlighet att ta bort rollinstanserna om det behövs.
* **[Ändra](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx) händelse**  
  Inträffar när konfigurationsändringen tillämpas för en viss instans av en roll.

> [!NOTE]
> Eftersom certifikatet ändringar ta alltid instanser av en roll som är offline, öka de inte RoleEnvironment.Changing eller RoleEnvironment.Changed händelser.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Du måste först paketera programmet i rätt format för att distribuera ett program som en tjänst i molnet i Azure. Du kan använda den **CSPack** kommandoradsverktyget (installeras med den [Azure SDK](https://azure.microsoft.com/downloads/)) att skapa paketfilen som ett alternativ till Visual Studio.

**CSPack** använder innehållet i tjänstdefinitionsfilen och konfigurationsfilen för tjänsten för att definiera innehållet i paketet. **CSPack** genererar ett programpaketfil (.cspkg) som du kan överföra till Azure med hjälp av den [Azure-portalen](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Som standard heter paketet `[ServiceDefinitionFileName].cspkg`, men du kan ange ett annat namn med hjälp av den `/out` möjlighet att **CSPack**.

**CSPack** finns på  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (på windows) är tillgängliga genom att köra den **Kommandotolken för Microsoft Azure** genväg som installeras med SDK.  
> 
> Kör programmet CSPack.exe ensamt finns dokumentationen om alla möjliga växlar och kommandon.
> 
> 

<p />

> [!TIP]
> Kör Molntjänsten lokalt i den **Microsoft Azure Compute Emulator**, använda den **/copyonly** alternativet. Det här alternativet kopierar de binära filerna för programmet till en katalog layout där de kan köras i beräkningsemulatorn.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Exempel-kommando för att paketera en tjänst i molnet
I följande exempel skapas ett programpaket som innehåller information för en webbroll. Kommandot anger tjänstdefinitionsfilen att använda katalogen där binära filer kan hittas, och namnet på paketfilen.

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

Där definieras variablerna enligt följande:

| Variabel | Värde |
| --- | --- |
| \[DirectoryName\] |Underkatalog under projektets rotkatalog som innehåller .csdef-filen för Azure-projekt. |
| \[ServiceDefinition\] |Namnet på tjänstdefinitionsfilen. Som standard är den här filen namnet ServiceDefinition.csdef. |
| \[OutputFileName\] |Namnet på filen i paketet. Normalt anges till namnet på programmet. Om inget filnamn har angetts programpaketet skapas som \[ApplicationName\].cspkg. |
| \[RoleName\] |Namnet på rollen som definierats i tjänstdefinitionsfilen. |
| \[RoleBinariesDirectory] |Platsen för de binära filerna för rollen. |
| \[VirtualPath\] |Fysiska kataloger för varje virtuell sökväg som definierats i avsnittet platser i tjänstdefinitionen. |
| \[PhysicalPath\] |Innehållet för varje virtuell sökväg som definierats i platsnoden i tjänstdefinitionen fysiska kataloger. |
| \[RoleAssemblyName\] |Namnet på den binära fil för rollen. |

## <a name="next-steps"></a>Nästa steg
Jag skapar en cloud service-paketet och vill...

* [Konfigurera Fjärrskrivbord för en tjänstinstans för molnet][remotedesktop]
* [Distribuera en Cloud Service-projekt][deploy]

Jag använder Visual Studio och vill...

* [Skapa en ny molntjänst][vs_create]
* [Konfigurera om en befintlig molntjänst][vs_reconfigure]
* [Distribuera en Cloud Service-projekt][vs_deploy]
* [Konfigurera Fjärrskrivbord för en tjänstinstans för molnet][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
