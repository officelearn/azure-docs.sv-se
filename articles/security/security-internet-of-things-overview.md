---
title: Skydda dina Internet of Things (IoT) i Azure | Microsoft Docs
description: " Azure Internet internet of things (IoT)-tjänster erbjuder ett brett utbud av funktioner. Den här artikeln hjälper dig att skydda dina IoT-lösningar i Azure. "
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
ms.openlocfilehash: 41e422d0808cafb45b182c5f0a6bb7176a35516f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35758628"
---
# <a name="internet-of-things-security-overview"></a>Översikt över Internet of Things-säkerhet
Azure Internet internet of things (IoT)-tjänster erbjuder ett brett utbud av funktioner. Med dessa tjänster i företagsklass kan du:

* Samla in data från enheter
* Analysera dataströmmar i rörelse
* Lagra och skicka frågor mot stora datamängder
* Visualisera både realtidsdata och historiska data
* Integrera med back office-system

Leverera dessa funktioner, Azure IoT-lösningspaketet acceleratorer samman flera Azure-tjänster med anpassade tillägg som förkonfigurerade lösningar. Dessa förkonfigurerade lösningar är grundläggande implementeringar av vanliga IoT-lösningsmönster som kan minska den tid det tar att leverera IoT-lösningar. Med programutvecklingspaketen kan du anpassa och utöka dessa lösningar efter dina egna behov. Du kan också använda dessa lösningar som exempel eller mallar när du utvecklar nya IoT-lösningar.

Azure IoT-Lösningsacceleratorer är kraftfulla lösningar för dina IoT-behov. Det är dock av upmost betydelse att dina IoT-lösningar har utformats med säkerhet i åtanke från starten. På grund av IoT-enheter finns så många kan blivit alla säkerhetsincidenter snabbt en omfattande händelse med betydande konsekvenser.

Vi har följande information för att hjälpa dig att skydda dina IoT-lösningar.

## <a name="security-architecture"></a>Säkerhetsarkitektur
När du designar ett system, är det viktigt att förstå de potentiella hot på systemet och lägga till lämpliga försvar, eftersom systemet är utformad och byggts. Det är viktigt att utforma produkten från början med säkerhet i åtanke eftersom förstå hur en angripare kan eventuellt att angripa en dator gör att lämpliga åtgärder finns på plats från början.

Du kan lära dig om IoT-säkerhetsarkitekturen genom att läsa [Internet of Things Security Architecture](/azure/iot-fundamentals/iot-security-architecture).

Den här artikeln beskrivs i följande avsnitt:

* [Security börjar med en Hotmodell](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Säkerhet i IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Threat Modeling Referensarkitektur för Azure IoT](/azure/iot-fundamentals/iot-security-architecture#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Säkerhet från grunden
I IoT utgör unika utmaningar för företag över hela världen som säkerhet, sekretess och efterlevnad. Till skillnad från traditionella cyberhot teknik där problemen kretsar kring programvara och hur det implementeras, gäller IoT vad som händer när cyberhot och de fysiska världarna Konvergera. Skydda IoT-lösningar kräver att säkerställa säker etablering av enheter, säkra anslutningar mellan dessa enheter och molnet och säkert dataskydd i molnet under bearbetning och lagring. Arbeta mot sådana funktioner, men är begränsad resurs enheter, geografisk fördelning av distributioner och många enheter i en lösning.

Du kan lära dig hur du hanterar säkerheten inom följande områden genom att läsa [Internet of Things-säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up).

Här beskrivs i följande avsnitt:

* [Säker infrastruktur från grunden](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – säker IoT-infrastruktur för ditt företag](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Metodtips
Skydda en IoT-infrastruktur kräver en rigorösa säkerhet – på djupet. Från skydd av data i molnet, som skyddar dataintegriteten som överförs via det offentliga internet, till att etablera enheter på ett säkert sätt, skapar varje lager större säkerhetskontroll i hela infrastrukturen.

Du kan lära dig om IOT-säkerhet bästa praxis genom att läsa [Internet of Things säkerhetsmetoder](/azure/iot-fundamentals/iot-security-best-practices).

Här beskrivs i följande avsnitt:

* [IoT maskinvara tillverkare/integrator](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [IoT-lösning developer](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [IoT-lösning deployer](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [Operator för IoT-lösning](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
