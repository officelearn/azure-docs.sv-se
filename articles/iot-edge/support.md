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
ms.openlocfilehash: 781884b0973ca42db542f53d452dbaaf7a7f1fa7
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973280"
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

![värd operativ system matchar gäst operativ system](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Nivå 1

De system som anges i följande tabell stöds av Microsoft, antingen allmänt tillgängliga eller i offentlig för hands version, och testas med varje ny version. 

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian-sträckning + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig förhandsversion  |
| [Ubuntu Server 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig förhandsversion |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


De Windows-operativsystem som anges ovan är kraven för enheter som kör Windows-behållare i Windows, vilket är den enda konfiguration som stöds för produktion. Azure IoT Edge installations paket för Windows tillåter användning av Linux-behållare i Windows. den här konfigurationen är dock endast för utveckling och testning. Mer information finns i [använda IoT Edge i Windows för att köra Linux-behållare](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Nivå 2

De system som anges i följande tabell anses vara kompatibla med Azure IoT Edge, men testas eller underhålls inte aktivt av Microsoft.

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7,5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7,5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Vind floden 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Vind floden 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

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
