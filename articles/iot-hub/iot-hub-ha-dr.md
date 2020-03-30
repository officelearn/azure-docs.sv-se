---
title: Azure IoT Hub hög tillgänglighet och haveriberedskap | Microsoft-dokument
description: Beskriver Azure- och IoT Hub-funktionerna som hjälper dig att skapa azure IoT-lösningar med högtillgängande Azure-lösningar med funktioner för haveriberedskap.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: 615dc1b7bd1a31069a542ebb7ea44693c404cb40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499107"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap för IoT Hub

Som ett första steg mot att implementera en flexibel IoT-lösning måste arkitekter, utvecklare och företagare definiera upptidsmålen för de lösningar de bygger. Dessa mål kan i första hand definieras baserat på specifika affärsmål för varje scenario. I det här sammanhanget beskriver artikeln [Azure Business Continuity Technical Guidance](https://docs.microsoft.com/azure/architecture/resiliency/) ett allmänt ramverk som hjälper dig att tänka på affärskontinuitet och haveriberedskap. Dokumentet [för haveriberedskap och hög tillgänglighet för Azure-program](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery) ger arkitekturvägledning om strategier för Azure-program för att uppnå hög tillgänglighet (HA) och disaster recovery (DR).

I den här artikeln beskrivs ha- och DR-funktionerna som erbjuds specifikt av IoT Hub-tjänsten. De breda områden som diskuteras i denna artikel är:

- Ha inom regionen
- Överregion DR
- Uppnå ha över region

Beroende på de upptidsmål du definierar för dina IoT-lösningar bör du bestämma vilka av alternativen som beskrivs nedan bäst passar dina affärsmål. Införliva någon av dessa HA / DR alternativ i din IoT-lösning kräver en noggrann utvärdering av kompromisser mellan:

- Nivå av återhämtning du behöver 
- Komplexitet i implementering och underhåll
- COGS-påverkan

## <a name="intra-region-ha"></a>Ha inom regionen

IoT Hub-tjänsten tillhandahåller ha inom regionen genom att implementera redundanser i nästan alla lager av tjänsten. [SLA som publiceras av IoT Hub-tjänsten](https://azure.microsoft.com/support/legal/sla/iot-hub) uppnås genom att använda dessa uppsägningar. Inget ytterligare arbete krävs av utvecklarna av en IoT-lösning för att dra nytta av dessa HA-funktioner. Även om IoT Hub erbjuder en någorlunda hög drifttidsgaranti kan tillfälliga fel fortfarande förväntas som med alla distribuerade datorplattformar. Om du precis har börjat med att migrera dina lösningar till molnet från en lokal lösning måste ditt fokus skifta från att optimera "tid mellan fel" till "tid att återställa". Med andra ord ska tillfälliga fel betraktas som normala när du arbetar med molnet i mixen. Lämpliga [principer för återförsök](iot-hub-reliability-features-in-sdks.md) måste vara inbyggda i de komponenter som interagerar med ett molnprogram för att hantera tillfälliga fel.

> [!NOTE]
> Vissa Azure-tjänster tillhandahåller också ytterligare lager av tillgänglighet inom en region genom att integrera med [tillgänglighetszoner (AZs)](../availability-zones/az-overview.md). AZs stöds för närvarande inte av IoT Hub-tjänsten.

## <a name="cross-region-dr"></a>Överregion DR

Det kan finnas vissa sällsynta situationer när ett datacenter upplever utökade avbrott på grund av strömavbrott eller andra fel som involverar fysiska tillgångar. Sådana händelser är sällsynta under vilka den intraregion HA-kapacitet som beskrivs ovan kanske inte alltid hjälper. IoT Hub erbjuder flera lösningar för återställning från sådana utökade avbrott. 

De återställningsalternativ som är tillgängliga för kunder i en sådan situation är [Microsoft-initierade redundans-](#microsoft-initiated-failover) och [manuell redundans.](#manual-failover) Den grundläggande skillnaden mellan de två är att Microsoft initierar den förra och användaren initierar den senare. Manuell redundans ger också ett lägre återställningstidsmål (RTO) jämfört med alternativet Microsoft-initierad redundans. De specifika foS som erbjuds med varje alternativ diskuteras i avsnitten nedan. När något av dessa alternativ för att utföra redundans för en IoT-hubb från dess primära region utnyttjas, blir navet fullt fungerande i motsvarande [Azure-geoparerade region](../best-practices-availability-paired-regions.md).

Båda dessa redundansalternativ erbjuder följande mål för återställningspunkter:

| Datatyp | Mål för återställningspunkt (RPO) |
| --- | --- |
| Identitetsregister |0-5 min dataförlust |
| Dubbla enhetsdata |0-5 min dataförlust |
| Meddelanden från molnet till enheten<sup>1</sup> |0-5 min dataförlust |
| Överordnade<sup>1-</sup> och enhetsjobb |0-5 min dataförlust |
| Meddelanden från enheten till molnet |Alla olästa meddelanden går förlorade |
| Åtgärder som övervakar meddelanden |Alla olästa meddelanden går förlorade |
| Feedbackmeddelanden från molnet till enheten |Alla olästa meddelanden går förlorade |

<sup>1.</sup> Meddelanden från molnet till enheten och överordnade jobb återställs inte som en del av manuell redundans.

När redundansåtgärden för IoT-hubben är klar förväntas alla åtgärder från enheten och backend-programmen fortsätta att fungera utan att det krävs en manuell åtgärd. Det innebär att dina meddelanden från enhet till moln ska fortsätta att fungera och hela enhetsregistret är intakt. Händelser som skickas via Event Grid kan förbrukas via samma prenumerationer som konfigurerats tidigare så länge som dessa Event Grid-prenumerationer fortsätter att vara tillgängliga.

> [!CAUTION]
> - Det eventnah-kompatibla namnet och slutpunkten för den inbyggda IoT Hub-slutpunkten för händelser efter redundans och konfigurerade konsumentgrupper tas bort (det här är ett fel som ska åtgärdas före maj 2020). När du tar emot telemetrimeddelanden från den inbyggda slutpunkten med antingen Event Hub-klienten eller händelsebehandlarens värd, bör du [använda anslutningssträngen för IoT-hubb](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) för att upprätta anslutningen. Detta säkerställer att dina backend-program fortsätter att fungera utan att kräva manuell åtgärd efter redundans. Om du använder det Event Hub-kompatibla namn och slutpunkt i ditt program direkt måste du [konfigurera om den konsumentgrupp som de använder och hämta den nya slutpunkten event hub-kompatibel](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) efter redundans för att fortsätta verksamheten. Om du använder Azure Functions eller Azure Stream Analytics för att ansluta den inbyggda slutpunkten kan du behöva utföra en **omstart**.
>
> - När routning till lagring rekommenderar vi att du listar blobbar eller filer och sedan itererar över dem, för att säkerställa att alla blobbar eller filer läss utan att göra några antaganden om partitionen. Partitionsintervallet kan eventuellt ändras under en Microsoft-initierad redundans eller manuell redundans. Du kan använda [API:et lista blobbar](https://docs.microsoft.com/rest/api/storageservices/list-blobs) för att räkna upp listan över blobbar eller [Lista ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) för listan över filer. 

## <a name="microsoft-initiated-failover"></a>Microsoft-initierad redundans

Microsoft-initierad redundans utövas av Microsoft i sällsynta situationer för att redundans alla IoT-hubbar från en region som påverkas till motsvarande geoparerade region. Den här processen är ett standardalternativ (inget sätt för användare att välja bort) och kräver inget ingripande från användaren. Microsoft förbehåller sig rätten att fastställa när denna option kommer att utnyttjas. Den här mekanismen involverar inte ett användarmedgivande innan användarens hubb har misslyckats. Microsoft-initierad redundans har ett återställningstidsmål (RTO) på 2-26 timmar. 

Den stora RTO beror på att Microsoft måste utföra redundansåtgärden för alla berörda kunder i den regionen. Om du kör en mindre kritisk IoT-lösning som kan upprätthålla en driftstopp på ungefär en dag, är det ok för dig att vara beroende av det här alternativet för att uppfylla de övergripande katastrofåterställningsmålen för din IoT-lösning. Den totala tiden för körningsåtgärder att bli fullt fungerande när den här processen utlöses beskrivs i avsnittet "Tid att återställa".

## <a name="manual-failover"></a>Manuell redundans

Om dina affärstidsmål inte uppfylls av rto som Microsoft initierade redundans ger, överväg att använda manuell redundans för att utlösa redundansprocessen själv. RTO med det här alternativet kan vara mellan 10 minuter och ett par timmar. RTO är för närvarande en funktion av antalet enheter som registrerats mot IoT-hubbinstan som misslyckades över. Du kan förvänta dig att RTO för ett nav som är värd för cirka 100 000 enheter ska vara i ballparken på 15 minuter. Den totala tiden för körningsåtgärder att bli fullt fungerande när den här processen utlöses beskrivs i avsnittet "Tid att återställa".

Alternativet manuell redundans är alltid tillgängligt för användning oavsett om den primära regionen har driftstopp eller inte. Därför kan det här alternativet eventuellt användas för att utföra planerade redundans. Ett exempel på användning av planerade redundansväxlingar är att utföra periodiska redundansövningar. Ett varningens ord är dock att en planerad redundansåtgärd resulterar i ett driftstopp för navet för den period som definieras av RTO för det här alternativet, och även resulterar i en dataförlust enligt definitionen i RPO-tabellen ovan. Du kan överväga att konfigurera en test-IoT-hubbinom du vill utnyttja det planerade redundansalternativet med jämna mellanrum för att få förtroende för din förmåga att få igång dina end-to-end-lösningar när en verklig katastrof inträffar.

Steg-för-steg-instruktioner finns i [Självstudiekurs: Utför manuell redundans för en IoT-hubb](tutorial-manual-failover.md)

### <a name="running-test-drills"></a>Köra provövningar

Testövningar bör inte utföras på IoT-hubbar som används i dina produktionsmiljöer.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Använd inte manuell redundans för att migrera IoT-hubb till en annan region

Manuell redundans bör *inte* användas som en mekanism för att permanent migrera navet mellan Azure geo-parade regioner. Om du gör det ökar svarstiden för de åtgärder som utförs mot IoT-hubben från enheter som är hemsökta i den gamla primära regionen.

## <a name="failback"></a>Återställning efter fel

Om du misslyckas med att återgå till den gamla primära regionen kan uppnås genom att utlösa redundansåtgärden en annan gång. Om den ursprungliga redundansåtgärden utfördes för att återställas från ett utökat avbrott i den ursprungliga primära regionen, rekommenderar vi att navet skulle återställas till den ursprungliga platsen när platsen har återställts från avbrottssituationen.

> [!IMPORTANT]
> - Användare får endast utföra 2 lyckade redundans- och 2 lyckade återställningsåtgärder per dag.
>
> - Åtgärder för återställning till rygg vid redundans/redundans är inte tillåtna. Du måste vänta i 1 timme mellan dessa operationer.

## <a name="time-to-recover"></a>Dags att återställa

Medan FQDN (och därmed anslutningssträngen) för IoT-hubbin förblir samma efter redundans, ändras den underliggande IP-adressen. Därför kan den övergripande tiden för de körningsåtgärder som utförs mot din IoT-hubbin för att bli fullt fungerande när redundansprocessen utlöses uttryckas med hjälp av följande funktion.

Dags att återställa = RTO [10 min - 2 timmar för manuell redundans | 2 - 26 timmar för Microsoft-initierad redundans] + DNS-spridningsfördröjning + Tid som klientprogrammet tar för att uppdatera alla cachelagrade IP-adress för cachelagrade IoT Hub.

> [!IMPORTANT]
> IoT-SDK:erna cachelagrar inte IP-adressen för IoT-hubben. Vi rekommenderar att användarkod som interagerar med SDK:erna inte cachelagrar IP-adressen för IoT-hubben.

## <a name="achieve-cross-region-ha"></a>Uppnå korsregion HA

Om dina affärstidsmål inte uppfylls av RTO som antingen Microsoft-initierade redundans- eller manuella redundansalternativ ger, bör du överväga att implementera en automatisk redundansmekanism för flera enheter.
En fullständig behandling av distributionstopologier i IoT-lösningar omfattas inte av den här artikeln. I artikeln beskrivs den *regionala redundansdistributionsmodellen* för hög tillgänglighet och haveriberedskap.

I en regional redundansmodell körs lösningens back end främst på en datacenterplats. En sekundär IoT-hubb och backend distribueras på en annan datacenterplats. Om IoT-hubben i den primära regionen drabbas av ett avbrott eller om nätverksanslutningen från enheten till den primära regionen avbryts, använder enheter en sekundär tjänstslutpunkt. Du kan förbättra lösningens tillgänglighet genom att implementera en redundansmodell över flera regioner i stället för att hålla sig inom en enda region. 

För att implementera en regional redundansmodell med IoT Hub på en hög nivå måste du vidta följande åtgärder:

* **En sekundär IoT-hubb- och enhetsroutningslogik**: Om tjänsten i din primära region störs måste enheter börja ansluta till din sekundära region. Med tanke på den tillståndsmedvetna karaktären hos de flesta inblandade tjänster är det vanligt att lösningsadministratörer utlöser redundansprocessen mellan regioner. Det bästa sättet att kommunicera den nya slutpunkten till enheter, samtidigt som kontrollen över processen bibehålls, är att låta dem regelbundet kontrollera en *concierge-tjänst* för den aktuella aktiva slutpunkten. Concierge-tjänsten kan vara ett webbprogram som replikeras och hålls nåbar med hjälp av DNS-omdirigeringstekniker (till exempel med Hjälp av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > IoT-hubbtjänsten är inte en slutpunktstyp som stöds i Azure Traffic Manager. Rekommendationen är att integrera den föreslagna concierge-tjänsten med Azure Traffic Manager genom att göra det implementera endpoint hälsa avsökning API.

* **Identitetsregisterreplikering**: För att kunna användas måste den sekundära IoT-hubben innehålla alla enhetsidentiteter som kan ansluta till lösningen. Lösningen bör behålla geo-replikerade säkerhetskopior av enhetsidentiteter och ladda upp dem till den sekundära IoT-hubben innan du byter den aktiva slutpunkten för enheterna. Funktionen för enhetsidentitetsexport för IoT Hub är användbar i det här sammanhanget. Mer information finns i [IoT Hub developer guide - identitetsregister](iot-hub-devguide-identity-registry.md).

* **Sammanslagningslogiken**: När den primära regionen blir tillgänglig igen måste alla tillstånd och data som har skapats på den sekundära platsen migreras tillbaka till den primära regionen. Det här tillståndet och data relaterar oftast till enhetsidentiteter och programmetadata, som måste sammanfogas med den primära IoT-hubben och alla andra programspecifika butiker i den primära regionen. 

För att förenkla det här steget bör du använda idempotenta åtgärder. Idempotenta åtgärder minimerar biverkningarna från den eventuella konsekventa fördelningen av händelser och från dubbletter eller leverans av händelser utanför beställningen. Dessutom bör programlogiken utformas så att den tolererar potentiella inkonsekvenser eller något inaktuella tillstånd. Denna situation kan uppstå på grund av den extra tid det tar för systemet att läka baserat på återställningspunkt mål (RPO).

## <a name="choose-the-right-hadr-option"></a>Välj rätt HA/DR-alternativ

Här är en sammanfattning av HA / DR alternativ som presenteras i den här artikeln som kan användas som en referensram för att välja rätt alternativ som fungerar för din lösning.

| HA/DR-alternativ | Rto | RPO | Kräver manuell intervention? | Komplexitet i genomförandet | Ytterligare kostnadspåverkan|
| --- | --- | --- | --- | --- | --- |
| Microsoft-initierad redundans |2 - 26 timmar|Se RPO-tabellen ovan|Inga|Inget|Inget|
| Manuell redundans |10 min - 2 timmar|Se RPO-tabellen ovan|Ja|Mycket låg. Du behöver bara utlösa den här åtgärden från portalen.|Inget|
| Överregion HA |< 1 min|Beror på replikeringsfrekvensen för din anpassade HA-lösning|Inga|Hög|> 1x kostnaden för 1 IoT-hubb|

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure IoT Hub?](about-iot-hub.md)
* [Komma igång med IoT Hubs (Snabbstart)](quickstart-send-telemetry-dotnet.md)
* [Självstudiekurs: Utför manuell redundans för en IoT-hubb](tutorial-manual-failover.md)