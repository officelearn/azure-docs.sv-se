---
title: Konfigurationsfiler i Azure Service Fabric parameteriserar | Microsoft Docs
description: Visar hur du parameterstyra konfigurationsfiler i Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 14fbdf27b8735bb3f2dc91ce0891711e9aaf2af3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Parameteriserar konfigurationsfiler i Service Fabric

Den här artikeln visar hur du parameterstyra en konfigurationsfil i Service Fabric.

## <a name="procedure-for-parameterizing-configuration-files"></a>Proceduren för Parameterisera konfigurationsfiler

I det här exemplet kan du åsidosätta ett konfigurationsvärde med parametrar i programdistributionen.

1. Öppna filen Config\Settings.xml.
1. Ange en konfigurationsparameter genom att lägga till följande XML-filen:

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. Spara och stäng filen.
1. Öppna filen `ApplicationManifest.xml`.
1. Lägg till en `ConfigOverride` element som refererar till konfigurationspaketet, avsnittet och parametern.

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. Fortfarande i filen ApplicationManifest.xml du ange parametern i den `Parameters` element

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. Och definiera en `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> I de fall där du lägger till en ConfigOverride, väljer alltid Service Fabric applikationsparametrarna eller standardvärdet som angetts i applikationsmanifestet.
>
>

## <a name="next-steps"></a>Nästa steg
Mer information om några grundläggande begrepp som diskuteras i den här artikeln finns det [hantera program för flera miljöer artiklar](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra app-hanteringsfunktioner som är tillgängliga i Visual Studio finns [hantera din Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).