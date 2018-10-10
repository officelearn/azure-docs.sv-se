---
title: Parameterisera konfigurationsfiler i Azure Service Fabric | Microsoft Docs
description: Lär dig mer om att Parameterisera konfigurationsfiler i Service Fabric.
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
ms.openlocfilehash: 9057cdc22e277e4e12e9f439f3fbe0c5a5cda2a2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900521"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Så här Parameterisera konfigurationsfiler i Service Fabric

Den här artikeln visar hur du Parameterisera en konfigurationsfil i Service Fabric.  Om du inte redan är bekant med grundläggande begrepp för att hantera program för flera miljöer kan du läsa [hantera program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Proceduren för Parameterisera konfigurationsfiler

I det här exemplet kan du åsidosätta ett konfigurationsvärde med parametrar i programdistributionen.

1. Öppna den  *<MyService>\PackageRoot\Config\Settings.xml* filen i ditt tjänstprojekt.
1. Ställ in parameternamn och värde, till exempel cachestorleken som är lika med 25, genom att lägga till följande XML:

  ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
  ```

1. Spara och stäng filen.
1. Öppna den  *<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* fil.
1. I filen applicationmanifest.XML deklarera ett värde för parametern och standard i den `Parameters` element.  Vi rekommenderar att parametern innehåller namnet på tjänsten (till exempel ”Mintjänst”).

  ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
  ```
1. I den `ServiceManifestImport` avsnitt i filen applicationmanifest.XML lägger du till en `ConfigOverride` element som refererar till konfigurationspaketet och avsnittet parametern.

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
> I fall där du lägger till en ConfigOverride väljer Service Fabric alltid applikationsparametrarna eller standardvärde som angetts i manifestet.
>
>

## <a name="next-steps"></a>Nästa steg
Information om andra funktioner för hantering av appen som är tillgängliga i Visual Studio finns i [hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).