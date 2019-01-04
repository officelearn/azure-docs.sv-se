---
title: Stöd för operativsystem, container motorer – Azure IoT Edge | Microsoft Docs
description: Lär dig vilka operativsystem kan köra Azure IoT Edge-daemon och runtime och stöds behållare motorer för dina produktionsenheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6443260de0a8bd8531edb303fa581d281034fef3
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555616"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge stöds system

Det finns en mängd olika sätt att söka efter support för Azure IoT Edge-produkten.

**Rapportering buggar** – flesta utveckling som ingår i Azure IoT Edge-produkten sker i IoT Edge open source-projektet. Buggar kan rapporteras på den [sidan ärenden](https://github.com/azure/iotedge/issues) i projektet. Korrigeringar hamna snabbt från projektet i produktuppdateringar.

**Microsofts kundsupport team** – användare som har en [supportavtal](https://azure.microsoft.com/support/plans/) kan interagera med Microsofts kundsupport-teamet genom att skapa ett supportärende direkt från den [Azure-portalen](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

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
| Raspbian stretch | Nej | Ja|
| Ubuntu Server 16.04 | Ja | Nej |
| Ubuntu Server 18.04 | Ja | Nej |

Offentlig förhandsversion
| Operativsystem | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core build 17763 | Ja | Nej |
| Windows 10 skapa 17763 för Windows-behållare<br><br>Windows 10 build 14393 eller senare för Linux-behållare\* | Ja | Nej |
| Windows Server 2019 för Windows-behållare<br><br>Windows Server 2016 eller senare för Linux-behållare\* | Ja | Nej |

\* Microsoft tillhandahåller installationspaket för Linux-behållare på Windows-enheter endast för utveckling och testning. Detta är inte en konfiguration som stöds för användning i produktion. 

### <a name="tier-2"></a>Nivå 2
Nivå 2-system kan betraktas som kompatibelt med Azure IoT Edge och relativt lätt kan användas. Detta innebär att:
* Microsoft har gjort ad hoc testning på plattformarna eller känner av en partner som kör Azure IoT Edge-plattformen
* Installationspaket för andra plattformar fungerar på följande plattformar

| Operativsystem | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Ja | Ja |
| Debian 8 | Ja | Ja |
| Debian 9 | Ja | Ja |
| RHEL 7.5 | Ja | Ja |
| Ubuntu 18.04 | Ja | Ja |
| Ubuntu 16.04 | Ja | Ja |
| Vind floden 8 | Ja | Nej |
| Yocto | Ja | Nej |

## <a name="container-engines"></a>Behållare-motorer
Azure IoT Edge måste en motor för behållaren att starta moduler, oavsett vilket operativsystem som den körs. Microsoft tillhandahåller en motor för behållare, moby-motorn att uppfylla detta krav. Den är baserad på öppen källkod-projekt Moby. Docker CE- och Docker EE är andra populära container-motorer. De även baseras på Moby öppnas source-projektet och är kompatibla med Azure IoT Edge. Microsoft tillhandahåller support efter bästa förmåga för system med hjälp av de motorerna för behållaren. Microsoft har dock inte att leverera korrigeringar för problem i dem. Därför rekommenderar Microsoft med moby-motor på produktionssystemen.

