---
title: Haveri beredskap och geo-distribution av Azure Durable Functions
description: Lär dig mer om haveri beredskap och geo-distribution i Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 1bddb6af777f36d04bae4a8b68c476789048be1b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081788"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Haveri beredskap och geo-distribution i Azure Durable Functions

I Durable Functions sparas all status i Azure Storage. En [aktivitets hubb](durable-functions-task-hubs.md) är en logisk behållare för Azure Storage resurser som används för dirigering. Orchestrator-och aktivitets funktioner kan bara samverka med varandra när de tillhör samma aktivitets nav.
De scenarier som beskrivs ger förslag på distributions alternativ för att öka tillgängligheten och minimera stillestånds tiden vid haveri beredskap.

Det är viktigt att Observera att de här scenarierna baseras på aktiva passiva konfigurationer, eftersom de guidas av användningen av Azure Storage. Det här mönstret består av att distribuera en säkerhets kopiering (passiv) Function-app till en annan region. Traffic Manager övervakar den primära (aktiva) Function-appen för tillgänglighet. Appen växlar över till säkerhets kopierings funktionen om den primära Miss lyckas. Mer information finns i [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) [Priority-metoden för trafik-routning.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - Den föreslagna aktiva passiva konfigurationen säkerställer att klienten alltid kan utlösa nya dirigeringar via HTTP. Till följd av att två Functions-appar delar samma lagring, distribueras bakgrunds bearbetningen mellan båda, och konkurrerar om meddelanden i samma köer. Den här konfigurationen medför ytterligare utgående kostnader för appen för sekundär funktion.
> - Det underliggande lagrings kontot och aktivitets navet skapas i den primära regionen och delas av båda funktions apparna.
> - Alla funktions program som är redundanta distribuerade måste dela samma funktions åtkomst nycklar om de ska aktive ras via HTTP. Functions-körningen exponerar ett [hanterings-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) som gör det möjligt för användare att program mässigt lägga till, ta bort och uppdatera funktions nycklar.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1 – belastnings bal anse rad beräkning med delad lagring

Om beräknings infrastrukturen i Azure Miss lyckas kan Function-appen bli otillgänglig. I det här scenariot används två Function-appar som distribuerats till olika regioner för att minimera risken för sådana nedtid.
Traffic Manager har kon figurer ATS för att identifiera problem i den primära Function-appen och automatiskt omdirigera trafik till Function-appen i den sekundära regionen. Den här funktions appen delar samma Azure Storage konto och aktivitets nav. Därför försvinner inte statusen för Function-apparna och arbetet kan återupptas normalt. När hälso tillståndet har återställts till den primära regionen börjar Azure Traffic Manager dirigera begär anden till den Function-appen automatiskt.

![Diagram som visar scenario 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Det finns flera fördelar med att använda det här distributions scenariot:

- Om beräknings infrastrukturen Miss lyckas kan arbetet återupptas i området redundansväxla utan tillstånds förlust.
- Traffic Manager tar hand om automatisk redundans till appen för felfri funktion automatiskt.
- Traffic Manager automatiskt återupprätta trafik till den primära Function-appen när avbrottet har åtgärd ATS.

Men i det här scenariot bör du tänka på följande:

- Om Function-appen distribueras med hjälp av en dedikerad App Service plan, ökar kostnaderna genom att replikera beräknings infrastrukturen i data centret för redundans.
- Det här scenariot täcker avbrott i beräknings infrastrukturen, men lagrings kontot fortsätter att vara den enda felpunkten för Function-appen. Om det uppstår ett lagrings avbrott får programmet en nedtid.
- Om Function-appen har redundansväxlats, ökar svars tiden eftersom den kommer åt sitt lagrings konto över flera regioner.
- Åtkomst till lagrings tjänsten från en annan region där den befinner sig i högre kostnad på grund av utgående trafik i nätverket.
- Det här scenariot är beroende av Traffic Manager. Med tanke på [hur Traffic Manager fungerar](../../traffic-manager/traffic-manager-how-it-works.md), kan det ta en stund innan ett klient program som förbrukar en varaktig funktion måste fråga igen appens adress från Traffic Manager.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2 – belastnings bal anse rad beräkning med regional lagring

Föregående scenario täcker bara händelse av ett haveri i beräknings infrastrukturen. Om lagrings tjänsten Miss lyckas resulterar det i ett avbrott i Function-appen.
För att säkerställa kontinuerlig drift av de varaktiga funktionerna använder det här scenariot ett lokalt lagrings konto i varje region som funktions programmen distribueras till.

![Diagram som visar scenario 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Den här metoden lägger till förbättringar i föregående scenario:

- Om funktions programmet Miss lyckas, Traffic Manager ta hand om att redundansväxla till den sekundära regionen. Men eftersom Function-appen är beroende av sitt eget lagrings konto fortsätter de varaktiga funktionerna att fungera.
- Under en växling vid misslyckande finns det ingen ytterligare fördröjning i området redundans, eftersom Function-appen och lagrings kontot är samplacerade.
- Fel i lagrings lagret orsakar fel i de varaktiga funktionerna, vilket i sin tur utlöser en omdirigering till området redundans. Eftersom Function-appen och lagringen isoleras per region fortsätter de varaktiga funktionerna att fungera.

Viktiga överväganden för det här scenariot:

- Om Function-appen distribueras med hjälp av en dedikerad AppService-plan ökar kostnaderna genom att replikera beräknings infrastrukturen i data centret för redundans.
- Det går inte att redundansväxla det aktuella läget, vilket innebär att det inte går att köra och checkpointd functions. Det är upp till klient programmet att försöka igen/starta om arbetet.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3 – belastnings bal anse rad beräkning med GRS delad lagring

Det här scenariot är en ändring i det första scenariot, vilket implementerar ett delat lagrings konto. Den största skillnaden att lagrings kontot har skapats med geo-replikering aktiverat.
Det här scenariot ger samma fördelar som scenario 1, men det ger ytterligare fördelar för data återställning:

- Geo-redundant lagring (GRS) och Read-Access GRS (RA-GRS) maximerar tillgängligheten för ditt lagrings konto.
- Om lagrings tjänsten har ett regions avbrott är en av möjligheterna att data Center åtgärderna bestämmer att lagringen måste växlas över till den sekundära regionen. I det här fallet omdirigeras åtkomsten till lagrings kontot transparent till den geo-replikerade kopian av lagrings kontot, utan att användaren tillfrågas.
- I det här fallet kommer tillstånd för de varaktiga funktionerna att bevaras upp till den senaste replikeringen av lagrings kontot, vilket sker vart några minuter.

Precis som med andra scenarier finns det viktiga överväganden:

- Redundans till repliken utförs av data Center operatörer och det kan ta lite tid. Fram till dess kommer funktions programmet att drabbas av ett avbrott.
- Det finns en ökad kostnad för att använda geo-replikerade lagrings konton.
- GRS sker asynkront. Några av de senaste transaktionerna kan gå förlorade på grund av svars tiden för replikeringen.

![Diagram som visar scenario 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om att [utforma hög tillgängliga program med RA-GRS](../../storage/common/geo-redundant-design.md)
