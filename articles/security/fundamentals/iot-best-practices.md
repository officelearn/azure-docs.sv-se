---
title: Rekommenderade säkerhets metoder för Sakernas Internet | Microsoft Docs
description: Artikeln innehåller en granskad lista över rekommenderade metoder för Microsoft Sakernas Internet säkerhet och allmänna rekommendationer.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 14f1905b1acd60acdf7b3dad0686e3fa34629953
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727057"
---
# <a name="internet-of-things-security-best-practices"></a>Rekommenderade säkerhets metoder för Sakernas Internet

Att skydda Sakernas Internet-infrastrukturen (IoT) är ett kritiskt företag för alla som är involverade i IoT-lösningar. På grund av antalet enheter som är inblandade och den distribuerade typen av enheter, är påverkan på en säkerhets händelse som är relaterad till kompromisser i miljon tals IoT-enheter icke-trivial och kan ha omfattande påverkan.

Av den anledningen behöver IoT-säkerhet en säkerhets djupgående metod. Data måste vara säkra i molnet och flyttas över privata och offentliga nätverk. Metoder måste finnas för att kunna tillhandahålla själva IoT-enheterna på ett säkert sätt. Varje lager, från enhet, till nätverk, till moln server dels behöver starka säkerhets garantier.

Bästa praxis för IoT kan kategoriseras på följande sätt:

* IoT-maskin varu tillverkare eller Integrator
* IoT Solution-utvecklare
* IoT-lösning, distribution
* IoT Solution-operator

I den här artikeln sammanfattas [rekommenderade säkerhets metoder för Sakernas Internet säkerhet](/azure/iot-fundamentals/iot-security-best-practices). Mer detaljerad information finns i artikeln.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT-maskin varu tillverkare eller Integrator

Följ de bästa metoderna nedan om du är en IoT-maskin varu tillverkning eller en maskin varu Integrator:

* **Omfattnings maskin vara för minimi krav**: maskin varu designen bör omfatta minimi kraven för maskin vara och inget annat. 
* **Gör maskin varu manipulering**: skapa i mekanismer för att identifiera fysisk manipulering av maskin vara, till exempel att öppna enhets locket, ta bort en del av enheten osv. 
* **Bygg en säker maskin vara**: om [KSV](https://en.wikipedia.org/wiki/Cost_of_goods_sold) -tillåtelse är att bygga säkerhetsfunktioner som säker och krypterad lagring och Trusted Platform Module TPM-baserad start funktion.
* **Gör uppgraderingar säkra**: den inbyggda program varan under enhetens livs längd är oundviklig.

## <a name="iot-solution-developer"></a>IoT Solution-utvecklare

Följ rekommendationerna nedan om du är en IoT-lösning utvecklare:

* **Använd säker program utvecklings metod**: att utveckla säkra program kräver att du funderar på säkerheten från projektets början till dess implementering, testning och distribution.
* **Välj program med öppen källkod med försiktighet**: program vara med öppen källkod ger möjlighet att snabbt utveckla lösningar.
* **Integrera med försiktighet**: många av program varu säkerhets fel finns på gränserna för bibliotek och API: er. 

## <a name="iot-solution-deployer"></a>IoT-lösning, distribution

Följ de rekommenderade metoderna nedan om du är en IoT-lösning som distribuerar:

* **Distribuera maskin vara på ett säkert sätt**: IoT-distributioner kan kräva att maskin vara distribueras på osäkra platser, till exempel i offentliga utrymmen eller i ej övervakade språk.
* **Se**till att nycklarna är säkra: under distributionen kräver varje enhet enhets-ID: n och associerade autentiseringsinställningar som genereras av moln tjänsten. Behåll dessa nycklar fysiskt säkra även efter distributionen. Eventuella komprometterade nycklar kan användas av en skadlig enhet för att maskeras som en befintlig enhet.

## <a name="iot-solution-operator"></a>IoT Solution-operator

Följ rekommendationerna nedan om du är en IoT Solution-operator:

* **Håll systemen uppdaterade**: kontrol lera att enhetens operativ system och alla enhets driv rutiner har uppdaterats till de senaste versionerna. 
* **Skydda mot skadlig aktivitet**: om operativ systemet tillåter placerar du de senaste antivirus-och antiskadligt-funktionerna på varje enhets operativ system. 
* **Granska ofta**: granskning av IoT-infrastruktur för säkerhetsrelaterade problem är nyckel vid svar på säkerhets incidenter.
* **Skydda IoT-infrastrukturen fysiskt**: de värsta säkerhets angrepp mot IoT-infrastrukturen startas med fysisk åtkomst till enheter.
* **Skydda autentiseringsuppgifter för moln**: autentiseringsuppgifter för moln autentisering som används för att konfigurera och använda en IoT-distribution är förmodligen det enklaste sättet att få åtkomst till och kompromettera ett IoT-system. 

