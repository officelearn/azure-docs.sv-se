---
title: Skydda din Sakernas Internet (IoT) i Azure | Microsoft Docs
description: " Azure sakernas internet (IoT)-tjänster erbjuder en rad olika funktioner. Den här artikeln hjälper dig att förstå hur du skyddar dina IoT-lösningar i Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 670fcc5e92cdb9937026d91ee85b7e8e856a3025
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727037"
---
# <a name="internet-of-things-security-overview"></a>Översikt över Sakernas Internet säkerhet
Azure sakernas internet (IoT)-tjänster erbjuder en rad olika funktioner. Med dessa tjänster i företagsklass kan du:

* Samla in data från enheter
* Analysera dataströmmar i rörelse
* Lagra och skicka frågor mot stora datamängder
* Visualisera både realtidsdata och historiska data
* Integrera med back office-system

För att leverera dessa funktioner är Azure IoT Solution Accelerators-paketet samman flera Azure-tjänster med anpassade tillägg som förkonfigurerade lösningar. Dessa förkonfigurerade lösningar är grundläggande implementeringar av vanliga IoT-lösningsmönster som kan minska den tid det tar att leverera IoT-lösningar. Med IoT Software Development Kits kan du anpassa och utöka dessa lösningar efter dina egna behov. Du kan också använda dessa lösningar som exempel eller mallar när du utvecklar nya IoT-lösningar.

Azure IoT Solution Accelerators är kraftfulla lösningar för dina IoT-behov. Men det är av största vikt att dina IoT-lösningar är utformade med säkerhet i åtanke från början. På grund av Sheer antal IoT-enheter kan alla säkerhets incidenter snabbt bli en omfattande händelse med betydande konsekvenser.

Vi har följande information för att hjälpa dig att förstå hur du skyddar dina IoT-lösningar.

## <a name="security-architecture"></a>Säkerhetsarkitektur
När du designar ett system är det viktigt att förstå de potentiella hoten mot systemet och lägga till lämpliga försvar i enlighet med detta, eftersom systemet är utformat och konstruerat. Det är viktigt att utforma produkten från början med säkerhet i åtanke eftersom du förstår hur en angripare kan kompromettera ett system och se till att lämpliga åtgärder är på plats från början.

Du kan lära dig mer om IoT-säkerhetsarkitekturen genom att läsa [sakernas internet säkerhets arkitektur](/azure/iot-fundamentals/iot-security-architecture).

I den här artikeln beskrivs följande avsnitt:

* [Säkerhet börjar med en hot modell](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Säkerhet i IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Hot modellering referens arkitektur för Azure IoT](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>Säkerhet från grunden
IoT utgör unika utmaningar för säkerhet, sekretess och efterlevnad för företag över hela världen. Till skillnad från traditionell cyberhot-teknik där problemen kretsar kring program vara och hur de implementeras, berör IoT vad som händer när cyberhot och det fysiska världar konvergerar. Att skydda IoT-lösningar kräver säker etablering av enheter, säker anslutning mellan enheterna och molnet och skyddar data skyddet i molnet under bearbetning och lagring. Att arbeta med sådana funktioner är dock resurs begränsade enheter, geografisk distribution av distributioner och många enheter i en lösning.

Du kan lära dig hur du hanterar säkerheten i dessa områden genom att läsa [Sakernas Internet säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up).

I artikeln beskrivs följande avsnitt:

* [Skydda infrastrukturen från grunden](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – säker IoT-infrastruktur för din verksamhet](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Metodtips
Att skydda en IoT-infrastruktur kräver en omfattande säkerhets djupgående strategi. Att skydda data i molnet och skydda data integriteten under överföring via det offentliga Internet, för att på ett säkert sätt allokera enheter, skapar varje lager större säkerhets garanti i den övergripande infrastrukturen.

Du kan lära dig mer om rekommenderade säkerhets metoder för Sakernas Internet genom att läsa [metod tips för Sakernas Internet säkerhet](/azure/iot-fundamentals/iot-security-best-practices).

I artikeln beskrivs följande avsnitt:

* [IoT-maskin varu tillverkare/Integrator](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [IoT Solution-utvecklare](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [IoT-lösning, distribution](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [IoT Solution-operator](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
