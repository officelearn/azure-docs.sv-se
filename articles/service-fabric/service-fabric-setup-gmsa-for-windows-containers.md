---
title: Installation av gMSA för Azure Service Fabric-behållartjänster
description: Lär dig nu att konfigurera grupp hanterade tjänstkonton (gMSA) för en behållare som körs i Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639215"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Konfigurera gMSA för Windows-behållare som körs på Service Fabric

Om du vill konfigurera gMSA (grupphanterade tjänstkonton) placeras en referensspecifikationsfil (`credspec`) på alla noder i klustret. Filen kan kopieras på alla noder med ett VM-tillägg.  Filen `credspec` måste innehålla redovisningsinformationen för gMSA.The file must contain the gMSA account information. Mer information om `credspec` filen finns i [Skapa en referensspecifikation](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Autentiseringsuppgifterna och taggen `Hostname` anges i programmanifestet. Taggen `Hostname` måste matcha gMSA-kontonamnet som behållaren körs under.  Taggen `Hostname` gör att behållaren kan autentisera sig till andra tjänster i domänen med Kerberos-autentisering.  Ett exempel för `Hostname` att `credspec` ange och i programmanifestet visas i följande utdrag:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Som ett nästa steg läser du följande artiklar:

* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en Docker-behållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
