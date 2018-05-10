---
title: ta med fil
description: ta med fil
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2c6f5cf2d89da0c2418ac58ca5d47a8aa05e732f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="internet-of-things-security-best-practices"></a>Sakernas Internet säkerhetsmetoder

Skydda Sakernas Internet (IoT)-infrastruktur kräver en rigorösa security-strategi. Den här strategin måste du skydda data i molnet, skydda dataintegriteten som överförs via det offentliga internet och etablera enheter på ett säkert sätt. Varje lager skapar större säkerhet säkerhet i hela infrastrukturen.

## <a name="secure-an-iot-infrastructure"></a>Skydda en IoT-infrastruktur

Den här strategin för säkerhet på djupet kan utvecklas och utförs med aktiv medverkan av olika aktörer med tillverkning, utveckling och distribution av IoT-enheter och infrastrukturen. Nedan följer en beskrivning av dessa spelare.

* **IoT maskinvara tillverkare/integrator**: dessa spelare normalt tillverkare av IoT-maskinvara som distribueras i dietetisk montering maskinvara från olika tillverkare och leverantörer som tillhandahåller maskinvara för en IoT-distribution tillverkade eller integrerat med andra leverantörer.
* **IoT-lösningen developer**: utvecklingen av en IoT-lösning bör göras av en för lösningsutvecklare. Den här utvecklare kan en del av en interna team eller en systemintegreraren (SI) som har specialiserat sig på den här aktiviteten. IoT-lösningen utvecklare kan utveckla olika komponenter i IoT-lösningen från början, integrera olika standardprogram eller öppen källkod komponenter eller anta solution Accelerator med mindre anpassning.
* **IoT-lösningen deployer**: efter IoT-lösningen har utvecklats, den måste distribueras i fältet. Den här processen distribution av maskinvara, sammankoppling av enheter och distribution av lösningar i maskinvaruenheter eller molnet.
* **IoT-lösningen operatorn**: efter IoT-lösningen distribueras kräver den långsiktiga driften, övervakningen, uppgraderingar och underhåll. Dessa uppgifter kan utföras av en intern team som omfattar information technology specialister, maskinvara åtgärder och underhåll team och domän specialister som övervakar rätt beteendet för övergripande IoT-infrastruktur.

Avsnitten som följer ge bästa praxis för var och en av dessa spelare för att utveckla, distribuera och driva en säker IoT-infrastruktur.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT maskinvara tillverkare/integrator

Följande är bästa praxis för IoT maskinvarutillverkare och maskinvara dietetisk.

* **Omfång maskinvara minimikrav**: maskinvara designen ska innehålla minsta funktioner som krävs för åtgärden för maskinvara, och inget mer. Ett exempel är att inkludera USB-portar endast om det behövs för driften av enheten. Dessa ytterligare funktioner öppna enhet för oönskade angreppsmetoder som bör undvikas.
* **Kontrollera maskinvara säkerhetsförsluten bevis**: skapa mekanismer för att identifiera obehöriga, till exempel öppning av luckan eller ta bort en del av enheten. Dessa säkerhetsförsluten signaler kan vara en del av dataströmmen har överförts till molnet, som kan meddela operatorerna för dessa händelser.
* **Skapa runt säker maskinvara**: om kostnad för sålda varor tillåter, skapa säkerhetsfunktioner, till exempel säker och krypterad lagring eller starta funktioner som bygger på Trusted Platform Module (TPM). Dessa funktioner göra enheter mer säkra och skydda den övergripande IoT-infrastrukturen.
* **Göra uppgraderingar säker**: Firmware-uppgraderingar under livslängden för enheten är ofrånkomligt. Skapa enheter med säker sökvägar för uppgraderingar och kryptografiska försäkran för versioner av inbyggd kan enheten inte säker under och efter uppgraderingar.

## <a name="iot-solution-developer"></a>IoT-lösningen utvecklare

Följande är bästa praxis för utvecklare av IoT-lösningar:

* **Följ säker software development metoder**: utveckling av säker programvara kräver grunden tänka säkerhet från starten av projektet ända till dess implementering, testning och distribution. Val av plattformar och språk verktyg påverkas med denna metod. Microsoft Security Development Lifecycle innehåller stegvis genom att skapa säkra program.
* **Välj med öppen källkod med försiktighet**: med öppen källkod ger en möjlighet att snabbt utveckla lösningar. När du väljer med öppen källkod, Överväg aktivitetsnivå gemenskapens för varje komponent för öppen källkod. En aktiv community garanterar att programvaran stöds och att problem identifieras och hanteras. Du kan också ett otydligt och inaktiva med öppen källkod projekt stöds inte och problem är förmodligen inte identifieras.
* **Integrera med försiktighet**: många programvara säkerhetsbrister finns på gränsen för bibliotek och API: er. Funktioner som inte kanske behövs för den aktuella distributionen kan fortfarande vara tillgänglig via ett API-lager. Se till att markera alla komponenter integreras för säkerhetsbrister-gränssnitt för att garantera övergripande säkerheten.

## <a name="iot-solution-deployer"></a>Distribueraren för IoT-lösning

Följande är bästa praxis för IoT-lösningen distributörer:

* **Distribuera maskinvara på ett säkert sätt**: IoT-distributioner kan kräva maskinvara som distribueras i osäkra platser, exempelvis offentliga blanksteg eller oövervakade språk. I sådana situationer, se till att maskinvara distribution förfalska i utsträckning. Om USB- eller andra portar finns på maskinvara, kontrollera att de omfattas på ett säkert sätt. Många angreppsmetoder kan använda dessa som startpunkter.
* **Skydda autentiseringsnycklar**: under distribution varje enhet kräver enhets-ID och tillhörande autentiseringsnycklar som genererats av Molntjänsten. Skydda de här nycklarna fysiskt även efter distributionen. Alla avslöjade nyckeln kan användas av en obehörig enhet till ut som en befintlig enhet.

## <a name="iot-solution-operator"></a>IoT-lösningen operator

Följande är bästa praxis för ansvariga för IoT-lösningen:

* **Håll datorn uppdaterad**: se till att uppgradera enhetens operativsystem och alla drivrutiner till de senaste versionerna. Om du aktiverar automatiska uppdateringar i Windows 10 (IoT eller andra SKU: er), håller Microsoft den aktuella, vilket ger ett säkert operativsystem för IoT-enheter. Att hålla andra operativsystem (till exempel Linux) säkerställer uppdaterade att de också skyddas mot skadliga attacker.
* **Skydda mot skadlig aktivitet**: om operativsystemet tillåter, installera de senaste funktionerna för antivirus eller antimalware på varje enhets operativsystem. Den här metoden kan du minimera de flesta externa hot. Du kan skydda de flesta moderna operativsystem mot hot genom att vidta lämpliga åtgärder.
* **Granska ofta**: granskning IoT infrastruktur för säkerhetsrelaterade problem är nyckel när svara på säkerhetsincidenter. De flesta operativsystem innehåller inbyggda händelseloggning som bör granskas ofta för att se till att inga säkerhetsintrång har uppstått. Granskningsinformation kan skickas som en separat telemetri dataström till Molntjänsten där den kan analyseras.
* **Skydda fysiskt IoT infrastruktur**: sämsta säkerhetsattacker mot IoT infrastruktur startas med fysisk åtkomst till enheter. En är viktig säkerhet att skydda mot obehörig användning av USB-portar och andra fysisk åtkomst. En nyckel till uncovering överträdelser som har inträffat loggning av fysisk åtkomst till exempel USB-port används. Windows 10 (IoT och andra SKU: er) kan igen och detaljerad loggning av dessa händelser.
* **Skydda molnet autentiseringsuppgifter**: molnet autentiseringsuppgifter som används för att konfigurera och använda en IoT-distribution är eventuellt det enklaste sättet att komma åt och en IoT-dator. Skydda autentiseringsuppgifterna genom att ändra lösenordet ofta och avstå från att använda dessa autentiseringsuppgifter på offentliga datorer.

Funktionerna i olika IoT-enheter varierar. Vissa enheter kan vara datorer som kör vanliga skrivbord och vissa enheter med mycket inaktiverad operativsystem. Rekommenderade säkerhetsmetoder beskrivs kan tidigare tillämpas på dessa enheter i olika grader. Om ytterligare säkerhets- och bästa praxis från tillverkaren av ska följas.

Vissa äldre och begränsad enheter kan inte ha har utformats speciellt för IoT-distribution. Dessa enheter kanske saknar möjlighet att kryptera data, ansluta till Internet eller ge avancerad granskning. I dessa fall kan en modern och säker gateway aggregera data från äldre enheter och ge den säkerhet som krävs för att ansluta enheterna via Internet. Fältet gateways kan tillhandahålla säker autentisering, förhandling krypterade sessioner, kan ta emot kommandon från molnet och många andra säkerhetsfunktioner.