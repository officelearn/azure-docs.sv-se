---
title: Stöd för Azure IoT Edge-plattformar | Microsoft Docs
description: Plattformar som stöds av Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 91821d66ac0be265e6b66fd9eb2378169e337430
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057455"
---
# <a name="azure-iot-edge-support"></a>Support för Azure IoT Edge
Det finns en mängd olika sätt att söka efter support för Azure IoT Edge-produkten.

**Rapportering buggar** – flesta utveckling som ingår i Azure IoT Edge-produkten sker i IoT Edge open source-projektet. Buggar kan rapporteras på den [sidan ärenden](https://github.com/azure/iotedge/issues) i projektet. Korrigeringar hamna snabbt från projektet i produktuppdateringar.

**Microsofts kundsupport team** – användare som har en [supportavtal](https://azure.microsoft.com/support/plans/) kan interagera med Microsofts kundsupport-teamet genom att skapa ett supportärende direkt från den [Azure-portalen]( https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funktionen begäranden** – The Azure IoT Edge-produkten spårar funktionsförfrågningar via produktens [User Voice-sidan](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Operativsystem
Azure IoT Edge som körs på de flesta operativsystem som kan köra behållare. men stöds alla dessa lika inte. Operativsystem är grupperade i nivåer som representerar supportnivå de kan förvänta sig.

### <a name="tier-1"></a>Nivå 1
Nivå 1-system kan betraktas som officiellt stöds. Detta innebär att Microsoft:
* har dessa operativsystem i automatiska tester
* tillhandahåller installationspaket för dem.

Allmänt tillgänglig
| Operativsystem | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Ubuntu Server 18.04 | Ja | Nej |
| Ubuntu Server 16.04 | Ja | Nej |
| Raspbian stretch | Nej | Ja|

Offentlig förhandsversion
| Operativsystem | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10-Server 1803 | Ja | Nej |
| Windows 10 IoT Enterprise (April 2018 uppdatering) | Ja | Nej |
| Windows 10 IoT Core (April 2018 uppdatering) | Ja | Nej |

### <a name="tier-2"></a>Nivå 2
Nivå 2-system kan betraktas som kompatibelt med Azure IoT Edge och relativt lätt kan användas. Detta innebär att:
* Microsoft har gjort ad hoc testning på plattformarna eller känner av en partner som kör Azure IoT Edge-plattformen
* Installationspaket för andra plattformar fungerar på följande plattformar

| Operativsystem | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Ubuntu 18.04 | Ja | Nej |
| Ubuntu 16.04 | Ja | Nej |
| Vind floden 8 | Ja | Nej |
| Yocto | Ja | Nej |
| Debian | Ja | Nej |
| Mac | Ja | Nej |

## <a name="container-engines"></a>Behållare-motorer
Azure IoT Edge måste en motor för behållaren att starta moduler, oavsett vilket operativsystem som den körs. Microsoft tillhandahåller en motor för behållare, moby-motorn att uppfylla detta krav. Den är baserad på öppen källkod-projekt Moby. Docker CE- och Docker EE är andra populära container-motorer. De även baseras på Moby öppnas source-projektet och är kompatibla med Azure IoT Edge. Microsoft tillhandahåller support efter bästa förmåga för system med hjälp av de motorerna för behållaren. Microsoft har dock inte att leverera korrigeringar för problem i dem. Därför rekommenderar Microsoft med moby-motor på produktionssystemen.


<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 
