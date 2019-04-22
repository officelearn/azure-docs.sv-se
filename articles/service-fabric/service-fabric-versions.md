---
title: Lär dig mer om Azure Service Fabric-kluster-versioner | Microsoft Docs
description: Azure Service Fabric-kluster-versioner som stöds
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: d99000e1682b662f4bceb28096395243c894282f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681615"
---
# <a name="supported-service-fabric-versions"></a>Service Fabric-versioner som stöds

Kontrollera att klustret alltid körs på en Service Fabric-version som stöds. Och när vi presentera en ny version av Service Fabric, markeras den tidigare versionen för support upphör efter 60 dagar efter att minst. De nya versionerna tillkännages [på Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/).

Se följande dokument på information om hur du håller ditt kluster som kör en Service Fabric-version som stöds.

- [Uppgradera Service Fabric-versionen på ett Azure-kluster](service-fabric-cluster-upgrade.md)
- [Uppgradera Service Fabric-versionen på ett fristående windows server-kluster](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versioner som stöds

I följande tabell visas de Service Fabric-versioner som stöds och deras slutdatum för support.

| **Service Fabric-körning i klustret** | **Kan uppgradera direkt från klusterversion** |**Kompatibla SDK / NuGet paket versioner** | **Slutet av supportperioden** |
| --- | --- |--- | --- |
| Alla klusterversioner före 5.3.121 | 5.1.158* |Mindre än eller lika med version 2.3 |20 januari 2017 |
| 5.3.* | 5.1.158.* |Mindre än eller lika med version 2.3 |24 februari 2017 |
| 5.4.* | 5.1.158.* |Mindre än eller lika med version 2.4 |Kan 10,2017       |
| 5.5.* | 5.4.164.* |Mindre än eller lika med version 2.5 |Augusti 10,2017    |
| 5.6.* | 5.4.164.* |Mindre än eller lika med version 2.6 |Oktober 13,2017   |
| 5.7.* | 5.4.164.* |Mindre än eller lika med version 2.7 |December 15,2017  |
| 6.0.* | 5.6.205.* |Mindre än eller lika med version 2.8 |Mars 30,2018     |
| 6.1.* | 5.7.221.* |Mindre än eller lika med version 3.0 |Juli 15,2018      |
| 6.2.* | 6.0.232.* |Mindre än eller lika med version 3.1 |Oktober 26,2018   |
| 6.3.* | 6.1.480.* |Mindre än eller lika med version 3.2 |Mars 31,2019  |
| 6.4.* | 6.2.301.* |Mindre än eller lika med version 3.3 |Aktuell version och så inget slutdatum |

## <a name="supported-operating-systems"></a>Operativsystem som stöds

 I följande tabell visas operativsystem som stöds för Service Fabric-versioner som stöds.

| **Operativsystem** | **Tidigaste stöds Service Fabric-Version** |
| --- | --- |
| Windows Server 2012 R2 | Alla versioner |
| Windows Server 2016 | Alla versioner |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

