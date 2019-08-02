---
title: Konfigurera gMSA för Azure Service Fabric Container Services | Microsoft Docs
description: Lär dig nu att konfigurera gMSA för en behållare som körs i Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: subramar
ms.openlocfilehash: 09994c7676de8470efff1707598ddf32a48e41a0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599179"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Konfigurera gMSA för Windows-behållare som körs på Service Fabric

Om du vill konfigurera gMSA (grupphanterade tjänst konton) placeras en fil för`credspec`autentiseringsuppgifter specifikation () på alla noder i klustret. Filen kan kopieras på alla noder med ett VM-tillägg.  `credspec` Filen måste innehålla gMSA konto information. Mer information om `credspec` filen finns i [skapa en specifikation för autentiseringsuppgifter](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Referens specifikationen och `Hostname` taggen anges i applikations manifestet. `Hostname` Taggen måste matcha det gMSA-konto namn som containern kör under.  `Hostname` Taggen gör att behållaren kan autentisera sig själv till andra tjänster i domänen med hjälp av Kerberos-autentisering.  Ett exempel på hur du `Hostname` anger `credspec` och i program manifestet visas i följande kodfragment:

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
