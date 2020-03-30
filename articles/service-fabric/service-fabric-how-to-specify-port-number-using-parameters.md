---
title: Ange portnummer för en tjänst med hjälp av parametrar
description: Visar hur du använder parametrar för att ange porten för ett program i Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609867"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Så här anger du portnumret för en tjänst med hjälp av parametrar i Service Fabric

Den här artikeln visar hur du anger portnumret för en tjänst med hjälp av parametrar i Service Fabric med Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Förfarande för att ange portnumret för en tjänst med hjälp av parametrar

I det här exemplet anger du portnumret för ditt asp.net grundläggande webb-API med hjälp av en parameter.

1. Öppna Visual Studio och skapa ett nytt Service Fabric-program.
1. Välj den tillståndslösa mallen ASP.NET Core.
1. Välj Webb-API.
1. Öppna filen ServiceManifest.xml.
1. Observera namnet på den slutpunkt som angetts för tjänsten. Standardvärdet är `ServiceEndpoint`.
1. Öppna filen ApplicationManifest.xml
1. Lägg `ServiceManifestImport` till ett nytt `RessourceOverrides` element med en referens till slutpunkten i filen ServiceManifest.xml i elementet.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. I `Endpoint` elementet kan du nu åsidosätta alla attribut med hjälp av en parameter. I det här `Port` exemplet anger och ställer du in det på ett parameternamn med hjälp av hakparenteser, till exempel`[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Fortfarande i filen ApplicationManifest.xml anger du sedan `Parameters` parametern i elementet

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. Och definiera en`DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Öppna mappen ApplicationParameters `Cloud.xml` och filen
1. Om du vill ange en annan port som ska användas vid publicering till ett fjärrkluster lägger du till parametern med portnumret i den här filen.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

När du publicerar ditt program från Visual Studio med publiceringsprofilen Cloud.xml är tjänsten konfigurerad för att använda port 80. Om du distribuerar programmet utan att ange parametern MyWebAPI_PortNumber använder tjänsten port 8080.

## <a name="next-steps"></a>Nästa steg
Mer information om några av de grundläggande begrepp som beskrivs i den här artikeln finns i [artiklar om Hantera program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra funktioner för apphantering som är tillgängliga i Visual Studio finns i [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).
