---
title: Parameterisera-konfigurationsfiler i Azure Service Fabric | Microsoft Docs
description: Lär dig hur du Parameterisera konfigurationsfiler i Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: dad497978de7187177998524db3b2f2ee448c717
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464784"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Så här Parameterisera du konfigurationsfiler i Service Fabric

Den här artikeln visar hur du Parameterisera en konfigurations fil i Service Fabric.  Om du inte redan är bekant med de grundläggande begreppen för att hantera program i flera miljöer kan du läsa [hantera program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedur för att parametriserade konfigurationsfiler

I det här exemplet åsidosätter du ett konfigurations värde med hjälp av parametrarna i program distributionen.

1. Öppna \PackageRoot\Config\Settings.XML-filen för > i ditt tjänst projekt.  *\<*
1. Ange ett namn och värde för konfigurations parametern, till exempel cache-storlek som motsvarar 25, genom att lägga till följande XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Spara och stäng filen.
1. Öppna filen  *\<> \ApplicationPackageRoot\ApplicationManifest.xml för* .
1. Deklarera en parameter och ett standardvärde i `Parameters` elementet i ApplicationManifest. XML-filen.  Vi rekommenderar att parameter namnet innehåller namnet på tjänsten (till exempel "fjärrtjänst").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. I avsnittet i filen ApplicationManifest. XML lägger du till ett `ConfigOverrides` och `ConfigOverride` -element, refererar till konfigurations paketet, avsnittet och parametern. `ServiceManifestImport`

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> Om du lägger till en ConfigOverride väljer Service Fabric alltid program parametrarna eller standardvärdet som anges i applikations manifestet.
>
>

## <a name="next-steps"></a>Nästa steg
Information om andra hanterings funktioner för appar som är tillgängliga i Visual Studio finns i [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).
