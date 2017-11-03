---
title: "Internet av saker säkerhetsmetoder | Microsoft Docs"
description: "Artikeln innehåller en granskad lista över Microsoft Internet saker säkerhetsmetoder och allmänna rekommendationer."
services: security
documentationcenter: na
author: TomShinder
manager: StevenPo
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: yurid
ms.openlocfilehash: 8efc0053458e338ac1afe98d9ce970c1d5cbfa81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="internet-of-things-security-best-practices"></a>Internet av saker säkerhetsmetoder
Skydda Sakernas Internet (IoT)-infrastruktur är en kritisk företag alla som sysslar med IoT-lösningar. På grund av antalet enheter som är inblandade och distribuerade uppbyggnad enheterna effekten en säkerhetshändelse relaterade till skada för miljontals IoT-enheter är icke-trivial och kan ha stor inverkan.

Därför säkerhetsbehov IoT säkerhet ingående-information. Data behöver vara säkert i molnet och som flyttas över privata och offentliga nätverk. Metoder måste vara på plats för att etablera sig IoT-enheter på ett säkert sätt. Varje lager från enhet till nätverket, till molnet backend-behöver garantier för hög säkerhet.

Metodtips för IoT kan kategoriseras på följande sätt:

* IoT-maskinvarutillverkaren eller integrator
* IoT-lösningen utvecklare
* Distribueraren för IoT-lösning
* IoT-lösningen operator

Den här artikeln sammanfattar [Internet av saker säkerhetsmetoder](../iot-suite/iot-security-best-practices.md). Läs artikeln mer detaljerad information.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT-maskinvarutillverkaren eller integrator
Följ metodtips nedan om du är en IoT programvaran eller en integrator maskinvara:

* **Omfång maskinvara minimikrav**: maskinvara designen ska innehålla minsta funktioner som krävs för åtgärden för maskinvara, och inget mer. 
* **Kontrollera maskinvara säkerhetsförsluten bevis**: skapa mekanismer för att upptäcka fysiska manipulering av maskinvara, till exempel öppna luckan, ta bort en del av enheten, osv. 
* **Skapa runt säker maskinvara**: om [kostnad för sålda varor](https://en.wikipedia.org/wiki/Cost_of_goods_sold) tillåta, skapa säkerhetsfunktioner, till exempel säker och krypterad lagring och Trusted Platform Module TPM-baserade Start-funktioner.
* **Göra uppgraderingar säker**: uppgradera firmware under livslängden för enheten inte är.

## <a name="iot-solution-developer"></a>IoT-lösningen utvecklare
Följ metodtips nedan om du utvecklar en IoT-lösningen:

* **Följ säker software development metoder**: utveckla säkra program kräver grunden tänka säkerhet från starten av projektet ända till dess implementering, testning och distribution.
* **Välj programvara med öppen källkod med försiktighet**: programvara med öppen källkod ger en möjlighet att snabbt utveckla lösningar.
* **Integrera med försiktighet**: många säkerhetsbrister programvara finns på gränsen för bibliotek och API: er. 

## <a name="iot-solution-deployer"></a>Distribueraren för IoT-lösning
Följ metodtips nedan om du är en IoT-lösningen deployer:

* **Distribuera maskinvara på ett säkert sätt**: IoT-distributioner kan kräva maskinvara som distribueras i osäkra platser, exempelvis offentliga blanksteg eller oövervakade språk.
* **Skydda autentiseringsnycklar**: under distribution varje enhet kräver enhets-ID och tillhörande autentiseringsnycklar som genererats av Molntjänsten. Skydda de här nycklarna fysiskt även efter distributionen. Alla avslöjade nyckeln kan användas av en obehörig enhet till ut som en befintlig enhet.

## <a name="iot-solution-operator"></a>IoT-lösningen operator
Följ metodtips nedan om du är operatör IoT-lösningen:

* **Kontinuerligt system**: Kontrollera enhetens operativsystem och alla enhetsdrivrutiner uppdateras till de senaste versionerna. 
* **Skydda mot skadlig aktivitet**: om operativsystemet tillåter placera de senaste funktionerna för skydd mot virus och skadlig kod på varje enhets operativsystem. 
* **Granska ofta**: granskning IoT infrastruktur för säkerhetsrelaterade problem är nyckel när svara på säkerhetsincidenter.
* **Skydda fysiskt IoT infrastruktur**: sämsta säkerhetsattacker mot IoT infrastruktur startas med fysisk åtkomst till enheter.
* **Skydda molnet autentiseringsuppgifter**: molnet autentiseringsuppgifter som används för att konfigurera och använda en IoT-distribution är eventuellt det enklaste sättet att komma åt och en IoT-dator. 

