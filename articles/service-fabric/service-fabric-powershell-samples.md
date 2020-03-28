---
title: Azure PowerShell-exempel – Service Fabric
description: Lär dig mer om hur du skapar och hanterar Kluster, appar och tjänster i Azure Service Fabric med Powershell.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: f9ba1b4833b3aff13284a68c23f398ea47ef2ae2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75645658"
---
# <a name="azure-service-fabric-powershell-samples"></a>Azure Service Fabric PowerShell-exempel

Följande tabell innehåller länkar till PowerShell-exempelskript för att hantera Service Fabric-kluster, appar och tjänster.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Skapa kluster** ||
| [Skapa ett kluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Skapar ett Azure Service Fabric-kluster. |
| **Hantera kluster, noder och infrastruktur** ||
| [Lägga till ett appcertifikat](./scripts/service-fabric-powershell-add-application-certificate.md)| Skapar ett X509-certifikat till Key Vault och distribuerar det till en skala för virtuella datorer i klustret. |
| [Uppdatera RDP-portintervallen på virtuella datorer i klustret](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Ändrar RDP-portintervallet på klusternodens virtuella datorer i ett distribuerat kluster.|
| [Uppdatera admin-användare och lösenord för virtuella datorer i klusternoden](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Uppdaterar admin-användare och lösenord för virtuella datorer i klusternoden. |
| [Öppna en port i lastbalanseraren](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Öppna en apport i Azure Load Balancer som tillåter inkommande trafik på en specifik port. |
| [Skapa en regel för inkommande nätverkssäkerhetsgrupper](./scripts/service-fabric-powershell-add-nsg-rule.md) | Skapa ett regel för inkommande nätverkssäkerhetsgrupper för att tillåta inkommande trafik till klustret på en specifik port. |
| **Hantera program** ||
| [Distribuera ett program](./scripts/service-fabric-powershell-deploy-application.md)| Distribuera ett program till ett kluster.|
| [Uppgradera ett program](./scripts/service-fabric-powershell-upgrade-application.md)| Uppgraderar ett program.|
| [Ta bort ett program](./scripts/service-fabric-powershell-remove-application.md)| Tar bort ett program från ett kluster.|
