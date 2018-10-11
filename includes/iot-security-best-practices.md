---
title: ta med fil
description: ta med fil
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b870be792da9a6646a7854bc17b7e33fb11d7b0c
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49070176"
---
# <a name="internet-of-things-security-best-practices"></a>Metodtips för IOT-säkerhet

Skydda en infrastruktur för Internet of Things (IoT) kräver en rigorösa säkerhet – på djupet. Den här strategin kräver att du kan skydda data i molnet, skydda dataintegriteten som överförs via det offentliga internet och etablera enheter på ett säkert sätt. Varje lager bygger större säkerhetskontroll i hela infrastrukturen.

## <a name="secure-an-iot-infrastructure"></a>Skydda en IoT-infrastruktur

Den här strategin för säkerhet på djupet kan utvecklas och köras med aktiv medverkan av olika aktörer med tillverkning, utveckling och distribution av IoT-enheter och infrastruktur. Nedan följer en beskrivning av dessa spelare.

* **IoT maskinvara tillverkare/integrator**: dessa spelare är oftast tillverkare av IoT-maskinvara som distribueras integrerare att sätta samman maskinvara från olika tillverkare eller leverantörer som tillhandahåller maskinvara för en IoT-distribution som tillverkas eller integrerad av andra leverantörer.

* **IoT-lösning developer**: utvecklingen av en IoT-lösning normalt görs av en för lösningsutvecklare. Den här utvecklaren kan del av en interna team eller en systemintegrerare (SI) som specialiserar sig på den här aktiviteten. IoT-lösning utvecklare kan utveckla olika komponenter i IoT-lösning från grunden, integrera olika startklara och öppen källkod-komponenter eller införa Lösningsacceleratorer med mindre anpassning.

* **IoT-lösning deployer**: efter IoT-lösning utvecklas, den måste distribueras i fältet. Den här processen innefattar distribution av maskinvara, sammankoppling av enheter och distribution av lösningar i maskinvaruenheter eller molnet.

* **IoT-lösning operatorn**: efter IoT-lösningen har distribuerats, det krävs långsiktig drift, övervakning, uppgraderingar och underhåll. Dessa uppgifter kan göras av en intern grupp som består av information teknikexperter, maskinvara åtgärder och underhåll team och domän specialister som övervakar rätt beteendet för övergripande IoT-infrastruktur.

Avsnitten som följer ger bästa praxis för var och en av dessa spelare för att utveckla, distribuera och driva en säker IoT-infrastruktur.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT maskinvara tillverkare/integrator

Här följer några Metodtips för IoT-maskinvarutillverkare och integrerare maskinvara.

* **Omfång för maskinvara till minimikrav**: maskinvara designen ska innehålla de minsta funktioner som krävs för åtgärden för maskinvara, och inget mer. Ett exempel är att inkludera USB-portar endast om det behövs för driften av enheten. Dessa funktioner öppnar du enheten för oönskade angreppsmetoder som bör undvikas.

* **Uppnå maskinvara som är säkerhetsförsluten och proof**: skapa mekanismer för att identifiera fysiska manipulering, som att öppna av luckan eller ta bort en del av enheten. Dessa Förvanska signaler kan vara en del av dataströmmen överförs till molnet, vilket kan operatörerna av dessa händelser.

* **Skapa runt säker maskinvara**: tillåter att om kostnad för sålda varor, skapa säkerhetsfunktioner som säker och krypterad lagring eller starta beroende på Trusted Platform Module (TPM). Dessa funktioner gör enheter mer säkra och skydda den övergripande IoT-infrastrukturen.

* **Skydda uppgraderingar**: inbyggd programvara uppgraderingar under livslängden för enheten är ofrånkomligt. Skapa enheter med säker sökvägar för uppgraderingar och kryptografiska vara säkra på att versioner av inbyggd programvara kan enheten inte säker under och efter uppgraderingar.

## <a name="iot-solution-developer"></a>IoT-lösning developer

Här följer de rekommenderade metoderna för utvecklare av IoT-lösningar:

* **Följ säker programvara utvecklingsmetodiken**: utveckling av säker programvara kräver grunden tänka på säkerheten från starten av projektet hela vägen till dess implementering, testning och distribution. Val av plattformar, språk och verktyg påverkas med den här metoden. Microsoft Security Development Lifecycle ger en stegvis metod för att skapa säkra program.

* **Välj programvara med öppen källkod med försiktighet**: öppenkällkodsprogram utgör en möjlighet att utveckla lösningar snabbt. När du väljer program med öppen källkod, Överväg aktivitetsnivån i communityn för varje komponent med öppen källkod. En aktiv community garanterar att programvaran stöds och att problem identifieras och åtgärdas. Du kan också ett diskret och inaktiva öppenkällkodsprogram projekt stöds inte och problem är förmodligen inte identifieras.

* **Integrera med försiktighet**: säkerhetsbrister många programvara finns på gränsen för bibliotek och API: er. Funktioner som inte kanske behövs för den aktuella distributionen kanske fortfarande är tillgänglig via ett API-lager. För att säkerställa säkerheten, se till att kontrollera alla gränssnitt komponenter integreras för säkerhetsbrister.

## <a name="iot-solution-deployer"></a>IoT-lösning deployer

Här följer några Metodtips för IoT-lösning distributörer:

* **Distribuera maskinvara på ett säkert sätt**: IoT-distributioner kan kräva maskinvara som distribueras i oskyddade platser, till exempel i offentliga blanksteg eller ej kontrollerade språk. I sådana situationer kan du se till att maskinvara distribution är manipuleringssäker i utsträckning. Om USB-enheter eller andra portar är tillgängliga för maskinvaran, kontrollerar du att de beskrivs på ett säkert sätt. Många attackvektorer kan använda dessa som startpunkter.

* **Skydda autentiseringsnycklar**: under distributionen av varje enhet kräver enhets-ID och tillhörande autentiseringsnycklar som genererats av Molntjänsten. Skydda de här nycklarna fysiskt även efter distributionen. Alla komprometterade nyckeln kan användas av en obehörig enhet för att domänresurs en befintlig enhet.

## <a name="iot-solution-operator"></a>Operator för IoT-lösning

Här följer de rekommenderade säkerhetsmetoderna för IoT-operatörer för lösningen:

* **Hålla systemet uppdaterade**: se till att uppgradera operativsystem för enheter och alla enhetsdrivrutiner till de senaste versionerna. Om du aktiverar automatiska uppdateringar i Windows 10 (IoT eller andra SKU: er), behåller Microsoft den uppdaterade, vilket ger ett säkert operativsystem för IoT-enheter. Att hålla andra operativsystem (till exempel Linux) uppdaterad hjälper dig att se till att de är skyddade mot skadliga attacker.

* **Skydda mot skadlig aktivitet**: om operativsystemet tillåter kan du installera de senaste funktionerna för virus och skadlig kod på varje enhetsoperativsystem. Den här metoden kan du minimera de flesta externa hot. Du kan skydda de flesta moderna operativsystem mot hot genom att utföra lämpliga åtgärder.

* **Granska ofta**: granskning IoT-infrastruktur för säkerhetsrelaterade problem är nyckeln när du åtgärdar säkerhetsincidenter. De flesta operativsystem tillhandahåller inbyggd loggning som bör granskas ofta för att se till att inga säkerhetsbrist har uppstått. Granskningsinformation kan skickas som en separat telemetriströmmen till Molntjänsten där de kan analyseras.

* **Fysiskt skydda IoT-infrastruktur**: sämsta attacker mot IoT-infrastruktur startas med hjälp av fysisk åtkomst till enheter. En viktig säkerhet metod är att skydda mot skadlig användning av USB-portar och andra fysisk åtkomst. En nyckel till upptäcka överträdelser som har inträffat loggning av fysisk åtkomst till exempel USB-port används. Windows 10 (IoT och andra SKU: er) kan igen, detaljerad loggning av dessa händelser.

* **Skydda molnautentiseringsuppgifter**: Cloud-autentiseringsuppgifterna som används för att konfigurera och driva en IoT-distribution är eventuellt det enklaste sättet att få åtkomst och angripa ett IoT-system. Skydda autentiseringsuppgifterna genom att ändra lösenordet ofta och inte använda dessa autentiseringsuppgifter på offentliga datorer.

Funktionerna för olika IoT-enheter kan variera. Vissa enheter kan vara datorer som kör vanliga operativsystem och vissa enheter kanske körs mycket lätta operativsystem. Rekommenderade säkerhetsmetoder beskrivs kan tidigare tillämpas på dessa enheter i olika grader. Om ytterligare säkerhet och distribution bästa praxis från tillverkaren av ska följas.

Vissa äldre och begränsad enheter kan inte ha har utformats speciellt för IoT-distributionen. Dessa enheter kanske saknar möjlighet att kryptera data, ansluta till Internet eller tillhandahålla avancerade granskning. I dessa fall kan en modern och säker fältgateway samla ihop data från äldre enheter och ge den säkerhet som krävs för att ansluta enheterna via Internet. Fält-gateways kan tillhandahålla säker autentisering, förhandling av krypterade sessioner, mottagande av kommandon från molnet och många andra säkerhetsfunktioner.