---
title: Molntjänster Roll config XPath lathund | Microsoft-dokument
description: De olika XPath-inställningar som du kan använda i molntjänstrollen config för att exponera inställningar som en miljövariabel.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 380b0be4e4e4b19d16cb611b0b472294339f2199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386093"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Visa rollkonfigurationsinställningar som en miljövariabel med XPath
I definitionsfilen för molntjänstarbetare eller webbrolltjänst kan du visa körningskonfigurationsvärden som miljövariabler. Följande XPath-värden stöds (som motsvarar API-värden).

Dessa XPath-värden är också tillgängliga via [Microsoft.WindowsAzure.ServiceRuntime-biblioteket.](/previous-versions/azure/reference/ee773173(v=azure.100)) 

## <a name="app-running-in-emulator"></a>App som körs i emulator
Anger att appen körs i emulatorn.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Kod |var x = RollMiljö.IsEmulated; |

## <a name="deployment-id"></a>Distributions-ID
Hämtar distributions-ID:et för instansen.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/Deployment/@id" |
| Kod |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Roll-ID
Hämtar det aktuella roll-ID:et för instansen.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Kod |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Uppdatera domän
Hämtar uppdateringsdomänen för instansen.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kod |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Feldomän
Hämtar feldomänen för instansen.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kod |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Rollnamn
Hämtar rollnamnet för instanserna.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Kod |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Konfigurationsinställning
Hämtar värdet för den angivna konfigurationsinställningen.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Kod |var-inställning = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Sökväg för lokal lagring
Hämtar den lokala lagringssökvägen för instansen.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Kod |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Lokal lagringsstorlek
Hämtar storleken på det lokala lagringsutrymmet för instansen.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Kod |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Slutpunktsprotokoll
Hämtar slutpunktsprotokollet för instansen.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Kod |varprot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. protokollet. |

## <a name="endpoint-ip"></a>IP-adress för slutpunkt
Hämtar den angivna slutpunktens IP-adress.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Kod |var-adress = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Slutpunktsport
Hämtar slutpunktsporten för instansen.

| Typ | Exempel |
| --- | --- |
| Xpath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Kod |varport = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Exempel
Här är ett exempel på en arbetarroll som skapar `TestIsEmulated` en startuppgift med en miljövariabel med namnet [ @emulated xpath-värde](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Nästa steg
Läs mer om filen [ServiceConfiguration.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)

Skapa ett [ServicePackage.cspkg-paket.](cloud-services-model-and-package.md#servicepackagecspkg)

Aktivera [fjärrskrivbord](cloud-services-role-enable-remote-desktop-new-portal.md) för en roll.




