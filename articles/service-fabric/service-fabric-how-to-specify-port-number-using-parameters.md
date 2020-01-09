---
title: Ange port numret för en tjänst med parametrar
description: Visar hur du använder parametrar för att ange porten för ett program i Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609867"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Så här anger du Port numret för en tjänst med hjälp av parametrar i Service Fabric

Den här artikeln visar hur du anger port numret för en tjänst med hjälp av parametrar i Service Fabric med Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedur för att ange port numret för en tjänst med hjälp av parametrar

I det här exemplet anger du Port numret för ditt asp.net Core webb-API med hjälp av en parameter.

1. Öppna Visual Studio och skapa ett nytt Service Fabric-program.
1. Välj mall för tillstånds lös ASP.NET Core.
1. Välj webb-API.
1. Öppna filen ServiceManifest. xml.
1. Anteckna namnet på den slut punkt som har angetts för din tjänst. Standardvärdet är `ServiceEndpoint`.
1. Öppna filen ApplicationManifest. XML
1. I `ServiceManifestImport`-elementet lägger du till ett nytt `RessourceOverrides`-element med en referens till slut punkten i din ServiceManifest. XML-fil.

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

1. I `Endpoint`-elementet kan du nu åsidosätta alla attribut med en parameter. I det här exemplet anger du `Port` och anger det till ett parameter namn med hakparenteser – till exempel `[MyWebAPI_PortNumber]`

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

1. Fortfarande i filen ApplicationManifest. XML anger du parametern i `Parameters`-elementet

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. Och definiera en `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Öppna mappen ApplicationParameters och filen `Cloud.xml`
1. Om du vill ange en annan port som ska användas vid publicering till ett fjärrkluster lägger du till parametern med port numret till den här filen.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

När du publicerar ditt program från Visual Studio med hjälp av publicerings profilen Cloud. xml är din tjänst konfigurerad för att använda port 80. Om du distribuerar programmet utan att ange parametern MyWebAPI_PortNumber, använder tjänsten Port 8080.

## <a name="next-steps"></a>Nästa steg
Mer information om några av de grundläggande begreppen som beskrivs i den här artikeln finns i artikeln [hantera program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra hanterings funktioner för appar som är tillgängliga i Visual Studio finns i [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).
