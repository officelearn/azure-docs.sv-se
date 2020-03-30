---
title: Operativsystem som stöds, behållarmotorer - Azure IoT Edge
description: Lär dig vilka operativsystem som kan köra Azure IoT Edge-demonen och körningen och behållarmotorer som stöds för dina produktionsenheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536946"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge-stödda system

Den här artikeln innehåller information om vilka system och komponenter som stöds av IoT Edge, antingen officiellt eller i förhandsversion.

Om du får problem när du använder Azure IoT Edge-tjänsten finns det flera sätt att söka support. Prova någon av följande kanaler för support:

**Rapportera fel** – Den största delen av utvecklingen som går till Azure IoT Edge-produkten sker i IoT Edge open source-projektet. Buggar kan rapporteras på [ärendesidan](https://github.com/azure/iotedge/issues) i projektet. Korrigeringar tar sig snabbt från projektet till produktuppdateringar.

**Microsofts kundtjänst** – Användare som har ett [supportabonnemang](https://azure.microsoft.com/support/plans/) kan engagera Microsofts kundtjänst genom att skapa en supportbiljett direkt från [Azure-portalen](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funktionsförfrågningar** – Azure IoT Edge-produkten spårar funktionsförfrågningar via produktens [användarröstsida](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Containermotorer

Azure IoT Edge-moduler implementeras som behållare, så IoT Edge behöver en behållarmotor för att starta dem. Microsoft tillhandahåller en containermotor, moby-motor, för att uppfylla detta krav. Denna containermotor är baserad på Moby open-source-projektet. Docker CE och Docker EE är andra populära containermotorer. De är också baserade på Moby open-source-projektet och är kompatibla med Azure IoT Edge. Microsoft ger bäst koll på system som använder dessa behållarmotorer. Microsoft kan dock inte skicka korrigeringar för problem i dem. Därför rekommenderar Microsoft att du använder moby-engine på produktionssystem.

<br>
<center>

![Moby-motorn som containerkörning](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operativsystem

Azure IoT Edge körs på de flesta operativsystem som kan köra behållare. Alla dessa system stöds dock inte på samma sätt. Operativsystem grupperas i nivåer som representerar nivån på supportanvändare kan förvänta sig.

* Nivå 1-system stöds. För nivå 1-system, Microsoft:
  * har detta operativsystem i automatiserade tester
  * tillhandahåller installationspaket för dem
* Nivå 2-system är kompatibla med Azure IoT Edge och kan användas relativt enkelt. För nivå 2-system:
  * Microsoft har gjort informella tester på plattformarna eller känner till en partner som framgångsrikt kör Azure IoT Edge på plattformen
  * Installationspaket för andra plattformar kan fungera på dessa plattformar

Familjen till värdoperativsystemet måste alltid matcha familjen till gästoperativsystemet som används i en moduls behållare. Med andra ord kan du bara använda Linux-behållare på Linux- och Windows-behållare på Windows. När du använder Windows stöds endast process isolerade behållare, inte Hyper-V-isolerade behållare.  

<br>
<center>

![Värd-OS matchar gäst-OS](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Nivå 1

De system som anges i följande tabell stöds av Microsoft, antingen allmänt tillgängliga eller i offentlig förhandsversion, och testas med varje ny utgåva. 

| Operativsystem | AMD64 (på andra sätt) | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig förhandsgranskning  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig förhandsgranskning |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), bygga 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), bygga 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), bygg 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), bygg 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

De Windows-operativsystem som anges ovan är kraven för enheter som kör Windows-behållare i Windows, vilket är den enda konfiguration som stöds för produktion. Azure IoT Edge-installationspaketen för Windows tillåter användning av Linux-behållare i Windows. Den här konfigurationen är dock endast för utveckling och testning. Mer information finns i [Använda IoT Edge i Windows för att köra Linux-behållare](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Nivå 2

De system som anges i följande tabell anses vara kompatibla med Azure IoT Edge, men testas inte aktivt eller underhålls av Microsoft.

| Operativsystem | AMD64 (på andra sätt) | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Inbäddade Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Vind Flod 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10-system, inklusive Raspian Buster, använder en version av OpenSSL som IoT Edge inte stöder. Använd följande kommando för att installera en tidigare version innan du installerar IoT Edge:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Versioner

IoT Edge-versionstillgångar och versionsfakturor är tillgängliga på sidan [azure-iotedge-versioner.](https://github.com/Azure/azure-iotedge/releases) Det här avsnittet återspeglar information från dessa viktig information som hjälper dig att visualisera komponenterna i varje version enklare.

IoT Edge-komponenter kan installeras eller uppdateras individuellt och är bakåtkompatibla med komponenter från äldre versioner. I följande tabell visas de komponenter som ingår i varje utgåva:

| Frisläpp   | Säkerhetsdemon  | Edge-nav<br>Kantagent | Libiothsm (libjothsm) | Moby  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

IoT Edge använder Microsoft.Azure.Device.Client SDK. Mer information finns i [Azure IoT C# SDK GitHub repo](https://github.com/Azure/azure-iot-sdk-csharp) eller [Azure SDK för .NET-referensinnehåll](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet). Följande lista visar den version av klienten SDK som varje utgåva testas mot:

* **IoT Edge 1.0.9**: Klient SDK 1.21.1
* **IoT Edge 1.0.8**: Klient SDK 1.20.3
* **IoT Edge 1.0.7**: Klient SDK 1.20.1
* **IoT Edge 1.0.6**: Klient SDK 1.17.1
* **IoT Edge 1.0.5**: Klient SDK 1.17.1

## <a name="virtual-machines"></a>Virtuella datorer

Azure IoT Edge kan köras i virtuella datorer. Det är vanligt att använda en virtuell dator som en IoT Edge-enhet när kunder vill utöka befintlig infrastruktur med kantinformation. Familjen till värd-VM-operativsystemet måste matcha familjen för gästoperativsystemet som används i en moduls behållare. Det här kravet är detsamma som när Azure IoT Edge körs direkt på en enhet. Azure IoT Edge är agnostiker för den underliggande virtualiseringstekniken och fungerar i virtuella datorer som drivs av plattformar som Hyper-V och vSphere.

<br>
<center>

![Azure IoT Edge i en virtuell dator](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimikrav på system

Azure IoT Edge körs utmärkt på enheter så små som en Raspberry Pi3 till server grade hårdvara. Att välja rätt maskinvara för ditt scenario beror på vilka arbetsbelastningar du vill köra. Att fatta det slutliga enhetsbeslutet kan vara komplicerat; Du kan dock enkelt börja prototypera en lösning på traditionella bärbara eller stationära datorer.

Upplev medan prototyper hjälper dig att styra ditt slutliga enhetsval. Frågor som du bör tänka på är:

* Hur många moduler finns i din arbetsbelastning?
* Hur många lager delar modulernas behållare?
* På vilket språk är dina moduler skrivna?
* Hur mycket data kommer dina moduler att bearbeta?
* Behöver dina moduler någon specialiserad maskinvara för att påskynda sina arbetsbelastningar?
* Vilka är de önskade prestandaegenskaperna hos din lösning?
* Vad är din maskinvarubudget?
