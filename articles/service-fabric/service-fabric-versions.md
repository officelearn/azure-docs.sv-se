---
title: Lär dig mer om Azure Service Fabric-kluster-versioner | Microsoft Docs
description: Azure Service Fabric-kluster-versioner som stöds
services: service-fabric
documentationcenter: .net
author: twhitney
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: TylerMSFT
ms.openlocfilehash: 154869c3e6bcd44a71480a3cf7363537dddcebf9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727689"
---
# <a name="supported-service-fabric-versions"></a>Service Fabric-versioner som stöds

Kontrollera att klustret alltid körs på en Service Fabric-version som stöds. Och när vi presentera en ny version av Service Fabric, markeras den tidigare versionen för support upphör efter 60 dagar efter att minst. De nya versionerna tillkännages [på Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/).

Se följande dokument på information om hur du håller ditt kluster som kör en Service Fabric-version som stöds.

- [Uppgradera Service Fabric-versionen på ett Azure-kluster ](service-fabric-cluster-upgrade.md)
- [Uppgradera Service Fabric-versionen på ett fristående windows server-kluster ](service-fabric-cluster-upgrade-windows-server.md)

Här följer en lista över Service Fabric-versioner som stöds och deras slutdatum för support.

| **Service Fabric-körning i klustret** | **Kan uppgradera direkt från klusterversion** |**Kompatibla SDK / NuGet paket versioner** | **Slutet av supportperioden** |
| --- | --- |--- | --- |
| Alla klusterversioner före 5.3.121 | 5.1.158* |Mindre än eller lika med version 2.3 |20 januari 2017 |
| 5.3.* | 5.1.158.* |Mindre än eller lika med version 2.3 |24 februari 2017 |
| 5.4.* | 5.1.158.* |Mindre än eller lika med version 2.4 |Kan 10,2017       |
| 5.5.* | 5.4.164.* |Mindre än eller lika med version 2.5 |Augusti 10,2017    |
| 5.6.* | 5.4.164.* |Mindre än eller lika med version 2.6 |Oktober 13,2017   |
| 5.7.* | 5.4.164.* |Mindre än eller lika med version 2.7 |December 15,2017  |
| 6.0.* | 5.6.205.* |Mindre än eller lika med version 2.8 |Mars 30,2018     |
| 6.1. * | 5.7.221.* |Mindre än eller lika med version 3.0 |Juli 15,2018      |
| 6.2. * | 6.0.232.* |Mindre än eller lika med version 3.1 |Oktober 26,2018   |
| 6.3. * | 6.1.480.* |Mindre än eller lika med version 3.2 |Februari 28,2019  |
| 6.4. * | 6.2.301.* |Mindre än eller lika med version 3.3 |Aktuell version och så inget slutdatum |