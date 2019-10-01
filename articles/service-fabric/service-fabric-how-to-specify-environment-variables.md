---
title: 'Anvisningar: Ange miljövariabler för tjänster i Azure Service Fabric | Microsoft Docs'
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
ms.openlocfilehash: df9b199c24301016b9f9da8a8dec52129bbf94bd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703532"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Så här anger du miljövariabler för tjänster i Service Fabric

Den här artikeln visar hur du anger miljövariabler för en tjänst eller behållare i Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedur för att ange miljövariabler för tjänster

I det här exemplet anger du en miljö variabel för en behållare. Artikeln förutsätter att du redan har ett program-och tjänst manifest.

1. Öppna filen ServiceManifest. xml.
2. I elementet `CodePackage` lägger du till ett nytt `EnvironmentVariables`-element och ett `EnvironmentVariable`-element för varje miljö variabel.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Miljövariabler kan åsidosättas i applikations manifestet.

3. Om du vill åsidosätta miljövariablerna i applikations manifestet använder du elementet `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Ange miljövariabler dynamiskt med Docker Compose

Service Fabric har stöd för möjligheten att [använda Docker Compose för distribution](service-fabric-docker-compose.md#supported-compose-directives). Skapa filer kan käll miljövariabler från gränssnittet. Det här beteendet kan användas för att ersätta önskade miljö värden dynamiskt:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Nästa steg
Mer information om några av de grundläggande begreppen som beskrivs i den här artikeln finns i artikeln [hantera program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

Information om andra hanterings funktioner för appar som är tillgängliga i Visual Studio finns i [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).
