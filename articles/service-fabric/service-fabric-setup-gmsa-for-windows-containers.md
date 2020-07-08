---
title: Konfigurera gMSA för Azure Service Fabric Container Services
description: Lär dig nu att konfigurera grupphanterade tjänst konton (gMSA) för en behållare som körs i Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75639215"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Konfigurera gMSA för Windows-behållare som körs på Service Fabric

Om du vill konfigurera gMSA (grupphanterade tjänst konton) placeras en fil för autentiseringsuppgifter specifikation ( `credspec` ) på alla noder i klustret. Filen kan kopieras på alla noder med ett VM-tillägg.  `credspec`Filen måste innehålla gMSA konto information. Mer information om `credspec` filen finns i [skapa en specifikation för autentiseringsuppgifter](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Referens specifikationen och `Hostname` taggen anges i applikations manifestet. `Hostname`Taggen måste matcha det gMSA-konto namn som containern kör under.  `Hostname`Taggen gör att behållaren kan autentisera sig själv till andra tjänster i domänen med hjälp av Kerberos-autentisering.  Ett exempel på hur du anger `Hostname` och `credspec` i program manifestet visas i följande kodfragment:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
I nästa steg ska du läsa följande artiklar:

* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en Docker-behållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
