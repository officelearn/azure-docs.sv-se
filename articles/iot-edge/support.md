---
title: Stöd för Azure IoT-Edge plattformar | Microsoft Docs
description: Plattformar som stöds av Azure IoT kant
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6bf918428312c511505304bd23b68cd19e46471
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036668"
---
# <a name="azure-iot-edge-support"></a>Azure IoT-Edge-Support
Det finns flera olika sätt att begära support för Azure IoT kant-produkten.

**Rapportering av programfel** – flesta utveckling som ingår i produkten Azure IoT kant sker i projektet IoT kant öppen källkod. Fel rapporteras på den [sidan ärenden](https://github.com/azure/iot-edge/issues) i projektet. Korrigeringar nå snabbt från projektet i att produktuppdateringarna.

**Microsofts kundsupport team** – användare som har en [supportavtal](https://azure.microsoft.com/support/plans/) kan delta i Microsofts kundsupport teamet genom att skapa ett supportärende direkt från den [Azure-portalen]( https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funktionen begäranden** – i Azure IoT kant produkten spårar funktionsförfrågningar via produktens [User Voice sidan](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Operativsystem
Azure IoT kant som körs på de flesta operativsystem som kan köra behållare. men stöds alla dessa lika inte. Operativsystem som är grupperade i nivåer som representerar supportnivå som användare kan förvänta dig.

### <a name="tier-1"></a>Nivå 1
Nivå 1-system kan betraktas som officiellt stöds. Detta innebär att Microsoft:
* har dessa operativsystem i testerna
* ger installationspaket för dem.

Allmänt tillgänglig
* Ubuntu 18.04
* Ubuntu 16.04
* Raspbian stretch

Förhandsversion
* Windows 10 Server 1803
* Windows 10 IoT Enterprise (med April 2018 uppdatering)
* Windows 10 IoT Core (med April 2018 uppdatering)

### <a name="tier-2"></a>Nivå 2
Nivå 2-system kan betraktas som kompatibelt med Azure IoT kant och relativt lätt kan användas. Detta innebär att:
* Microsoft har gjort ad hoc-tester på plattformarna eller känner av en partner har kör Azure IoT kanten på plattformen
* Installationspaket för andra plattformar fungerar på följande plattformar

Ubuntu Server 18.04

Ubuntu Server 16.04

Vind floden 8

Yocto

Debian

Mac

## <a name="container-engines"></a>Behållaren motorer
Azure IoT-Edge måste en behållare motor att starta moduler, oberoende av operativsystemet som körs. Microsoft tillhandahåller en behållare motorn moby-motorn att uppfylla detta krav. Den är baserad på projektet Moby öppen källkod. Docker CE och Docker EE är andra populära behållaren motorer. De också baserat på Moby öppnas-projekt och är kompatibla med Azure IoT kant. Microsoft erbjuder support efter bästa förmåga för system med dessa behållare motorer. Microsoft har dock inte möjlighet att leverera korrigeringar för problem. Därför Microsoft rekommenderar att du använder moby-motorn på datorer i produktionsmiljö.


<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 