---
title: Katastrofåterställning och geodistribution av Azure Durable Functions
description: Lär dig mer om haveriberedskap och geodistribution i varaktiga funktioner.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433338"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Haveriberedskap och geodistribution i Azure Durable Functions

I varaktiga funktioner sparas alla tillstånd i Azure Storage. En [aktivitetsnav](durable-functions-task-hubs.md) är en logisk behållare för Azure Storage-resurser som används för orkestreringar. Orchestrator- och aktivitetsfunktioner kan bara interagera med varandra när de tillhör samma aktivitetsnav.
De beskrivna scenarierna föreslår distributionsalternativ för att öka tillgängligheten och minimera driftstopp under haveriberedskapsaktiviteter.

Det är viktigt att notera att dessa scenarier baseras på active-passive-konfigurationer, eftersom de styrs av användningen av Azure Storage. Det här mönstret består i att distribuera en backup -funktionsapp (passiv) till en annan region. Traffic Manager övervakar den primära (aktiva) funktionsappen för tillgänglighet. Det kommer att växla över till backup funktion app om den primära misslyckas. Mer information finns i [Traffic Manager's](https://azure.microsoft.com/services/traffic-manager/) [Priority Traffic-Routing Method.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - Den föreslagna active-passive-konfigurationen säkerställer att en klient alltid kan utlösa nya orkestreringar via HTTP. Men som en följd av att ha två funktionsappar som delar samma lagring, kommer bakgrundsbearbetning att distribueras mellan dem båda, som konkurrerar om meddelanden i samma köer. Den här konfigurationen medför extra utgående kostnader för den sekundära funktionsappen.
> - Det underliggande lagringskontot och aktivitetshubben skapas i den primära regionen och delas av båda funktionsapparna.
> - Alla funktionsappar som distribueras redundant måste dela samma funktionsåtkomstnycklar om de aktiveras via HTTP. Funktionskörningen visar ett [hanterings-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) som gör det möjligt för konsumenter att programmässigt lägga till, ta bort och uppdatera funktionsnycklar.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1 - Belastningsbalanserad beräkning med delad lagring

Om beräkningsinfrastrukturen i Azure misslyckas kan funktionsappen bli otillgänglig. För att minimera risken för sådana driftstopp använder det här scenariot två funktionsappar som distribueras till olika regioner.
Traffic Manager är konfigurerad för att identifiera problem i den primära funktionsappen och omdirigera automatiskt trafik till funktionsappen i den sekundära regionen. Den här funktionsappen delar samma Azure Storage-konto och Aktivitetshub. Därför går funktionsapparnas tillstånd inte förlorat och arbetet kan återupptas normalt. När hälsotillståndet har återställts till den primära regionen startar Azure Traffic Manager routningsbegäranden till den funktionsappen automatiskt.

![Diagram som visar scenario 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Det finns flera fördelar när du använder det här distributionsscenariot:

- Om beräkningsinfrastrukturen misslyckas kan arbetet återupptas i regionen överväxling utan tillståndsförlust.
- Traffic Manager tar hand om den automatiska växla över till felfri funktion app automatiskt.
- Traffic Manager återupprättar automatiskt trafiken till den primära funktionsappen efter att avbrottet har korrigerats.

Men med hjälp av det här scenariot överväga:

- Om funktionsappen distribueras med hjälp av en dedikerad App Service-plan ökar kostnaderna att replikera beräkningsinfrastrukturen i datacentret för felväxling.
- Det här scenariot täcker avbrott vid beräkningsinfrastrukturen, men lagringskontot fortsätter att vara den enda felpunkten för funktionsappen. Om det finns ett lagringsstopp drabbas programmet av driftstopp.
- Om funktionsappen har misslyckats över kommer det att finnas en ökad svarstid eftersom den kommer åt sitt lagringskonto i olika regioner.
- Åtkomst till lagringstjänsten från en annan region där den finns medför högre kostnader på grund av nätverksutgående trafik.
- Det här scenariot beror på Traffic Manager. Med tanke på [hur Traffic Manager fungerar](../../traffic-manager/traffic-manager-how-it-works.md)kan det dröja tills ett klientprogram som använder en varaktig funktion behöver fråga igen funktionsappadressen från Traffic Manager.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2 - Belastningsbalanserad beräkning med regional lagring

Föregående scenario täcker endast fel i beräkningsinfrastrukturen. Om lagringstjänsten misslyckas resulterar det i ett avbrott i funktionsappen.
För att säkerställa kontinuerlig drift av de varaktiga funktionerna använder det här scenariot ett lokalt lagringskonto för varje region som funktionsapparna distribueras till.

![Diagram som visar scenario 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Den här metoden lägger till förbättringar jämfört med det tidigare scenariot:

- Om funktionsappen misslyckas tar Traffic Manager hand om att misslyckas med att gå över till den sekundära regionen. Men eftersom funktionsappen är beroende av sitt eget lagringskonto fortsätter de varaktiga funktionerna att fungera.
- Under en återställnings-, finns det ingen ytterligare svarstid i regionen växla över, eftersom funktionsappen och lagringskontot finns i samlokalring.
- Fel på lagringslagret orsakar fel i de varaktiga funktionerna, vilket i sin tur utlöser en omdirigering till regionen växla över. Återigen, eftersom funktionsappen och lagringen är isolerade per region, fortsätter de varaktiga funktionerna att fungera.

Viktiga överväganden för det här scenariot:

- Om funktionsappen distribueras med hjälp av en dedikerad AppService-plan ökar kostnaderna att replikera beräkningsinfrastrukturen i datacentret för felväxling.
- Det aktuella tillståndet har inte misslyckats över, vilket innebär att körnings- och kontrollpunktsfunktioner misslyckas. Det är upp till klientprogrammet att försöka/starta om arbetet igen.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3 – Belastningsbalanserad beräkning med delad GRS-lagring

Det här scenariot är en ändring jämfört med det första scenariot, implementera ett delat lagringskonto. Den största skillnaden att lagringskontot skapas med geo-replikering aktiverat.
Funktionellt ger det här scenariot samma fördelar som scenario 1, men det möjliggör ytterligare dataåterställningsfördelar:

- Geo-redundant lagring (GRS) och Read-access GRS (RA-GRS) maximerar tillgängligheten för ditt lagringskonto.
- Om det finns ett regionutbrott för lagringstjänsten är en av möjligheterna att datacenteråtgärderna avgör att lagring måste överföras till den sekundära regionen. I det här fallet omdirigeras åtkomsten till lagringskontot transparent till den geore replikerade kopian av lagringskontot, utan att användaren behöver göra något.
- I det här fallet kommer tillståndet för de varaktiga funktionerna att bevaras upp till den sista replikeringen av lagringskontot, som inträffar med några minuters mellanrum.

Precis som med de andra scenarierna finns det viktiga överväganden:

- Växla över till repliken görs av datacenteroperatorer och det kan ta lite tid. Fram till dess kommer funktionsappen att drabbas av ett avbrott.
- Det finns en ökad kostnad för att använda geo-replikerade lagringskonton.
- GRS sker asynkront. Vissa av de senaste transaktionerna kan gå förlorade på grund av fördröjningen för replikeringsprocessen.

![Diagram som visar scenario 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om [design av program med högtillgängliga program med RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
