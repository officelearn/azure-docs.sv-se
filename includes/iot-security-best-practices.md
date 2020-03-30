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
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793135"
---
För att säkra en IoT-infrastruktur (Internet of Things) krävs en rigorös strategi för säkerhet på djupet. Den här strategin kräver att du skyddar data i molnet, skyddar dataintegriteten under överföring över det offentliga internet och etablerar enheter på ett säkert sätt. Varje lager skapar större säkerhetsgaranti i den övergripande infrastrukturen.

## <a name="secure-an-iot-infrastructure"></a>Skydda en IoT-infrastruktur

Denna djupgående strategi för säkerhet kan utvecklas och genomföras med aktivt deltagande av olika aktörer som arbetar med tillverkning, utveckling och distribution av IoT-enheter och infrastruktur. Följande är en hög nivå beskrivning av dessa spelare.

* **IoT hårdvarutillverkare /integratör:** Vanligtvis är dessa spelare tillverkare av IoT-maskinvara som distribueras, integratörer monterar maskinvara från olika tillverkare eller leverantörer som tillhandahåller maskinvara för en IoT-distribution som tillverkas eller integreras av andra leverantörer.

* **IoT-lösningsutvecklare**: Utvecklingen av en IoT-lösning görs vanligtvis av en lösningsutvecklare. Den här utvecklaren kan ingå i ett internt team eller en systemintegratör (SI) som specialiserat sig på den här aktiviteten. IoT-lösningsutvecklaren kan utveckla olika komponenter i IoT-lösningen från grunden, integrera olika komponenter från färdiga eller öppen källkod eller anta lösningsacceleratorer med mindre anpassning.

* **IoT-lösningsutsättare**: När en IoT-lösning har utvecklats måste den distribueras i fältet . Den här processen innebär distribution av maskinvara, sammankoppling av enheter och distribution av lösningar i maskinvaruenheter eller molnet.

* **IoT-lösningsoperator:** När IoT-lösningen har distribuerats kräver den långsiktiga åtgärder, övervakning, uppgraderingar och underhåll. Dessa uppgifter kan utföras av ett internt team som består av it-specialister, maskinvaruoperationer och underhållsteam samt domänspecialister som övervakar korrekt beteende hos den övergripande IoT-infrastrukturen.

De avsnitt som följer ger bästa praxis för var och en av dessa spelare för att hjälpa till att utveckla, distribuera och driva en säker IoT-infrastruktur.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-maskinvarutillverkare/integratör

Följande är de bästa metoderna för IoT-maskinvarutillverkare och maskinvaruintegratörer.

* **Scope hårdvara till minimikrav:** Maskinvarudesign bör innehålla de lägsta funktioner som krävs för drift av hårdvaran, och inget mer. Ett exempel är att inkludera USB-portar endast om det behövs för driften av enheten. Dessa ytterligare funktioner öppnar enheten för oönskade attackvektorer som bör undvikas.

* **Gör maskinvarumanipuleringssäkert:** Bygg in mekanismer för att upptäcka fysisk manipulering, till exempel att öppna enhetsluckan eller ta bort en del av enheten. Dessa manipuleringssignaler kan vara en del av dataströmmen som överförs till molnet, vilket kan varna operatörer för dessa händelser.

* **Bygg runt säker maskinvara:** Om COGS tillåter, bygg säkerhetsfunktioner som säker och krypterad lagring eller startfunktioner baserade på Trusted Platform Module (TPM). Dessa funktioner gör enheterna säkrare och skyddar den övergripande IoT-infrastrukturen.

* **Gör uppgraderingar säkra:** Firmware uppgraderingar under enhetens livstid är oundvikliga. Att bygga enheter med säkra sökvägar för uppgraderingar och kryptografisk säkerhet för firmware-versioner gör att enheten kan vara säker under och efter uppgraderingar.

## <a name="iot-solution-developer"></a>Utvecklareutvecklare av IoT-lösningar

Följande är de bästa metoderna för IoT-lösningsutvecklare:

* **Följ säker metod för programvaruutveckling**: Utveckling av säker programvara kräver grundtänk om säkerhet, från starten av projektet hela vägen till dess implementering, testning och driftsättning. De olika plattformarnas, språkens och verktygens val påverkas alla av denna metod. Livscykeln för Microsoft Security Development Lifecycle är en steg-för-steg-metod för att skapa säker programvara.

* **Välj programvara med öppen källkod med försiktighet:** Programvara med öppen källkod ger möjlighet att snabbt utveckla lösningar. När du väljer programvara med öppen källkod bör du tänka på aktivitetsnivån för communityn för varje komponent med öppen källkod. En aktiv community ser till att programvara stöds och att problem upptäcks och åtgärdas. Alternativt kanske ett obskyrt och inaktivt programprojekt med öppen källkod inte stöds och problem är sannolikt inte upptäckta.

* **Integrera med försiktighet:** Många brister i programvarusäkerheten finns vid gränsen för bibliotek och API:er. Funktioner som kanske inte krävs för den aktuella distributionen kan fortfarande vara tillgängliga via ett API-lager. Se till att kontrollera alla gränssnitt för komponenter som integreras för säkerhetsbrister för att säkerställa övergripande säkerhet.

## <a name="iot-solution-deployer"></a>IoT-lösningsde deployer

Följande är metodtips för IoT-lösningsutredare:

* **Distribuera maskinvara på ett säkert sätt:** IoT-distributioner kan kräva att maskinvara distribueras på osäkra platser, till exempel i offentliga utrymmen eller oövervakade språk. I sådana situationer, se till att maskinvarudistribution är manipuleringssäker i största möjliga utsträckning. Om USB eller andra portar är tillgängliga på maskinvaran kontrollerar du att de täcks på ett säkert sätt. Många attackvektorer kan använda dessa som startpunkter.

* **Skydda autentiseringsnycklar:** Under distributionen kräver varje enhet enhets-ID och associerade autentiseringsnycklar som genereras av molntjänsten. Håll dessa nycklar fysiskt säkra även efter distributionen. Alla komprometterade nyckel kan användas av en skadlig enhet för att maskera sig som en befintlig enhet.

## <a name="iot-solution-operator"></a>IoT-lösningsoperatör

Följande är de bästa metoderna för IoT-lösningsoperatörer:

* **Håll systemet uppdaterat:** Se till att enhetsoperativsystem och alla drivrutiner uppgraderas till de senaste versionerna. Om du aktiverar automatiska uppdateringar i Windows 10 (IoT eller andra SKU:er) håller Microsoft det uppdaterat och tillhandahåller ett säkert operativsystem för IoT-enheter. Att hålla andra operativsystem (till exempel Linux) uppdaterade hjälper till att säkerställa att de också skyddas mot skadliga attacker.

* **Skydda mot skadlig aktivitet:** Om operativsystemet tillåter, installera de senaste antivirus- och antimalware-funktionerna på varje enhets operativsystem. Den här metoden kan bidra till att minska de flesta externa hot. Du kan skydda de flesta moderna operativsystem mot hot genom att vidta lämpliga åtgärder.

* **Granska ofta**: Granskning av IoT-infrastruktur för säkerhetsrelaterade problem är nyckeln när du svarar på säkerhetsincidenter. De flesta operativsystem tillhandahåller inbyggd händelseloggning som bör granskas ofta för att se till att ingen säkerhetsöverträdelse har inträffat. Granskningsinformation kan skickas som en separat telemetriström till molntjänsten där den kan analyseras.

* **Fysiskt skydda IoT-infrastrukturen**: De värsta säkerhetsattackerna mot IoT-infrastrukturen startas med hjälp av fysisk åtkomst till enheter. En viktig säkerhetspraxis är att skydda mot skadlig användning av USB-portar och annan fysisk åtkomst. En nyckel till att upptäcka överträdelser som kan ha inträffat är loggning av fysisk åtkomst, till exempel användning av USB-portar. Återigen, Windows 10 (IoT och andra SKU: er) möjliggör detaljerad loggning av dessa händelser.

* **Skydda molnautentiseringsuppgifter:** Autentiseringsuppgifter för molnautentisering som används för att konfigurera och driva en IoT-distribution är kanske det enklaste sättet att få åtkomst till och kompromettera ett IoT-system. Skydda autentiseringsuppgifterna genom att ändra lösenordet ofta och avstå från att använda dessa autentiseringsuppgifter på offentliga datorer.

Funktionerna för olika IoT-enheter varierar. Vissa enheter kan vara datorer som kör vanliga stationära operativsystem, och vissa enheter kan köra mycket lätta operativsystem. De bästa säkerhetsrutinerna som beskrivits tidigare kan vara tillämpliga på dessa enheter i varierande grad. Om det tillhandahålls bör ytterligare metoder för säkerhet och distribution från tillverkarna av dessa enheter följas.

Vissa äldre och begränsade enheter kanske inte har utformats specifikt för IoT-distribution. Dessa enheter kan sakna möjlighet att kryptera data, ansluta till Internet eller tillhandahålla avancerad granskning. I dessa fall kan en modern och säker fältgateway aggregera data från äldre enheter och ge den säkerhet som krävs för att ansluta dessa enheter via Internet. Fältgateways kan tillhandahålla säker autentisering, förhandling av krypterade sessioner, mottagande av kommandon från molnet och många andra säkerhetsfunktioner.