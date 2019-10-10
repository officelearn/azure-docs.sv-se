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
ms.author: atsenthi
ms.openlocfilehash: 45fc4c924a8fb794ad81529de74b98ee812f46c5
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170430"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Konfigurera gMSA för Windows-behållare som körs på Service Fabric

Om du vill konfigurera gMSA (grupphanterade tjänst konton) placeras en fil med autentiseringsuppgifter (`credspec`) på alla noder i klustret. Filen kan kopieras på alla noder med ett VM-tillägg.  @No__t-0-filen måste innehålla gMSA konto information. Mer information om `credspec`-filen finns i [skapa en specifikation för autentiseringsuppgifter](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Credential-specifikationen och `Hostname`-taggen anges i applikations manifestet. Taggen `Hostname` måste matcha det gMSA-kontonamn som behållaren körs under.  Med taggen `Hostname` kan behållaren autentisera sig själv för andra tjänster i domänen med hjälp av Kerberos-autentisering.  Ett exempel på hur du anger `Hostname` och `credspec` i applikations manifestet visas i följande kodfragment:

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
