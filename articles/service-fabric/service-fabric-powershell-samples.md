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
ms.date: 11/29/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 5d8137b2ca6c47713b7a681090963dd2749dd1f2
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634055"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-exempel

Följande tabell innehåller länkar till PowerShell-exempelskript för att hantera Service Fabric-kluster, appar och tjänster.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Skapa kluster** ||
| [Skapa ett kluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Skapar ett Azure Service Fabric-kluster. |
| **Hantera kluster, noder och infrastruktur** ||
| [Lägga till ett appcertifikat](./scripts/service-fabric-powershell-add-application-certificate.md)| Lägger till appcertifikatet X.509 för alla noder i ett kluster. |
| [Uppdatera RDP-portintervallen på virtuella datorer i klustret](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Ändrar RDP-portintervallet på klusternodens virtuella datorer i ett distribuerat kluster.|
| [Uppdatera admin-användare och lösenord för virtuella datorer i klusternoden](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Uppdaterar admin-användare och lösenord för virtuella datorer i klusternoden. |
| [Öppna en port i lastbalanseraren](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Öppna en apport i Azure Load Balancer som tillåter inkommande trafik på en specifik port. |
| [Skapa en regel för inkommande nätverkssäkerhetsgrupper](./scripts/service-fabric-powershell-add-nsg-rule.md) | Skapa ett regel för inkommande nätverkssäkerhetsgrupper för att tillåta inkommande trafik till klustret på en specifik port. |
| **Hantera program** ||
| [Distribuera ett program](./scripts/service-fabric-powershell-deploy-application.md)| Distribuera ett program till ett kluster.|
| [Uppgradera ett program](./scripts/service-fabric-powershell-upgrade-application.md)| Uppgraderar ett program.|
| [Ta bort ett program](./scripts/service-fabric-powershell-remove-application.md)| Tar bort ett program från ett kluster.|
