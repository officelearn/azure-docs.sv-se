---
title: Konfigurera gMSA för Azure Service Fabric-behållartjänster | Microsoft Docs
description: Lär dig att konfigurera gMSA för en behållare som körs i Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: subramar
ms.openlocfilehash: e4cd0b42e21609f88edc28c8fd7b5c433d56b3c1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209101"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Ställ in gMSA för Windows-behållare som körs på Service Fabric

Att ställa in gMSA (grupp hanterade tjänstkonton), en fil för specifikation av autentiseringsuppgifter (`credspec`) är placerad på alla noder i klustret. Filen kan kopieras på alla noder som använder ett tillägg för virtuell dator.  Den `credspec` filen måste innehålla gMSA-kontoinformation. Mer information om den `credspec` fil, se [tjänstkonton](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). Specifikationen autentiseringsuppgifter och `Hostname` taggen har angetts i applikationsmanifestet. Den `Hostname` taggen måste matcha namnet på gMSA-kontot som behållaren körs under.  Den `Hostname` tillåter behållare för att autentisera sig till andra tjänster i domänen via Kerberos-autentisering.  Ett exempel för att ange den `Hostname` och `credspec` manifestet i programmet visas i följande utdrag:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Läs följande artiklar som ett nästa steg:

* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en dockerbehållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
