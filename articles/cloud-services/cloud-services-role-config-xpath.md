---
title: Cloud Services-rollen config XPath fusklapp | Microsoft Docs
description: Olika XPath-inställningar du kan använda i cloud service rollen config för att exponera inställningar som en miljövariabel.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: e71adbca34390bda3a7d4067742ffb3a28201449
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
ms.locfileid: "24860379"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Exponera konfigurationsinställningar för rollen som en miljövariabel med XPath
I cloud service worker eller web rollen tjänstdefinitionsfilen exponera du runtime konfigurationsvärden som miljövariabler. Följande XPath-värden stöds (som motsvarar API värden).

Värdena XPath finns också tillgängliga via den [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) bibliotek. 

## <a name="app-running-in-emulator"></a>Appen körs i emulatorn
Anger att programmet körs i emulatorn.

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
| Kod |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Uppdatera domänen
Hämtar uppdateringsdomän för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/RoleEnvironment/CurrentInstance/@updateDomain” |
| Kod |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>feldomän
Hämtar feldomänen för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/RoleEnvironment/CurrentInstance/@faultDomain” |
| Kod |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Rollnamn
Hämtar rollnamnet av instanserna.

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

## <a name="local-storage-path"></a>Sökvägen till lokal lagring
Hämtar lokal lagringssökvägen för instansen.

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

## <a name="endpoint-protocol"></a>Protokoll för slutpunkten
Hämtar endpoint-protokollet för instansen.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/ RoleEnvironment eller CurrentInstance/slutpunkter/slutpunktens [@name= 'slutpunkt 1']/@protocol” |
| Kod |var skydd = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1”]. Protokollet. |

## <a name="endpoint-ip"></a>Slutpunkten IP
Hämtar den angivna slutpunkten IP-adress.

| Typ | Exempel |
| --- | --- |
| XPath |XPath = ”/ RoleEnvironment eller CurrentInstance/slutpunkter/slutpunktens [@name= 'slutpunkt 1']/@address” |
| Kod |var adress = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1”]. IPEndpoint.Address |

## <a name="endpoint-port"></a>port för slutpunkt
Hämtar endpoint-port för instansen.

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
Lär dig mer om den [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fil.

Skapa en [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) paketet.

Aktivera [fjärrskrivbord](cloud-services-role-enable-remote-desktop-new-portal.md) för en roll.

