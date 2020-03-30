---
title: Vad är en cloud service-modell och -paket | Microsoft-dokument
description: Beskriver molntjänstmodellen (.csdef, .cscfg) och paketet (.cspkg) i Azure
services: cloud-services
author: tanmaygore
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 32603f4ab33e020245861e5dc66d2ade545fa627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247493"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Vad är Cloud Service-modellen och hur paketerar jag den?
En molntjänst skapas från tre komponenter, tjänstdefinitionen *(.csdef),* tjänsten config *(.cscfg)* och ett servicepaket *(.cspkg)*. Både **filerna ServiceDefinition.csdef** och **ServiceConfig.cscfg** är XML-baserade och beskriver molntjänstens struktur och hur den konfigureras. gemensamt kallad modellen. **ServicePackage.cspkg** är en zip-fil som genereras från **ServiceDefinition.csdef** och innehåller bland annat alla obligatoriska binärbaserade beroenden. Azure skapar en molntjänst från både **ServicePackage.cspkg** och **ServiceConfig.cscfg**.

När molntjänsten körs i Azure kan du konfigurera om den via **filen ServiceConfig.cscfg,** men du kan inte ändra definitionen.

## <a name="what-would-you-like-to-know-more-about"></a>Vad vill du veta mer om?
* Jag vill veta mer om [ServiceDefinition.csdef](#csdef) och [ServiceConfig.cscfg](#cscfg) filer.
* Jag vet redan om det, ge mig [några exempel](#next-steps) på vad jag kan konfigurera.
* Jag vill skapa [ServicePackage.cspkg](#cspkg).
* Jag använder Visual Studio och jag vill ...
  * [Skapa en molntjänst][vs_create]
  * [Konfigurera om en befintlig molntjänst][vs_reconfigure]
  * [Distribuera ett molntjänstprojekt][vs_deploy]
  * [Fjärrskrivbord till en molntjänstinstans][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**Filen ServiceDefinition.csdef** anger de inställningar som används av Azure för att konfigurera en molntjänst. [Azure Service Definition Schema (.csdef File)](/previous-versions/azure/reference/ee758711(v=azure.100)) tillhandahåller det tillåtna formatet för en tjänstdefinitionsfil. I följande exempel visas de inställningar som kan definieras för webb- och arbetarrollerna:

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

Du kan hänvisa till [servicedefinitionsschemat](/previous-versions/azure/reference/ee758711(v=azure.100)) för en bättre förståelse av XML-schemat som används här, men här är en snabb förklaring av några av elementen:

**Webbplatser**  
Innehåller definitioner för webbplatser eller webbprogram som finns i IIS7.

**InputEndpoints**  
Innehåller definitioner för slutpunkter som används för att kontakta molntjänsten.

**InternalEndpoints**  
Innehåller definitioner för slutpunkter som används av rollinstanser för att kommunicera med varandra.

**Konfigurationsinställningar**  
Innehåller inställningsdefinitioner för funktioner i en viss roll.

**Certifikat**  
Innehåller definitionerna för certifikat som behövs för en roll. I det föregående kodexemplet visas ett certifikat som används för konfigurationen av Azure Connect.

**Lokala resurser**  
Innehåller definitionerna för lokala lagringsresurser. En lokal lagringsresurs är en reserverad katalog i filsystemet för den virtuella datorn där en instans av en roll körs.

**Import**  
Innehåller definitionerna för importerade moduler. I det föregående kodexemplet visas modulerna för anslutning till fjärrskrivbord och Azure Connect.

**Start**  
Innehåller aktiviteter som körs när rollen startar. Aktiviteterna definieras i en CMD- eller körbar fil.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
Konfigurationen av inställningarna för molntjänsten bestäms av värdena i filen **ServiceConfiguration.cscfg.** Du anger antalet instanser som du vill distribuera för varje roll i den här filen. Värdena för de konfigurationsinställningar som du definierade i tjänstdefinitionsfilen läggs till i tjänstkonfigurationsfilen. Tumavtrycken för alla hanteringscertifikat som är associerade med molntjänsten läggs också till i filen. [Azure Service Configuration Schema (.cscfg File)](/previous-versions/azure/reference/ee758710(v=azure.100)) tillhandahåller det tillåtna formatet för en tjänstkonfigurationsfil.

Tjänstkonfigurationsfilen är inte paketerad med programmet, men överförs till Azure som en separat fil och används för att konfigurera molntjänsten. Du kan ladda upp en ny tjänstkonfigurationsfil utan att distribuera om molntjänsten. Konfigurationsvärdena för molntjänsten kan ändras medan molntjänsten körs. I följande exempel visas de konfigurationsinställningar som kan definieras för webb- och arbetarrollerna:

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

Du kan hänvisa till [servicekonfigurationsschemat](/previous-versions/azure/reference/ee758710(v=azure.100)) för att bättre förstå XML-schemat som används här, men här är en snabb förklaring av elementen:

**Instanser**  
Konfigurerar antalet instanser som körs för rollen. För att förhindra att molntjänsten blir otillgänglig under uppgraderingar rekommenderar vi att du distribuerar mer än en instans av dina webbinriktade roller. Genom att distribuera mer än en instans följer du riktlinjerna i [Azure Compute Service Level Agreement (SLA),](https://azure.microsoft.com/support/legal/sla/)som garanterar 99,95 % extern anslutning för Internet-kritiska roller när två eller flera rollinstanser distribueras för en tjänst.

**Konfigurationsinställningar**  
Konfigurerar inställningarna för gående instanser för en roll. Namnet på `<Setting>` elementen måste matcha inställningsdefinitionerna i tjänstdefinitionsfilen.

**Certifikat**  
Konfigurerar de certifikat som används av tjänsten. Det föregående kodexemplet visar hur du definierar certifikatet för RemoteAccess-modulen. Värdet för *tumavtrycket* måste anges till tumavtrycket för det certifikat som ska användas.

<p/>

> [!NOTE]
> Tumavtrycket för certifikatet kan läggas till i konfigurationsfilen med hjälp av en textredigerare. Eller så kan värdet läggas till på fliken **Certifikat** på sidan **Egenskaper** för rollen i Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definiera portar för rollinstanser
Azure tillåter endast en startpunkt till en webbroll. Vilket innebär att all trafik sker via en IP-adress. Du kan konfigurera dina webbplatser så att de delar en port genom att konfigurera värdhuvudet för att dirigera begäran till rätt plats. Du kan också konfigurera dina program för att lyssna på välkända portar på IP-adressen.

Följande exempel visar konfigurationen för en webbroll med en webbplats och ett webbprogram. Webbplatsen är konfigurerad som standardpostplats på port 80 och webbprogrammen är konfigurerade för att ta emot begäranden från ett alternativt värdhuvud som kallas "mail.mysite.cloudapp.net".

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
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Ändra konfigurationen av en roll
Du kan uppdatera konfigurationen av din molntjänst medan den körs i Azure, utan att tjänsten tas ur. Om du vill ändra konfigurationsinformation kan du antingen ladda upp en ny konfigurationsfil eller redigera konfigurationsfilen på plats och tillämpa den på tjänsten som körs. Följande ändringar kan göras i konfigurationen av en tjänst:

* **Ändra värden för konfigurationsinställningar**  
  När en konfigurationsinställning ändras kan en rollinstans välja att tillämpa ändringen medan instansen är online, eller att återanvända instansen på ett smidigt sätt och tillämpa ändringen medan instansen är offline.
* **Ändra tjänsttopologin för rollinstanser**  
  Topologiändringar påverkar inte löpinstanser, förutom när en instans tas bort. Alla återstående instanser behöver i allmänhet inte återvinnas. Du kan dock välja att återvinna rollinstanser som svar på en topologiändring.
* **Ändra certifikatets tumavtryck**  
  Du kan bara uppdatera ett certifikat när en rollinstans är offline. Om ett certifikat läggs till, tas bort eller ändras medan en rollinstans är online, tar Azure smidigt instansen offline för att uppdatera certifikatet och ansluta det igen när ändringen är klar.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Hantera konfigurationsändringar med Service Runtime-händelser
[Azure Runtime-biblioteket](/previous-versions/azure/reference/mt419365(v=azure.100)) innehåller namnområdet [Microsoft.WindowsAzure.ServiceRuntime,](/previous-versions/azure/reference/ee741722(v=azure.100)) som tillhandahåller klasser för interaktion med Azure-miljön från en roll. Klassen [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) definierar följande händelser som utlöses före och efter en konfigurationsändring:

* **[Ändra](/previous-versions/azure/reference/ee758134(v=azure.100)) händelse**  
  Detta inträffar innan konfigurationsändringen tillämpas på en angiven instans av en roll som ger dig en chans att ta bort rollinstanserna om det behövs.
* **[Ändrad](/previous-versions/azure/reference/ee758129(v=azure.100)) händelse**  
  Inträffar när konfigurationsändringen tillämpas på en angiven instans av en roll.

> [!NOTE]
> Eftersom certifikatändringar alltid tar instanserna av en roll offline, höjer de inte Rollmiljö.Changing eller RoleEnvironment.Changed events.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
> [!NOTE]
> Den maximala paketstorleken som kan distribueras är 600 MB

Om du vill distribuera ett program som en molntjänst i Azure måste du först paketera programmet i lämpligt format. Du kan använda kommandoradsverktyget **CSPack** (installerat med [Azure SDK)](https://azure.microsoft.com/downloads/)för att skapa paketfilen som ett alternativ till Visual Studio.

**CSPack** använder innehållet i tjänstdefinitionsfilen och tjänstkonfigurationsfilen för att definiera innehållet i paketet. **CSPack** genererar en programpaketfil (.cspkg) som du kan ladda upp till Azure med hjälp av [Azure-portalen](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Som standard heter `[ServiceDefinitionFileName].cspkg`paketet , men du kan ange `/out` ett annat namn med alternativet **CSPack**.

**CSPack** finns på  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (i windows) är tillgängligt genom att köra genvägen **microsoft Azure Command Prompt** som är installerad med SDK.  
> 
> Kör CSPack.exe-programmet av sig själv för att se dokumentation om alla möjliga växlar och kommandon.
> 
> 

<p />

> [!TIP]
> Kör din molntjänst lokalt i **Microsoft Azure Compute Emulator**, använd alternativet **/copyonly.** Det här alternativet kopierar de binära filerna för programmet till en kataloglayout som de kan köras från i beräkningsemulatorn.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Exempelkommando för att paketera en molntjänst
I följande exempel skapas ett programpaket som innehåller information för en webbroll. Kommandot anger vilken tjänstdefinitionsfil som ska användas, katalogen där binära filer finns och namnet på paketfilen.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Om programmet innehåller både en webbroll och en arbetsroll används följande kommando:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Om variablerna definieras på följande sätt:

| Variabel | Värde |
| --- | --- |
| \[Katalognamn\] |Underkatalogen under rotprojektkatalogen som innehåller CSDEF-filen i Azure-projektet. |
| \[ServiceDefinition\] |Namnet på tjänstdefinitionsfilen. Som standard heter den här filen ServiceDefinition.csdef. |
| \[OutputFileName\] |Namnet på den genererade paketfilen. Vanligtvis är detta inställt på namnet på programmet. Om inget filnamn anges skapas programpaketet \[som\]ApplicationName .cspkg. |
| \[RoleName\] |Namnet på rollen enligt definitionen i tjänstdefinitionsfilen. |
| \[RoleBinariesDirectory] |Platsen för de binära filerna för rollen. |
| \[VirtualPath (virtualpath)\] |De fysiska katalogerna för varje virtuell sökväg som definieras i avsnittet Platser i tjänstdefinitionen. |
| \[FysiskSökväg\] |De fysiska katalogerna för innehållet för varje virtuell sökväg som definierats i platsnoden i tjänstdefinitionen. |
| \[RollAssemblyName\] |Namnet på den binära filen för rollen. |

## <a name="next-steps"></a>Nästa steg
Jag skapar ett molntjänstpaket och vill...

* [Konfigurera fjärrskrivbord för en molntjänstinstans][remotedesktop]
* [Distribuera ett molntjänstprojekt][deploy]

Jag använder Visual Studio och jag vill ...

* [Skapa en ny molntjänst][vs_create]
* [Konfigurera om en befintlig molntjänst][vs_reconfigure]
* [Distribuera ett molntjänstprojekt][vs_deploy]
* [Konfigurera fjärrskrivbord för en molntjänstinstans][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md



