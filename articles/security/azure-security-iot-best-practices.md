---
title: Internet of Things säkerhetsmetoder | Microsoft Docs
description: Artikeln innehåller en granskad lista över Microsoft Internet of Things Security Best Practices and allmänna rekommendationer.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 9413c0503c1b78550776d1c2f6ab8239205a788b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121633"
---
# <a name="internet-of-things-security-best-practices"></a>Internet of Things säkerhetsmetoder

Att skydda infrastrukturen som Internet of Things (IoT) är ett företag som är viktiga för alla som ingår i IoT-lösningar. På grund av antalet enheter som ingår och distribuerade natur i enheterna effekten en säkerhetshändelse som är relaterade till skada för miljontals IoT-enheter är icke-trivialt och kan ha stor inverkan.

Därför måste en strategi för säkerhet på djupet i IoT-säkerhet. Data måste vara säkra i molnet och som flyttas över privata och offentliga nätverk. Metoderna måste vara på plats för att på ett säkert sätt etablera IoT-enheter själva. Varje lager från enheten till nätverket, till molnet backend-server måste ha stark säkerhetsgarantier.

Metodtips för IoT kan kategoriseras på följande sätt:

* IoT-maskinvarutillverkare eller integrator
* IoT-lösning developer
* IoT-lösning deployer
* Operator för IoT-lösning

Den här artikeln sammanfattas [Internet för saker säkerhetsmetoder](../iot-suite/iot-security-best-practices.md). Läs den här artikeln för mer detaljerad information.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT-maskinvarutillverkare eller integrator

Följ metodtipsen nedan om du är en IoT programvaran eller en maskinvara integrator:

* **Omfång för maskinvara till minimikrav**: maskinvara designen ska innehålla minsta funktioner som krävs för driften av maskinvaran och inget mer. 
* **Uppnå maskinvara som är säkerhetsförsluten och proof**: skapa mekanismer för att identifiera fysiska manipulation av maskinvara, till exempel att öppna luckan, ta bort en del av enheten, osv. 
* **Skapa runt säker maskinvara**: om [kostnad för sålda varor](https://en.wikipedia.org/wiki/Cost_of_goods_sold) tillåta, skapa säkerhetsfunktioner som säker och krypterad lagring och Trusted Platform Module TPM-baserade Start-funktioner.
* **Skydda uppgraderingar**: det är ofrånkomligt att uppgradera inbyggda programvaran under livslängden för enheten.

## <a name="iot-solution-developer"></a>IoT-lösning developer

Följ metodtipsen nedan om du är utvecklare av IoT-lösningen:

* **Följ säker programvara utvecklingsmetodiken**: utveckla säkra program kräver grunden tänka på säkerheten från starten av projektet hela vägen till dess implementering, testning och distribution.
* **Välj programvara med öppen källkod med försiktighet**: ger möjlighet att snabbt utveckla lösningar för programvara med öppen källkod.
* **Integrera med försiktighet**: många av säkerhetsbrister programvara finns på gränsen för bibliotek och API: er. 

## <a name="iot-solution-deployer"></a>IoT-lösning deployer

Följ metodtipsen nedan om du är en IoT-lösning deployer:

* **Distribuera maskinvara på ett säkert sätt**: IoT-distributioner kan kräva maskinvara som distribueras i oskyddade platser, till exempel i offentliga blanksteg eller ej kontrollerade språk.
* **Skydda autentiseringsnycklar**: under distributionen av varje enhet kräver enhets-ID och tillhörande autentiseringsnycklar som genererats av Molntjänsten. Skydda de här nycklarna fysiskt även efter distributionen. Alla komprometterade nyckeln kan användas av en obehörig enhet för att domänresurs en befintlig enhet.

## <a name="iot-solution-operator"></a>Operator för IoT-lösning

Följ metodtipsen nedan om du är operatör IoT-lösning:

* **Hålla system uppdaterade**: Kontrollera att enhetens operativsystem och alla enhetsdrivrutiner uppdateras till de senaste versionerna. 
* **Skydda mot skadlig aktivitet**: om operativsystemet tillåter, placera de senaste funktionerna för skydd mot virus och skadlig på varje enhetsoperativsystem. 
* **Granska ofta**: granskning IoT-infrastruktur för säkerhetsrelaterade problem är nyckeln när du åtgärdar säkerhetsincidenter.
* **Fysiskt skydda IoT-infrastruktur**: sämsta attacker mot IoT-infrastruktur startas med hjälp av fysisk åtkomst till enheter.
* **Skydda molnautentiseringsuppgifter**: cloud-autentiseringsuppgifterna som används för att konfigurera och driva en IoT-distribution är eventuellt det enklaste sättet att få åtkomst och angripa ett IoT-system. 

