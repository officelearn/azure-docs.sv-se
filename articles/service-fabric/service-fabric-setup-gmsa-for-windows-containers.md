---
title: Konfigurera gMSA för Azure Service Fabric-behållartjänster | Microsoft Docs
description: Lär dig att konfigurera gMSA för en behållare i Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: 4ad697e01ef9e023232e2a2a16e4584a2779f84a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806308"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Konfigurera gMSA för Windows-behållare som körs på Service Fabric

Konfigurera gMSA (gruppen hanterade tjänstkonton), en credential-specifikationsfilen (`credspec`) placeras på alla noder i klustret. Filen kopieras på alla noder med hjälp av ett VM-tillägg.  Den `credspec` filen måste innehålla uppgifterna för ett gMSA-konto. Mer information om den `credspec` fil, se [tjänstkonton](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). Specifikationen autentiseringsuppgifter och `Hostname` tagg har angetts i manifestet. Den `Hostname` taggen måste matcha namnet på gMSA-kontot som behållaren körs under.  Den `Hostname` tagg kan behållare för att autentisera sig själv till andra tjänster i domänen via Kerberos-autentisering.  Ett exempel för att ange den `Hostname` och `credspec` i programmet manifest visas i följande kodavsnitt:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Som ett nästa steg kan du läsa följande artiklar:

* [Distribuera en Windows-behållare till Service Fabric i Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en Docker-behållare till Service Fabric i Linux](service-fabric-get-started-containers-linux.md)
