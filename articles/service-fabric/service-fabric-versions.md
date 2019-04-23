---
title: Klusterversioner som stöds i Azure Service Fabric | Microsoft Docs
description: Läs mer om i Azure Service Fabric-kluster-versioner.
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
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998829"
---
# <a name="supported-service-fabric-versions"></a>Service Fabric-versioner som stöds

Kontrollera att klustret körs alltid en Azure Service Fabric-version som stöds. Minst 60 dagar efter att vi presentera en ny version av Service Fabric, stöd för den tidigare versionen slutar. Du hittar nyheterna om nya versioner om den [Service Fabric-teamets blogg](https://blogs.msdn.microsoft.com/azureservicefabric/).

Se följande dokument för information om hur du håller ditt kluster som kör en Service Fabric-version som stöds:

- [Uppgradera ett Azure Service Fabric-kluster](service-fabric-cluster-upgrade.md)
- [Uppgradera Service Fabric-versionen som körs på ditt fristående Windows Server-kluster](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versioner som stöds

I följande tabell visas vilka versioner av Service Fabric och deras slutdatum för support.

| Service Fabric-körning i klustret | Kan uppgradera direkt från klusterversion |Kompatibla SDK eller NuGet Paketversion | Support upphör |
| --- | --- |--- | --- |
| Alla klusterversioner före 5.3.121 | 5.1.158.* |Mindre än eller lika med version 2.3 |20 januari 2017 |
| 5.3.* | 5.1.158.* |Mindre än eller lika med version 2.3 |24 februari 2017 |
| 5.4.* | 5.1.158.* |Mindre än eller lika med version 2.4 |10 maj 2017       |
| 5.5.* | 5.4.164.* |Mindre än eller lika med version 2.5 |Augusti 10,2017    |
| 5.6.* | 5.4.164.* |Mindre än eller lika med version 2.6 |Oktober 13,2017   |
| 5.7.* | 5.4.164.* |Mindre än eller lika med version 2.7 |15 december 2017  |
| 6.0.* | 5.6.205.* |Mindre än eller lika med version 2.8 |Den 30 mars 2018     |
| 6.1.* | 5.7.221.* |Mindre än eller lika med version 3.0 |15 juli 2018      |
| 6.2.* | 6.0.232.* |Mindre än eller lika med version 3.1 |Den 26 oktober 2018   |
| 6.3.* | 6.1.480.* |Mindre än eller lika med version 3.2 |Den 31 mars 2019  |
| 6.4.* | 6.2.301.* |Mindre än eller lika med version 3.3 |Aktuell version, så ingen slutdatum |

## <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas operativsystem som stöds för Service Fabric-versioner som stöds.

| Operativsystem | Tidigaste Service Fabric-version som stöds |
| --- | --- |
| Windows Server 2012 R2 | Alla versioner |
| Windows Server 2016 | Alla versioner |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

