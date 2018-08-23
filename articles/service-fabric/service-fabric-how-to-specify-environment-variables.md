---
title: Så här anger miljövariabler för tjänster i Azure Service Fabric | Microsoft Docs
description: Visar hur du använder miljövariabler för program i Service Fabric
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
ms.openlocfilehash: f75de635f08ae06db349387a436c636c149ec9f2
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42059589"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Hur du anger miljövariabler för tjänster i Service Fabric

Den här artikeln visar hur du anger miljövariabler för en tjänst eller en behållare i Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Proceduren för att ange miljövariabler för tjänster

I det här exemplet anger du en miljövariabel för en behållare. Artikeln förutsätter att du redan har ett manifest för program och tjänster.

1. Öppna filen servicemanifest.XML.
1. I den `CodePackage` element, lägga till en ny `EnvironmentVariables` element och en `EnvironmentVariable` element för varje miljövariabeln.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    Miljövariabler kan åsidosättas i applikationsmanifestet.

1. Om du vill åsidosätta miljövariabler i applikationsmanifestet, använda den `EnvironmentOverrides` element.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om några av de viktigaste begreppen som beskrivs i den här artikeln finns det [hantera program för flera miljöer artiklar](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra funktioner för hantering av appen som är tillgängliga i Visual Studio finns i [hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).