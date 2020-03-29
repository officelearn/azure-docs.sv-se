---
title: Ange miljövariabler för tjänster
description: Visar hur du använder miljövariabler för program i Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614323"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Så här anger du miljövariabler för tjänster i Service Fabric

Den här artikeln visar hur du anger miljövariabler för en tjänst eller behållare i Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Förfarande för att ange miljövariabler för tjänster

I det här exemplet anger du en miljövariabel för en behållare. Artikeln förutsätter att du redan har ett program- och tjänstmanifest.

1. Öppna filen ServiceManifest.xml.
2. Lägg `CodePackage` till ett nytt `EnvironmentVariables` element `EnvironmentVariable` och ett element för varje miljövariabel i elementet.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Miljövariabler kan åsidosättas i programmanifestet.

3. Om du vill åsidosätta miljövariablerna `EnvironmentOverrides` i programmanifestet använder du elementet.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Ange miljövariabler dynamiskt med Docker Compose

Service Fabric stöder möjligheten att [använda Docker Compose for Deployment](service-fabric-docker-compose.md#supported-compose-directives). Skriv filer kan källmiljövariabler från skalet. Detta kan användas för att ersätta önskade miljövärden dynamiskt:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Nästa steg
Mer information om några av de grundläggande begrepp som beskrivs i den här artikeln finns i [artiklar om Hantera program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra funktioner för apphantering som är tillgängliga i Visual Studio finns i [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).
