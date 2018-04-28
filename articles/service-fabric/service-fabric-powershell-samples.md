---
title: Azure PowerShell-exempel – Service Fabric | Microsoft Docs
description: Azure PowerShell-exempel – Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 04/09/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 92dbdf2d2e3bdc48cda9ef0e0a53212cc7ea4cb7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-exempel

Följande tabell innehåller länkar till PowerShell-exempelskript för att hantera Service Fabric-kluster, appar och tjänster.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Skapa kluster** ||
| [Skapa ett kluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Skapar ett Azure Service Fabric-kluster. |
|[Skapa ett testkluster (Azure)](./scripts/service-fabric-powershell-create-test-cluster.md)| Skapar ett Service Fabric-testkluster med tre noder i Azure.|
| **Hantera kluster, noder och infrastruktur** ||
| [Lägga till ett appcertifikat](./scripts/service-fabric-powershell-add-application-certificate.md)| Lägger till appcertifikatet X.509 för alla noder i ett kluster. |
| [Uppdatera RDP-portintervallen på virtuella datorer i klustret](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Ändrar RDP-portintervallet på klusternodens virtuella datorer i ett distribuerat kluster.|
| [Uppdatera admin-användare och lösenord för virtuella datorer i klusternoden](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Uppdaterar admin-användare och lösenord för virtuella datorer i klusternoden. |
| [Öppna en port i belastningsutjämnaren](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Öppna en apport i Azure belastningsutjämnare som tillåter inkommande trafik på en specifik port. |
| [Skapa en regel för inkommande nätverkssäkerhetsgrupper](./scripts/service-fabric-powershell-add-nsg-rule.md) | Skapa ett regel för inkommande nätverkssäkerhetsgrupper för att tillåta inkommande trafik till klustret på en specifik port. |
| **Hantera program** ||
| [Distribuera ett program](./scripts/service-fabric-powershell-deploy-application.md)| Distribuera ett program till ett kluster.|
| [Uppgradera ett program](./scripts/service-fabric-powershell-upgrade-application.md)| Uppgraderar ett program.|
| [Ta bort ett program](./scripts/service-fabric-powershell-remove-application.md)| Tar bort ett program från ett kluster.|
