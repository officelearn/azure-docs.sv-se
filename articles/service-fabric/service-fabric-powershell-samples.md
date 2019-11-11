---
title: Azure PowerShell-exempel – Service Fabric | Microsoft Docs
description: Azure PowerShell-exempel – Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cae88e142c3bca15e837db7f084eef68434a78ca
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903256"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-exempel

Följande tabell innehåller länkar till PowerShell-exempelskript för att hantera Service Fabric-kluster, appar och tjänster.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Skapa kluster** ||
| [Skapa ett kluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Skapar ett Azure Service Fabric-kluster. |
| **Hantera kluster, noder och infrastruktur** ||
| [Lägga till ett appcertifikat](./scripts/service-fabric-powershell-add-application-certificate.md)| Skapar ett X509-certifikat för att Key Vault och distribuera det till en skalnings uppsättning för virtuella datorer i klustret. |
| [Uppdatera RDP-portintervallen på virtuella datorer i klustret](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Ändrar RDP-portintervallet på klusternodens virtuella datorer i ett distribuerat kluster.|
| [Uppdatera admin-användare och lösenord för virtuella datorer i klusternoden](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Uppdaterar admin-användare och lösenord för virtuella datorer i klusternoden. |
| [Öppna en port i lastbalanseraren](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Öppna en apport i Azure Load Balancer som tillåter inkommande trafik på en specifik port. |
| [Skapa en regel för inkommande nätverkssäkerhetsgrupper](./scripts/service-fabric-powershell-add-nsg-rule.md) | Skapa ett regel för inkommande nätverkssäkerhetsgrupper för att tillåta inkommande trafik till klustret på en specifik port. |
| **Hantera program** ||
| [Distribuera ett program](./scripts/service-fabric-powershell-deploy-application.md)| Distribuera ett program till ett kluster.|
| [Uppgradera ett program](./scripts/service-fabric-powershell-upgrade-application.md)| Uppgraderar ett program.|
| [Ta bort ett program](./scripts/service-fabric-powershell-remove-application.md)| Ta bort ett program från ett kluster.|
