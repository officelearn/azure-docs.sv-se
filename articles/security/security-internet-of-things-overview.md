---
title: Skydda dina Internet saker (IoT) i Azure | Microsoft Docs
description: " Azure internet av saker (IoT) services erbjuder en mängd funktioner. Den här artikeln hjälper dig att förstå hur du skyddar din IoT-lösningar i Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: d5c1cb22fdfe59bd8409f9595b2fa4c3a0df771e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641245"
---
# <a name="internet-of-things-security-overview"></a>Översikt över säkerheten i Sakernas Internet
Azure internet av saker (IoT) services erbjuder en mängd funktioner. Med dessa tjänster i företagsklass kan du:

* Samla in data från enheter
* Analysera dataströmmar i rörelse
* Lagra och skicka frågor mot stora datamängder
* Visualisera både realtidsdata och historiska data
* Integrera med back office-system

Att tillhandahålla dessa funktioner, Azure IoT-lösningen acceleratorer paket tillsammans flera Azure-tjänster med anpassade tillägg som förkonfigurerade lösningar. Dessa förkonfigurerade lösningar är grundläggande implementeringar av vanliga IoT-lösningsmönster som kan minska den tid det tar att leverera IoT-lösningar. Med IoT software development Kit kan du anpassa och utöka dessa lösningar för att uppfylla dina egna behov. Du kan också använda dessa lösningar som exempel eller mallar när du utvecklar nya IoT-lösningar.

Azure IoT-Lösningsacceleratorer är kraftfulla lösningar för din IoT-behov. Det är dock upmost viktigt att IoT-lösningar är utformade med säkerhet i åtanke från början. På grund av IoT-enheter finns så många bli en säkerhetsincident snabbt en omfattande händelse med betydande konsekvenser.

Vi har följande information för att hjälpa dig att förstå hur du skyddar din IoT-lösningar.

## <a name="security-architecture"></a>Säkerhetsarkitektur
När ett system utformas, är det viktigt att förstå potentiella hot på systemet och lägga till lämpliga försvar därför eftersom systemet är utformad och konstruerad. Det är viktigt att utforma produkten från början med säkerhet i åtanke eftersom förstå hur en angripare kan vara att en dator gör att lämpliga åtgärder finns på plats från början.

Du kan lära dig om IoT-säkerhetsarkitekturen genom att läsa [Internet av saker säkerhetsarkitekturen](../iot-accelerators/iot-security-architecture.md).

Den här artikeln beskrivs i följande avsnitt:

* [Säkerhet börjar med en Hotmodell](../iot-accelerators/iot-security-architecture.md#security-starts-with-a-threat-model)
* [Säkerhet i IoT](../iot-accelerators/iot-security-architecture.md#security-in-iot)
* [Hot Modeling referens för Azure IoT-arkitektur](../iot-accelerators/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Säkerhet från grunden
IoT utgör unika säkerhet, sekretess och kompatibilitet utmaningar för företag över hela världen. Till skillnad från traditionella cyber teknik där problemen omfångsfasen handlar om programvara och hur den har implementerats gäller IoT vad som händer när cyber och fysiska arbetslivet Konvergera. Skydda IoT-lösningar kräver att säkerställa säker etablering av enheter, säker anslutning mellan dessa enheter och molnet och säkert dataskydd i molnet under bearbetning och lagring. Arbeta mot dessa funktioner är dock begränsad resurs enheter, geografisk fördelning av distributioner och många enheter i en lösning.

Du kan lära dig att hantera säkerhet i dessa områden genom att läsa [Sakernas Internet security från grunden](../iot-accelerators/securing-iot-ground-up.md).

Här beskrivs i följande avsnitt:

* [Säker infrastruktur från grunden](../iot-accelerators/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – säker IoT-infrastruktur för ditt företag](../iot-accelerators/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Metodtips
Skydda en IoT-infrastruktur kräver en rigorösa security-strategi. Från att skydda data i molnet, skydda dataintegriteten som överförs via det offentliga internet, till att etablera enheter på ett säkert sätt, skapar varje lager större säkerhet säkerhet i hela infrastrukturen.

Lär du dig i Sakernas Internet security bästa praxis genom att läsa [Sakernas Internet säkerhetsmetoder](../iot-accelerators/iot-security-best-practices.md).

Här beskrivs i följande avsnitt:

* [IoT maskinvara tillverkare/integrator](../iot-accelerators/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [IoT-lösningen utvecklare](../iot-accelerators/iot-security-best-practices.md#iot-solution-developer)
* [Distribueraren för IoT-lösning](../iot-accelerators/iot-security-best-practices.md#iot-solution-deployer)
* [IoT-lösningen operator](../iot-accelerators/iot-security-best-practices.md#iot-solution-operator)
