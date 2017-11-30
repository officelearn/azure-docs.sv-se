---
title: Azure PowerShell-exempel - Service Fabric | Microsoft Docs
description: Azure PowerShell-exempel - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: 
tags: 
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 738677def8b0eb70cfcab46e6fe57f9a344867a5
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-exempel

Följande tabell innehåller länkar till exempel på PowerShell-skript som skapar och hanterar Service Fabric-kluster, program och tjänster.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Skapa kluster** ||
| [Skapa ett kluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Skapar ett Azure Service Fabric-kluster. |
| **Hantera kluster, noder och infrastruktur** ||
| [Lägg till ett certifikat för programmet](./scripts/service-fabric-powershell-add-application-certificate.md)| Lägger till ett program X.509-certifikat för alla noder i ett kluster. |
| [Uppdatera portintervall RDP på klustret virtuella datorer](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Ändrar portintervall RDP på klusternoden virtuella datorer i en distribuerad kluster.|
| [Uppdatera admin-användare och lösenord för klusternoden virtuella datorer](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Uppdaterar admin användarnamn och lösenord för klusternoden virtuella datorer. |
| [Öppna en port i belastningsutjämnaren](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Öppna en port för program i Azure belastningsutjämnare som tillåter inkommande trafik på en specifik port. |
| [Skapa en regel för inkommande network security grupp](./scripts/service-fabric-powershell-add-nsg-rule.md) | Skapa ett inkommande grupp nätverkssäkerhetsregeln för att tillåta inkommande trafik till klustret på en specifik port. |
| **Hantera program** ||
| [Distribuera ett program](./scripts/service-fabric-powershell-deploy-application.md)| Distribuera ett program till ett kluster.|
| [Uppgradera ett program](./scripts/service-fabric-powershell-upgrade-application.md)| Uppgradera ett program.|
| [Ta bort ett program](./scripts/service-fabric-powershell-remove-application.md)| Ta bort ett program från ett kluster.|
