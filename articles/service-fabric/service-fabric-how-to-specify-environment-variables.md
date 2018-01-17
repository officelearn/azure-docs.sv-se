---
title: "Anvisningar anger miljövariabler för tjänster i Azure Service Fabric | Microsoft Docs"
description: "Visar hur du använder miljövariabler för program i Service Fabric"
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: d487eeadde9f9a45549763863f8fe5b06b2945a4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Hur du anger miljövariabler för tjänster i Service Fabric

Den här artikeln visar hur du anger miljövariabler för en tjänst eller behållare i Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Proceduren för att ange miljövariabler för tjänster

I det här exemplet anger du en miljövariabel för en behållare. Artikeln förutsätter att du redan har ett manifest för applikationen eller tjänsten.

1. Öppna filen ServiceManifest.xml.
1. I den `CodePackage` element, lägga till en ny `EnvironmentVariables` element och en `EnvironmentVariable` element för varje miljövariabeln.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DeafultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    Miljövariabler kan åsidosättas i programmanifestet.

1. Om du vill åsidosätta miljövariablerna i programmanifestet använder den `EnvironmentOverrides` element.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om några grundläggande begrepp som diskuteras i den här artikeln finns det [hantera program för flera miljöer artiklar](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra app-hanteringsfunktioner som är tillgängliga i Visual Studio finns [hantera din Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).