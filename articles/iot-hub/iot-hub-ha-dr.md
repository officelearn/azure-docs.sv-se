---
title: Azure IoT Hub hög tillgänglighet och katastrofåterställning recovery | Microsoft Docs
description: Beskriver de funktioner för Azure och IoT Hub som hjälper dig att bygga högtillgängliga Azure IoT-lösningar med disaster recovery-funktioner.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: rkmanda
ms.openlocfilehash: 308d9a04e52572e00e1cbed24548e5f09adda571
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985928"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub hög tillgänglighet och disaster recovery

Som ett första steg för att implementera en flexibel IoT definiera-lösning, arkitekter, utvecklare och företagsägare drifttid målen för lösningar som de skapar. Dessa mål kan definieras i första hand baserat på specifika affärsmål för varje scenario. I det här sammanhanget artikeln [Azure Business Continuity teknisk vägledning](https://docs.microsoft.com/azure/architecture/resiliency/) beskriver ett allmänt ramverk för att hjälpa dig att tänka på verksamhetskontinuitet och haveriberedskap. Den [haveriberedskap och hög tillgänglighet för Azure-program](https://msdn.microsoft.com/library/dn251004.aspx) dokumentet innehåller vägledning för arkitektur om strategier för Azure-program att uppnå hög tillgänglighet (HA) och Disaster Recovery (DR).

Den här artikeln beskriver HA och DR-funktioner som erbjuds av IoT Hub-tjänsten. Bred områden som beskrivs i den här artikeln är:

- Inom regionen hög tillgänglighet
- Mellan regioner DR
- Uppnå hög tillgänglighet för flera regioner

Beroende på de drifttid mål som du definierar för dina IoT-lösningar, bör du kontrollera vilka av alternativ som beskrivs nedan efter dina affärsmål. Genom att använda någon av följande hr/DR-alternativ i din IoT-lösning kräver en noggrann utvärdering av avvägningarna mellan den:

- Återhämtningsnivå som du behöver 
- Implementering och underhåll komplexitet
- Kostnad för sålda varor påverkan

## <a name="intra-region-ha"></a>Inom regionen hög tillgänglighet

Tjänsten IoT Hub ger hög tillgänglighet inom regioner genom att implementera uppsägningar i nästan alla lager i tjänsten. Den [serviceavtal som publicerats av IoT Hub-tjänsten](https://azure.microsoft.com/support/legal/sla/iot-hub) uppnås genom att utnyttja dessa uppsägningar. Inget ytterligare arbete krävs av utvecklare av en IoT-lösning för att dra nytta av dessa funktioner med hög tillgänglighet. IoT Hub garanterar vi att rimligen hög upptid, men tillfälliga fel kan du fortfarande förväntas precis som med alla distribuerade plattform för databearbetning. Om du precis har kommit igång med att migrera dina lösningar till molnet från en lokal lösning, fokus behöver skifta från optimera ”Genomsnittlig tid mellan fel” till ”Genomsnittlig tid för återställning”. Med andra ord är tillfälliga fel för att anses vara normala när molnet av sammansättning. Lämplig [återförsöksprinciper](iot-hub-reliability-features-in-sdks.md) måste skapas för att de komponenter som interagerar med ett molnprogram behöver bry dig om tillfälliga fel.

> [!NOTE]
> Vissa Azure-tjänster kan även ange ytterligare skyddslager för tillgänglighet inom en region genom att integrera med [Tillgänglighetszoner (AZs)](../availability-zones/az-overview.md). AZs stöds för närvarande inte av tjänsten IoT Hub.

## <a name="cross-region-dr"></a>Mellan regioner DR

Det kan finnas vissa sällsynta fall när ett datacenter påträffar långvariga avbrott på grund av strömavbrott eller andra fel som berör fysiska tillgångar. Sådana händelser är ovanliga under vilket intra region kapaciteten för hög tillgänglighet som beskrivs ovan kan inte alltid hjälpa. IoT Hub innehåller flera lösningar för att återställa från sådana långvariga avbrott. 

Återställningsalternativ som är tillgängliga för kunder i en sådan situation är ”Microsoft-initierad växling vid fel” och ”manuell växling vid fel”. Den grundläggande skillnaden mellan två är att Microsoft initierar den tidigare versionen och användaren initierar det senare. Manuell redundans tillhandahåller också en lägre återställningstid (RTO) jämfört med redundansalternativet för Microsoft-initierad. De specifika mål för återställningstid med varje alternativ beskrivs i avsnitten nedan. När något av dessa alternativ för att utföra redundans för en IoT hub från den primära regionen utnyttjas hubben blir fullt funktionell i motsvarande [Azure geoparats ihop region](../best-practices-availability-paired-regions.md).

Båda alternativen redundans erbjuder följande återställningspunktmål (Rpo):

| Datatyp | Återställningspunktmål (RPO) |
| --- | --- |
| Identitetsregistret |0 – 5 minuter dataförlust |
| Enhetsdata för enhetstvilling |0 – 5 minuter dataförlust |
| Meddelanden från moln till enhet<sup>1</sup> |0 – 5 minuter dataförlust |
| Överordnad<sup>1</sup> och enhetsjobb |0 – 5 minuter dataförlust |
| Meddelanden från enheten till molnet |Försvinner alla olästa meddelanden |
| Åtgärdsövervakning meddelanden |Försvinner alla olästa meddelanden |
| Meddelanden moln till enhet |Försvinner alla olästa meddelanden |

<sup>1</sup>inte hämta återställs meddelanden från moln till enhet och överordnade jobb som en del av manuell växling vid fel i i preview-versionen av den här funktionen.

När redundansväxlingen för IoT-hubben har slutförts kan förväntas alla åtgärder från enheten och backend-program att fortsätta arbeta utan manuell inblandning.

> [!CAUTION]
> - Event Hub-kompatibla namnet och slutpunkten för IoT Hub-slutpunkten för inbyggda händelser ändra efter en redundansväxling. När du tar emot telemetrimeddelanden från den inbyggda slutpunkten med hjälp av event hub-klienten eller värd för händelsebearbetning, bör du [använder IoT hub-anslutningssträngen](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) att upprätta anslutningen. Detta säkerställer att dina backend-program fortsätter att fungera utan manuella åtgärder efter redundans. Om du använder Event Hub-kompatibla namnet och slutpunkt i backend-programmet direkt, du behöver konfigurera om genom att ange [hämtar nya Event Hub-kompatibla namnet och slutpunkt](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) efter en redundansväxling för att fortsätta åtgärder.
>
> - De händelser som genererats via Event Grid kan användas via samma prenumerationerna som tidigare har konfigurerat så länge dessa Event Grid-prenumerationer fortsätter att vara tillgängliga efter redundansväxlingen.
>
> - När routning till blob storage, rekommenderar vi ta blobar och sedan iterera över dem, så läses alla behållare utan att göra några antaganden för partitionen. Partitionsintervall kan potentiellt påverkar under en Microsoft-initierad redundans eller manuell växling vid fel. Lär dig hur du räkna upp listan över blobar Se [routning till blob storage](iot-hub-devguide-messages-d2c.md#azure-blob-storage).

### <a name="microsoft-initiated-failover"></a>Microsoft-initierad redundans

Microsoft-initierad redundans utnyttjas av Microsoft i sällsynta fall vill växla vid fel i IoT hubs från en berörda region till motsvarande geoparats ihop region. Den här processen är standardalternativet (inget sätt för användare att välja bort) och kräver inget ingripande från användaren. Microsoft förbehåller sig rätten att göra en bestämning av när det här alternativet ska utföras. Den här mekanismen innebära inte en användarens medgivande innan användarens hub har redundansväxlats. Microsoft-initierad redundans har en återställningstid (RTO) 2-26 timmar. 

Stora RTO beror på Microsoft måste utföra redundansväxlingen för alla berörda kunder i den regionen. Om du kör en mindre viktiga IoT-lösning som kan klara ett driftstopp ungefär per dag, är det ok att skapa ett beroende på det här alternativet för att uppfylla de övergripande återställningsmålen för haveriberedskap för din IoT-lösning. Den totala tiden för körningen operations blir fullt fungerande när den här processen utlöses, beskrivs i avsnittet ”tid för att återställa”.

### <a name="manual-failover-preview"></a>Manuell redundansväxling (förhandsversion)

Om dina affärsmål drifttid inte är uppfyllt av RTO som Microsoft har initierat redundans ger, bör du använda manuell växling vid fel för att utlösa redundansprocessen själv. RTO som använder det här alternativet kan vara var som helst mellan 10 minuter till ett par timmar. RTO är för närvarande en funktion av antalet enheter som har registrerats mot IoT hub-instansen som redundansväxlas. Du kan förvänta dig RTO för ett nav som är värd för cirka 100 000 enheter kan på ungefärlig högst 15 minuter. Den totala tiden för körningen operations blir fullt fungerande när den här processen utlöses, beskrivs i avsnittet ”tid för att återställa”.

Alternativet för manuell växling vid fel är alltid tillgänglig för användning oavsett om den primära regionen har drabbats av driftstopp eller inte. Det här alternativet kan därför potentiellt användas för att utföra planerade redundanser. Ett exempel på användning av planerade redundanser är att utföra regelbundna redundans tester. En liten varning om är att en planerad redundans åtgärden resulterar i ett driftstopp för hubben för den tid som anges av RTO för det här alternativet och resulterar också i en förlust av data som definieras av RPO tabellen ovan. Du kan ställa in ett test av IoT hub-instansen att alternativet för planerad redundans med jämna mellanrum för att få förtroende i din möjlighet att få din slutpunkt till slutpunkt-lösningar och som körs när en verklig katastrof inträffar.

> [!IMPORTANT]
> - Testa tester bör inte utföras på IoT-hubbar som används i produktionsmiljöerna.
>
> - Manuell redundans bör inte användas som en mekanism för att migrera din hubb mellan regionerna som Azure geo ihop permanent. Detta skulle orsaka en ökad latens för åtgärder som utförs mot hubben från enheter homed i den gamla primära regionen.
>
> - Manuell redundans är en förhandsversion och är inte tillgänglig i följande Azure-regioner. Östra USA, västra USA, Norra Europa, västra Europa, södra Brasilien, södra centrala USA.

### <a name="failback"></a>Återställning efter fel

Misslyckas tillbaka till den gamla primära regionen kan uppnås genom att utlösa redundans åtgärden ett senare tillfälle. Om den ursprungliga Redundansåtgärden har genomförts för att återställa från ett längre avbrott i den ursprungliga primära regionen, rekommenderar vi hubben ska att växlas tillbaka till den ursprungliga platsen när platsen har återställts från avbrott situationen.

> [!IMPORTANT]
> - Användare kan bara utföra 2 lyckad redundans och 2 lyckad återställning efter fel-åtgärder per dag.
>
> - Intill varandra är redundans/återställning inte tillåtet. Användarna måste vänta i 1 timme mellan dessa åtgärder.

### <a name="time-to-recover"></a>Tid att återställa

Medan det fullständiga Domännamnet (och därför anslutningssträngen) av IoT hub-instansen är samma efter redundans, ändras den underliggande IP-adressen. Därför kan den totala tid för runtime-åtgärder som utförs mot din IoT hub-instansen blir fullt fungerande när redundansprocessen utlöses uttryckas med hjälp av följande funktion.

Tid att återställa = RTO [10: e minut – 2 timmar för manuell växling | 2-26 timmar för Microsoft-initierad redundans] + DNS-spridningen fördröjning + tid det tar att klientprogrammet kan uppdatera någon cachelagrade IoT Hub IP-adress.

> [!IMPORTANT]
> IoT-SDK: er Cachelagra inte IP-adressen för IoT-hubben. Vi rekommenderar att användarkod som samverkar med SDK: erna inte bör cachelagrar IP-adressen för IoT-hubben.

## <a name="achieve-cross-region-ha"></a>Uppnå mellan regioner hög tillgänglighet

Om drifttid affärsmålen inte är uppfyllt av RTO att Microsoft-initierad redundans eller manuell växling vid fel alternativ tillhandahåller, bör du överväga att implementera en mekanism för varje enhet automatiskt mellan regioner redundans.
En fullständig behandling av distributionstopologier i IoT-lösningar som ligger utanför omfånget för den här artikeln. Artikeln beskriver den *regional redundans* distributionsmodell för hög tillgänglighet och katastrofåterställning återställning.

Lösningen tillbaka slutet körs huvudsakligen i en datacenterplats i en regional redundans-modell. En sekundär IoT-hubb och backend-servrar distribueras på en annan datacenterplats. Om IoT-hubben i den primära regionen drabbas av ett avbrott eller nätverksanslutning från enheten till den primära regionen avbryts, enheter att använda en sekundär slutpunkt. Du kan förbättra tillgängligheten för lösningen genom att implementera en modell för redundans över regioner i stället för dig inom en region. 

På en hög nivå för att implementera en regional redundans-modell med IoT Hub måste du vidta följande steg:

* **En sekundär IoT hub och enhet routning logic**: Om tjänsten i din primära region avbryts starta enheter ansluter till din sekundära region. Den tillståndsmedveten naturen för de flesta tjänster som ingår, är det vanligt för administratörer av lösning att utlösa mellan regioner redundansprocessen. Det bästa sättet att kommunicera med den nya slutpunkten till enheter, samtidigt som de behåller kontrollen över processen är att de regelbundet kontrollera en *concierge* för den aktuella aktiva slutpunkten. Tjänsten concierge kan vara ett webbprogram som ska replikeras och sparas kan nås med hjälp av tekniker för DNS-omdirigering (till exempel [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > IoT hub-tjänsten är inte en typ av stöds slutpunkt i Azure Traffic Manager. Rekommendationen är att integrera tjänsten föreslagna concierge med Azure traffic manager genom att implementera endpoint hälsoavsökningen API.

* **Replikering för Identity-registry**: Sekundär IoT-hubben måste innehålla alla enhetsidentiteter som kan ansluta till lösningen kan användas. Lösningen ska hålla geo-replikerade säkerhetskopior av enhetsidentiteter och överföra dem till den sekundära IoT-hubben innan du byter aktiv slutpunkt för enheter. Enhetens identitet exportfunktionen för IoT Hub är användbart i den här kontexten. Mer information finns i [utvecklarhandboken för en IoT Hub - identitetsregistret](iot-hub-devguide-identity-registry.md).

* **Sammanslagning av logic**: När den primära regionen blir tillgänglig igen, måste alla tillstånd och data som har skapats på den sekundära platsen flyttas tillbaka till den primära regionen. Den här tillstånd och data främst är relaterade till enhetsidentiteter och programmetadata, som måste slås samman med den primära IoT hub och andra programspecifika butiker i den primära regionen. 

För att förenkla det här steget ska du använda idempotenta åtgärder. Idempotenta åtgärder minimera sidoeffekter från eventuell konsekvent distribution av händelser och dubbletter eller out ordning leverans av händelser. Dessutom bör programlogiken utformas ska kunna hanteras eventuella inkonsekvenser eller något inaktuell tillstånd. Den här situationen kan inträffa på grund av den ytterligare tid det tar för systemet kan reparera baserat på återställningspunktmål (RPO).

## <a name="choose-the-right-hadr-option"></a>Välj rätt hr/DR-alternativ

Här är en sammanfattning av hr/DR-alternativ som visas i den här artikeln som kan användas som en referensram för att välja rätt alternativ som passar din lösning.

| HR/DR-alternativet | MÅL FÖR ÅTERSTÄLLNINGSTID | Mål för återställningspunkt | Kräver manuella åtgärder? | Implementering komplexitet | Extra kostnad påverkan|
| --- | --- | --- | --- | --- | --- | --- |
| Microsoft-initierad redundans |2 – 26 timmar|Se RPO tabellen ovan|Nej|Ingen|Ingen|
| Manuell redundansväxling |10: e minut – 2 timmar|Se RPO tabellen ovan|Ja|Mycket låg. Du behöver bara utlösa den här åtgärden från portalen.|Ingen|
| Mellan region hög tillgänglighet |< 1 min|Beroende på replikeringsfrekvensen av ditt eget program för hög tillgänglighet|Nej|Hög|> 1 x-kostnaden för 1 IoT-hubb|

## <a name="next-steps"></a>Nästa steg

Du kan följa dessa länkar om du vill veta mer om Azure IoT Hub:

* [Kom igång med IoT-hubbar (Snabbstart)](quickstart-send-telemetry-dotnet.md)
* [Vad är Azure IoT Hub?](about-iot-hub.md)