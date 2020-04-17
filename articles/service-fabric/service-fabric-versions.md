---
title: Klusterversioner som stöds i Azure Service Fabric
description: Lär dig mer om klusterversioner i Azure Service Fabric, inklusive en länk till de senaste versionerna från Service Fabric-teambloggen.
ms.topic: troubleshooting
ms.date: 03/02/2020
ms.openlocfilehash: b76e498926492ce7acaa696dfe9a0299fb5573e0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460536"
---
# <a name="supported-service-fabric-versions"></a>Service Fabric-versioner som stöds

Kontrollera att klustret alltid kör en Azure Service Fabric-version som stöds. Minst 60 dagar efter att vi tillkännager lanseringen av en ny version av Service Fabric, slutar stödet för den tidigare versionen. Du hittar meddelanden om nya utgåvor på [Service Fabric team blogg](https://azure.microsoft.com/updates/?product=service-fabric).

Mer information om hur du håller klustret igång en service fabric-version som stöds finns i följande dokument:

- [Uppgradera ett Azure Service Fabric-kluster](service-fabric-cluster-upgrade.md)
- [Uppgradera Service Fabric-versionen som körs i ditt fristående Windows Server-kluster](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versioner som stöds

I följande tabell visas versionerna av Service Fabric och deras supportslutdatum.

| Service Fabric-körning i klustret | Kan uppgradera direkt från klusterversion |Kompatibel SDK- eller NuGet-paketversion | på supporten |
| --- | --- |--- | --- |
| Alla klusterversioner före 5.3.121 | 5.1.158.* |Mindre än eller lika med version 2.3 |den 20 januari 2017 |
| 5.3.* | 5.1.158.* |Mindre än eller lika med version 2.3 |den 24 februari 2017 |
| 5.4.* | 5.1.158.* |Mindre än eller lika med version 2.4 |10 maj 2017       |
| 5.5.* | 5.4.164.* |Mindre än eller lika med version 2.5 |den 10 augusti 2017    |
| 5.6.* | 5.4.164.* |Mindre än eller lika med version 2.6 |den 13 oktober 2017   |
| 5.7.* | 5.4.164.* |Mindre än eller lika med version 2.7 |15 december 2017  |
| 6.0.* | 5.6.205.* |Mindre än eller lika med version 2.8 |den 30 mars 2018     |
| 6.1.* | 5.7.221.* |Mindre än eller lika med version 3.0 |den 15 juli 2018      |
| 6.2.* | 6.0.232.* |Mindre än eller lika med version 3.1 |den 26 oktober 2018   |
| 6.3.* | 6.1.480.* |Mindre än eller lika med version 3.2 |den 31 mars 2019  |
| 6.4.* | 6.2.301.* |Mindre än eller lika med version 3.3 |den 15 september 2019 |
| 6.5.* | 6.4.617.* |Mindre än eller lika med version 3.4 |den 1 augusti 2020 |
| 7.0.466.* | 6.4.664.* |Mindre än eller lika med version 4.0|den 1 augusti 2020  |
| 7.0.466.* | 6.5.* |Mindre än eller lika med version 4.0|den 1 augusti 2020 |
| 7.0.470.* | 7.0.466.* |Mindre än eller lika med version 4.0 |den 1 augusti 2020  |
| 7.1.409.* | 7.0.466.* |Mindre än eller lika med version 4.0 |Aktuell version, så inget slutdatum |
## <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de operativsystem som stöds för de Service Fabric-versioner som stöds.

| Operativsystem | Tidigaste Service Fabric-versionen som stöds |
| --- | --- |
| Windows Server 2012 R2 | Alla versioner |
| Windows Server 2016 | Alla versioner |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |

## <a name="supported-version-names"></a>Versionsnamn som stöds

I följande tabell visas versionsnamnen för Service Fabric och motsvarande versionsnummer.

| Versionsnamn | Windows-versionsnummer | Linux-versionsnummer |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | Ej tillämpligt |
| 5.3 CU1 | 5.3.204.9494 | Ej tillämpligt |
| 5.3 CU2 | 5.3.301.9590 | Ej tillämpligt |
| 5.3 CU3 | 5.3.311.9590 | Ej tillämpligt |
| 5.4 CU2 | 5.4.164.9494 | Ej tillämpligt |
| 5,5 CU1 | 5.5.216.0    | Ej tillämpligt |
| 5,5 CU2 | 5.5.219.0    | Ej tillämpligt |
| 5,5 CU3 | 5.5.227.0    | Ej tillämpligt |
| 5,5 CU4 | 5.5.232.0    | Ej tillämpligt |
| 5,6 RTO | 5.6.204.9494 | Ej tillämpligt |
| 5,6 CU2 | 5.6.210.9494 | Ej tillämpligt |
| 5,6 CU3 | 5.6.220.9494 | Ej tillämpligt |
| 5.7 RTO | 5.7.198.9494 | Ej tillämpligt |
| 5,7 CU4 | 5.7.221.9494 | Ej tillämpligt |
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | Ej tillämpligt |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | Ej tillämpligt |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Ej tillämpligt |
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Ej tillämpligt |
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
