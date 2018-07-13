---
title: Cloud Services-roll config XPath-facit | Microsoft Docs
description: Olika XPath-inställningar du kan använda i molnkonfigurationen service rollen för att exponera inställningar som en miljövariabel.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 2db63be6c6997840f7409a3ca79f1845f30e4ceb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008067"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Exponera konfigurationsinställningarna för rollen som en miljövariabel med XPath
I cloud service worker eller tjänstdefinitionsfilen för web-roll, kan du exponera runtime konfigurationsvärden som miljövariabler. Följande XPath-värden stöds (som motsvarar API-värden).

Dessa XPath-värden är också tillgängliga via den [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) biblioteket. 

## <a name="app-running-in-emulator"></a>App som körs i emulatorn
Anger att appen körs i emulatorn.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/RoleEnvironment/Deployment/@emulated” |
| Kod |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>Distributions-ID
Hämtar distributions-ID för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/RoleEnvironment/Deployment/@id” |
| Kod |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Roll-ID
Hämtar den aktuella roll-ID för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/RoleEnvironment/CurrentInstance/@id” |
| Kod |var-id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Uppdatera domän
Hämtar uppdateringsdomän på-instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/RoleEnvironment/CurrentInstance/@updateDomain” |
| Kod |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Feldomän
Hämtar feldomänen för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/RoleEnvironment/CurrentInstance/@faultDomain” |
| Kod |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Rollnamn
Hämtar namnet på för instanser.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/RoleEnvironment/CurrentInstance/@roleName” |
| Kod |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Konfigurationsinställningen
Hämtar värdet för den angivna Konfigurationsinställningen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/ RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting [@name= 'Setting1']/@value” |
| Kod |var inställningen = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Sökväg för lokal lagring
Hämtar sökvägen till lokal lagring för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/ RoleEnvironment/CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@path” |
| Kod |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Lokal lagringsstorlek
Hämtar storleken på den lokala lagringen för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/ RoleEnvironment/CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@sizeInMB” |
| Kod |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Slutpunkt-protokollet
Hämtar endpoint-protokollet för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/ RoleEnvironment eller CurrentInstance/slutpunkter/slutpunktens [@name= 'slutpunkt 1']/@protocol” |
| Kod |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1”]. Protokoll. |

## <a name="endpoint-ip"></a>Slutpunkt IP
Hämtar den angivna slutpunkten IP-adress.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/ RoleEnvironment eller CurrentInstance/slutpunkter/slutpunktens [@name= 'slutpunkt 1']/@address” |
| Kod |var adress = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1”]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Slutpunktsport
Hämtar slutpunktsport för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/ RoleEnvironment eller CurrentInstance/slutpunkter/slutpunktens [@name= 'slutpunkt 1']/@port” |
| Kod |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1”]. IPEndpoint.Port; |

## <a name="example"></a>Exempel
Här är ett exempel på en arbetsroll som skapar en startåtgärd med miljövariabeln `TestIsEmulated` inställd på den [ @emulated xpath-värdet](#app-running-in-emulator). 

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
Läs mer om den [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fil.

Skapa en [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) paketet.

Aktivera [fjärrskrivbord](cloud-services-role-enable-remote-desktop-new-portal.md) för en roll.

