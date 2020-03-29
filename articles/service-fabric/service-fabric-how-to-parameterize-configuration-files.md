---
title: Parameterisera config-filer i Azure Service Fabric
description: Lär dig hur du parameteriserar konfigurationsfiler i Service Fabric, en användbar teknik när du hanterar flera miljöer.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644638"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Parameterisera konfigurationsfiler i Service Fabric

I den här artikeln beskrivs hur duparariserar en konfigurationsfil i Service Fabric.  Om du inte redan är bekant med de grundläggande begreppen att hantera program för flera miljöer läser du [Hantera program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedur för parameterisering av konfigurationsfiler

I det här exemplet åsidosätter du ett konfigurationsvärde med hjälp av parametrar i programdistributionen.

1. Öppna filen * \<MyService>\PackageRoot\Config\Settings.xml* i serviceprojektet.
1. Ange ett namn och värde för konfigurationsparametern, till exempel cachestorlek som är lika med 25, genom att lägga till följande XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Spara och stäng filen.
1. Öppna filen * \<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml.*
1. Deklarera en parameter och standardvärde i elementet `Parameters` i filen ApplicationManifest.xml.  Vi rekommenderar att parameternamnet innehåller namnet på tjänsten (till exempel "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. I `ServiceManifestImport` avsnittet ApplicationManifest.xml lägger du `ConfigOverrides` `ConfigOverride` till ett och ett element som refererar till konfigurationspaketet, avsnittet och parametern.

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
> Om du lägger till en ConfigOverride väljer Service Fabric alltid de programparametrar eller standardvärdet som anges i programmanifestet.
>
>

## <a name="next-steps"></a>Nästa steg
Information om andra funktioner för apphantering som är tillgängliga i Visual Studio finns i [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).
