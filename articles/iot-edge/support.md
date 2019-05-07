---
title: Stöd för operativsystem, container motorer – Azure IoT Edge | Microsoft Docs
description: Lär dig vilka operativsystem kan köra Azure IoT Edge-daemon och runtime och stöds behållare motorer för dina produktionsenheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1db999f5d3172734311ecc7dbec72214a1e07e91
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65137812"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge stöds system

Det finns en mängd olika sätt att söka efter support för Azure IoT Edge-produkten.

**Rapportering buggar** – flesta utveckling som ingår i Azure IoT Edge-produkten sker i IoT Edge open source-projektet. Buggar kan rapporteras på den [sidan ärenden](https://github.com/azure/iotedge/issues) i projektet. Korrigeringar hamna snabbt från projektet i produktuppdateringar.

**Microsofts kundsupport team** – användare som har en [supportavtal](https://azure.microsoft.com/support/plans/) kan interagera med Microsofts kundsupport-teamet genom att skapa ett supportärende direkt från den [Azure-portalen](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funktionen begäranden** – The Azure IoT Edge-produkten spårar funktionsförfrågningar via produktens [User Voice-sidan](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Behållare-motorer
Azure IoT Edge måste en motor för behållaren att starta moduler eftersom de har implementerats som behållare. Microsoft tillhandahåller en motor för behållare, moby-motorn att uppfylla detta krav. Den är baserad på öppen källkod-projekt Moby. Docker CE- och Docker EE är andra populära container-motorer. De också är baserade på öppen källkod-projekt Moby och är kompatibla med Azure IoT Edge. Microsoft tillhandahåller support efter bästa förmåga för system med hjälp av de motorerna för behållaren. Microsoft har dock inte att leverera korrigeringar för problem i dem. Därför rekommenderar Microsoft med moby-motor på produktionssystemen.

<br>
<center>

![Moby som behållare runtime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operativsystem
Azure IoT Edge som körs på de flesta operativsystem som kan köra behållare. men stöds alla dessa system lika inte. Operativsystem är grupperade i nivåer som representerar supportnivå de kan förvänta sig.
* Nivå 1-system kan betraktas som officiellt stöds. Nivå 1-system använder Microsoft:
    * har det här operativsystemet i automatiska tester
    * tillhandahåller installationspaket för dem.
* Nivå 2-system kan betraktas som kompatibelt med Azure IoT Edge och relativt lätt kan användas. För nivå 2-datorer:
    * Microsoft har gjort ad hoc-testning på plattformarna eller känner av en partner som kör Azure IoT Edge-plattformen
    * Installationspaket för andra plattformar fungerar på följande plattformar
    
Familjen av värdens operativsystem måste alltid matcha familjen av gäst-OS som används i en modul behållare. Med andra ord, kan du bara använda Linux-behållare på Linux och Windows-behållare på Windows. När du använder Windows, endast processen isolerade behållare stöds separat inte Hyper-V behållare.  

<br>
<center>

![Värdoperativsystem matchar gäst-OS](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Nivå 1
Allmänt tillgänglig

| Operativsystem | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian stretch | Nej | Ja|
| Ubuntu Server 16.04 | Ja | Nej |
| Ubuntu Server 18.04 | Ja | Nej |
| Windows 10 IoT Enterprise, build 17763 | Ja | Nej |

Offentlig förhandsversion

| Operativsystem | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core, build 17763 | Ja | Nej |
| Windows Server 2019, build 17763 | Ja | Nej |
| Windows Server IoT 2019, build 17763 | Ja | Nej |

Windows-operativsystemen som anges ovan är kraven för enheter som kör Windows-behållare på Windows. Den här konfigurationen är det enda konfigurationen som stöds för produktion. Azure IoT Edge-paket för Windows kan använda Linux-behållare på Windows; den här konfigurationen är dock endast för utveckling och testning. Användning av Linux-behållare i Windows är inte en konfiguration som stöds för produktion. Valfri version av Windows 10-version 14393 eller senare och Windows Server 2016 eller senare kan användas för det här scenariot för utveckling.

### <a name="tier-2"></a>Nivå 2

| Operativsystem | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Ja | Ja |
| Debian 8 | Ja | Ja |
| Debian 9 | Ja | Ja |
| RHEL 7.5 | Ja | Ja |
| Ubuntu 18.04 | Ja | Ja |
| Ubuntu 16.04 | Ja | Ja |
| Vind floden 8 | Ja | Nej |
| Yocto | Ja | Nej |


## <a name="virtual-machines"></a>Virtuella datorer
Azure IoT Edge kan köras på virtuella datorer. Med hjälp av en virtuell dator som en IoT Edge är enhet vanlig när kunder som vill förbättra befintliga infrastruktur med gränsintelligens. Familjen av värdoperativsystemet för virtuell dator måste matcha familjen av gäst-OS som används i en modul behållare. Det här kravet är samma som när du kör Azure IoT Edge direkt på en enhet. Azure IoT Edge är oberoende av den underliggande virtualiseringstekniken och fungerar på virtuella datorer som drivs av plattformar som Hyper-V och vSphere.

<br>
<center>

![Azure IoT Edge på en virtuell dator](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minsta systemkrav
Azure IoT Edge fungerar perfekt på enheter som är så litet som en Raspberry Pi3 att servermaskinvaran i företagsklass. Välja rätt maskinvara för ditt scenario beror på de arbetsbelastningar som du vill köra. Beslutet om den sista enhet kan vara komplicerat; Du kan dock enkelt starta prototyper en lösning på traditionella bärbara och stationära datorer.

Upplev medan prototyper kommer att guida valet av sista enhet. Frågor som du bör överväga är: 

* Hur många moduler finns i din arbetsbelastning?
* Hur många lager delar dina moduler behållare?
* I vilket språk skrivs dina moduler? 
* Hur mycket data kommer dina moduler kan behandla?
* Behöver dina moduler någon särskild maskinvara för att påskynda sina arbetsbelastningar?
* Vilka är de önskade prestandaegenskaperna för din lösning?
* Vad är en mer hållbar budget med maskinvara?
