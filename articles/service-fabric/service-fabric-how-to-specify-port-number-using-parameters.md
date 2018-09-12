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
ms.openlocfilehash: d69e02126564388bf045693b9960e6e574307641
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391342"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Så här anger du portnumret för en tjänst med parametrar i Service Fabric

Den här artikeln visar hur du anger du portnumret för en tjänst med parametrar i Service Fabric med Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Proceduren för att ange portnumret för en tjänst med parametrar

I det här exemplet anger du portnumret för din asp.net core webb-API med hjälp av en parameter.

1. Öppna Visual Studio och skapa ett nytt Service Fabric-program.
1. Välj den tillståndslösa ASP.NET Core-mallen.
1. Välj webb-API.
1. Öppna filen servicemanifest.XML.
1. Anteckna namnet på den slutpunkt som angavs för din tjänst. Standardvärdet är `ServiceEndpoint`.
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

1. I den `Endpoint` element, kan du nu åsidosätter alla attribut med hjälp av en parameter. I det här exemplet anger du `Port` och väljer ett parameternamn med hakparenteser - exempel: `[MyWebAPI_PortNumber]`

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

1. Fortfarande i filen applicationmanifest.XML anger du sedan parametern i den `Parameters` element

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
1. Lägga till parametern med portnummer till den här filen om du vill ange en annan port som ska användas när du publicerar till ett fjärrkluster.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

När publicering av programmet från Visual Studio med hjälp av Cloud.xml publiceringsprofil, är tjänsten konfigurerad för port 80. Om du distribuerar programmet utan att ange parametern MyWebAPI_PortNumber använder tjänsten port 8080.

## <a name="next-steps"></a>Nästa steg
Mer information om några av de viktigaste begreppen som beskrivs i den här artikeln finns det [hantera program för flera miljöer artiklar](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra funktioner för hantering av appen som är tillgängliga i Visual Studio finns i [hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).
