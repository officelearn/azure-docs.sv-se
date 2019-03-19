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
ms.openlocfilehash: 0ab6e3f189d4a2e7e8f3bc96108d7979c99fffa8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102677"
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