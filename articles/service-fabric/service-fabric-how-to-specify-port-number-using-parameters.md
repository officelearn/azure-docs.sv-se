---
title: Så här anger du portnumret för en tjänst med parametrar i Azure Service Fabric | Microsoft Docs
description: Visar hur du använder parametrar för att ange porten för ett program i Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 06cfb375c6c18082a0d0316cfcb742a7779fc8a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Så här anger du portnumret för en tjänst med parametrar i Service Fabric

Den här artikeln visar hur du anger du portnumret för en tjänst med parametrar i Service Fabric med Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Proceduren för att ange portnumret för en tjänst med parametrar

I det här exemplet anger du portnumret för din asp.net core webb-API som använder en parameter.

1. Öppna Visual Studio och skapa ett nytt Service Fabric-program.
1. Välj mallen tillståndslös ASP.NET Core.
1. Välj webb-API.
1. Öppna filen ServiceManifest.xml.
1. Notera namnet på slutpunkten som specificeras för din tjänst. Standardvärdet är `ServiceEndpoint`.
1. Öppna filen ApplicationManifest.xml
1. I den `ServiceManifestImport` element, lägga till en ny `RessourceOverrides` element med en referens till slutpunkten i filen ServiceManifest.xml.

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

1. I den `Endpoint` element, kan du nu åsidosätta alla attribut som använder en parameter. I det här exemplet anger du `Port` och ange det till ett parameternamn med hakparenteser - t.ex. `[MyWebAPI_PortNumber]`

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

1. Fortfarande i filen ApplicationManifest.xml du ange parametern i den `Parameters` element

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

1. Öppna mappen ApplicationParameters och `Cloud.xml` fil
1. Om du vill ange en annan port som ska användas vid publicering till ett kluster, lägger du till parametern med portnummer till den här filen.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="80" />
      </Parameters>
    ```

När publicering av programmet från Visual Studio med hjälp av Cloud.xml publiceringsprofil, har tjänsten konfigurerats för användning av port 80. Om du distribuerar programmet utan att ange parametern MyWebAPI_PortNumber använder tjänsten port 8080.

## <a name="next-steps"></a>Nästa steg
Mer information om några grundläggande begrepp som diskuteras i den här artikeln finns det [hantera program för flera miljöer artiklar](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra app-hanteringsfunktioner som är tillgängliga i Visual Studio finns [hantera din Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).