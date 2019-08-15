---
title: Stöd för operativsystem, container motorer – Azure IoT Edge | Microsoft Docs
description: Lär dig vilka operativsystem kan köra Azure IoT Edge-daemon och runtime och stöds behållare motorer för dina produktionsenheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 81d19552b56de540f235960c498c64e7b276320c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030968"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge stöds system

Den här artikeln innehåller information om vilka system och komponenter som stöds av IoT Edge, oavsett om de är officiellt eller i för hands versionen. 

Om du får problem när du använder tjänsten Azure IoT Edge kan du söka efter support på flera sätt. Prova någon av följande kanaler för support:

**Rapportering buggar** – flesta utveckling som ingår i Azure IoT Edge-produkten sker i IoT Edge open source-projektet. Buggar kan rapporteras på den [sidan ärenden](https://github.com/azure/iotedge/issues) i projektet. Korrigeringar hamna snabbt från projektet i produktuppdateringar.

**Microsofts kundsupport team** – användare som har en [supportavtal](https://azure.microsoft.com/support/plans/) kan interagera med Microsofts kundsupport-teamet genom att skapa ett supportärende direkt från den [Azure-portalen](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funktionen begäranden** – The Azure IoT Edge-produkten spårar funktionsförfrågningar via produktens [User Voice-sidan](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Behållare-motorer

Azure IoT Edge moduler implementeras som behållare, så IoT Edge behöver en behållar motor för att starta dem. Microsoft tillhandahåller en motor för behållare, moby-motorn att uppfylla detta krav. Den här behållar motorn är baserad på Moby projekt med öppen källkod. Docker CE- och Docker EE är andra populära container-motorer. De är också baserade på Moby projekt med öppen källkod och är kompatibla med Azure IoT Edge. Microsoft tillhandahåller stöd för bästa prestanda för system som använder dessa behållar motorer. Microsoft kan dock inte leverera korrigeringar för problem i dem. Därför rekommenderar Microsoft med moby-motor på produktionssystemen.

<br>
<center>

![Moby som container runtime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operativsystem
Azure IoT Edge körs på de flesta operativ system som kan köra behållare. dock stöds inte alla dessa system. Operativsystem är grupperade i nivåer som representerar supportnivå de kan förvänta sig.
* Nivå 1-system stöds. För system på nivå 1 är Microsoft:
    * har det här operativ systemet i automatiserade tester
    * tillhandahåller installationspaket för dem.
* Nivå 2-system är kompatibla med Azure IoT Edge och kan användas relativt enkelt. För nivå 2-system:
    * Microsoft har klarat ad hoc-testning på plattformarna eller känner av en partner som kör Azure IoT Edge på plattformen
    * Installationspaket för andra plattformar fungerar på följande plattformar
    
Värd operativ systemets familj måste alltid matcha den familj av gäst operativ systemet som används i en moduls behållare. Med andra ord kan du bara använda Linux-behållare på Linux-och Windows-behållare i Windows. När du använder Windows stöds endast process isolerade behållare, inte för Hyper-V-isolerade behållare.  

<br>
<center>

![Värd operativ system matchar gäst operativ system](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Nivå 1

De system som anges i följande tabell stöds av Microsoft, antingen allmänt tillgängliga eller i offentlig för hands version, och testas med varje ny version. 

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian stretch |  | ![Raspbian-sträckning + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 16.04 | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig granskning  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig granskning |
| Windows 10 IoT Enterprise, build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019, build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019, build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Core, build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


De Windows-operativsystem som anges ovan är kraven för enheter som kör Windows-behållare i Windows, vilket är den enda konfiguration som stöds för produktion. Azure IoT Edge installations paket för Windows tillåter användning av Linux-behållare i Windows. den här konfigurationen är dock endast för utveckling och testning. Mer information finns i [använda IoT Edge i Windows för att köra Linux-behållare](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Nivå 2

Systemen som anges i följande tabell betraktas som kompatibla med Azure IoT Edge, men testas eller bevaras inte aktivt. 

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| CentOS 7.5 | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 8 | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 9 | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 10<sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| RHEL 7.5 | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 16.04 | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 18.04 | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| Vind floden 8 | ![Vind floden 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Yocto | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster<sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10-system, inklusive Raspian Buster, använder en version av OpenSSL som IoT Edge inte stöder. Använd följande kommando för att installera en tidigare version innan du installerar IoT Edge: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtuella datorer
Azure IoT Edge kan köras i virtuella datorer. Att använda en virtuell dator som en IoT Edge enhet är vanligt när kunder vill utöka den befintliga infrastrukturen med Edge Intelligence. Familjen för värddatorns virtuella dator operativ system måste matcha den familj av gäst operativ systemet som används i en moduls behållare. Detta krav är detsamma som när Azure IoT Edge körs direkt på en enhet. Azure IoT Edge är oberoende av den underliggande Virtualization-tekniken och fungerar i virtuella datorer som drivs av plattformar som Hyper-V och vSphere.

<br>
<center>

![Azure IoT Edge i en virtuell dator](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minsta system krav
Azure IoT Edge körs bra på enheter så litet som en Raspberry-Pi3 till maskin vara på server nivå. Att välja rätt maskin vara för ditt scenario beror på de arbets belastningar som du vill köra. Att göra det slutliga enhets beslutet kan vara komplicerat. Du kan dock enkelt starta prototyper av en lösning på traditionella bärbara och Station ära datorer.

Upplevelsen under prototypen hjälper dig att hjälpa dig att hjälpa dig med din slutliga enhets val Frågor som du bör ta hänsyn till: 

* Hur många moduler finns i arbets belastningen?
* Hur många lager bevarar dina modulers behållare resurser?
* I vilket språk har dina moduler skrivits? 
* Hur mycket data kommer dina moduler att bearbetas?
* Behöver dina moduler all specialiserad maskin vara för att påskynda sina arbets belastningar?
* Vilka är de önskade prestanda egenskaperna för din lösning?
* Vilken är din maskin varu budget?
