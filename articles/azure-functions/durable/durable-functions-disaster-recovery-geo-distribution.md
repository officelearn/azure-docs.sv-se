---
title: Haveriberedskap och geo-replikering i varaktiga funktioner – Azure
description: Läs mer om haveriberedskap och geo-replikering i varaktiga funktioner.
services: functions
author: MS-Santi
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 1363dd3c620789b9f3c8ce1dbe0892ee61d66051
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108213"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Haveriberedskap och geo-replikering

## <a name="overview"></a>Översikt

Alla tillstånd sparas i varaktiga funktioner i Azure Storage. En [uppgift hub](durable-functions-task-hubs.md) är en logisk behållare för Azure Storage-resurser som används för orkestreringar. Orchestrator och aktivitet kan bara interagera med varandra när de tillhör samma uppgift hubb.
De beskrivna scenarierna föreslå distributionsalternativ för att öka tillgängligheten och minimera driftstopp under disaster recovery-aktiviteter.

Det är viktigt att Observera att dessa scenarier baseras på aktivt-passivt konfigurationer, eftersom de vägleds genom användningen av Azure Storage. Det här mönstret innebär att distribuera en funktionsapp för säkerhetskopiering (passivt) till en annan region. Traffic Manager ska övervaka primära (aktiva) funktionsappen för tillgänglighet. Den växlar över till säkerhetskopiering funktionsappen om den primära databasen misslyckas. Mer information finns i [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)'s [prioritet Trafikdirigeringsmetoden.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - Föreslagna aktiv-passiv konfiguration garanterar att en klient alltid är kunna få nya orkestreringar via HTTP. Till följd av att ha två funktionsappar som delar samma lagring, fördelas bearbetning i bakgrunden mellan lagringsintensiva tävlar om meddelanden i samma köer. Den här konfigurationen medför har lagts till utgående kostnader för sekundära funktionsappen.
> - Underliggande storage-konto och uppgiften hubben har skapats i den primära regionen och delas av båda funktionsappar.
> - Alla funktionsappar som distribueras redundant måste dela samma funktion åtkomstnycklar när det gäller som aktiveras via HTTP. Functions-körning Exponerar en [hanterings-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) som gör att kunder kan lägga till, ta bort och uppdatera funktionstangenter.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1 – belastningsutjämnade beräkning med delad lagring

Om det inte går att datainfrastruktur i Azure, bli funktionsappen otillgänglig. Det här scenariot används för att minimera risken för sådana avbrott, två funktionsappar som distribueras till olika regioner.
Traffic Manager är konfigurerad för att identifiera problem med primära funktionsappen och automatiskt dirigera trafik till funktionsappen i den sekundära regionen. Den här funktionsappen delar samma Azure Storage-konto och uppgiften Hub. Därför försvinner inte tillståndet för funktionsappar och arbetet kan återuppta normalt. När hälsotillståndet återställs till den primära regionen, startar Azure Traffic Manager dirigering av begäranden till den funktionsappen automatiskt.

![Diagram som visar scenario 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Det finns flera fördelar när du använder det här distributionsscenariot:

- Om det inte går att beräkningsinfrastrukturen, återupptas arbete om redundansen över regioner utan förlust av tillstånd.
- Traffic Manager hand tar om automatisk redundansväxlas till felfria funktionsappen automatiskt.
- Traffic Manager återupprättar automatiskt trafik till den primära funktionsappen när driftstörningarna har åtgärdats.

Tänk dock med det här scenariot:

- Om appen har distribuerats med hjälp av en dedikerad App Service-plan, ökar replikerar datainfrastruktur i misslyckas över datacenter kostnaderna.
- Det här scenariot omfattar avbrott på beräkningsinfrastrukturen, men lagringskontot fortsätter att vara enskild felpunkt för funktionen App. Om det finns driftstörningar, drabbas ett driftstopp i programmet.
- Om funktionsappen har redundansväxlats blir ökad latens eftersom den ska komma åt lagringskontot i flera regioner.
- Åtkomst till storage-tjänsten från en annan region där det är placerad medför högre kostnad på grund av utgående nätverkstrafik.
- Det här scenariot beror på Traffic Manager. Överväger [så här fungerar Traffic Manager](../../traffic-manager/traffic-manager-how-it-works.md), det kan vara en stund innan ett klientprogram som förbrukar en hållbar funktion måste fråga igen funktionen app-adress från Traffic Manager.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2 – belastningsutjämnade beräkning med regionala lagring

Det föregående scenariot beskriver bara i fallet med fel i beräkningsinfrastrukturen. Om det inte går att storage-tjänsten, resulterar det i ett avbrott i funktionsappen.
Det här scenariot används för att säkerställa kontinuerlig drift av varaktiga funktioner, ett konto för lokal lagring på varje region som funktionsappar som har distribuerats.

![Diagram som visar scenario 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Den här metoden lägger till förbättringar på scenariot ovan:

- Om det inte går att funktionsappen, hand Traffic Manager tar om växling till den sekundära regionen. Men eftersom funktionsappen är beroende av en egen storage-konto, fortsätta varaktiga funktioner att fungera.
- Under en redundansväxling finns det inga ytterligare fördröjning i inte över region, eftersom funktionsappen och storage-konto finns på samma plats.
- Fel i storage-skiktet kommer att orsaka fel i varaktiga funktioner, som i sin tur utlöser en omdirigering till inte över region. Igen, eftersom den funktionsapp och lagring är isolerade per region, varaktiga funktioner kommer att fortsätta att fungera.

Viktigt att tänka på för det här scenariot:

- Om appen har distribuerats med hjälp av en dedikerad App Service-plan, ökar replikerar datainfrastruktur i misslyckas över datacenter kostnaderna.
- Aktuell status är inte växlas över, vilket innebär att köra och med kontrollpunkt funktioner kommer att misslyckas. Det är upp till klientprogrammet återförsök/restart arbetet.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3 – belastningsutjämnade beräkning med GRS som delad lagring

Det här scenariot är en ändring över det första scenariot implementera en delad lagring-konto. Den största skillnaden att lagringskontot har skapats med geo-replikering aktiverat.
Funktionen är det här scenariot ger samma fördelar som Scenario 1, men den ger ytterligare data recovery fördelar:

- GEO-redundant lagring (GRS) och Read-access GRS (RA-GRS) maximera tillgängligheten för ditt lagringskonto.
- Om det finns en region avbrott i tjänsten storage, är en av möjligheterna att störs avgöra att lagring måste flyttas över till den sekundära regionen. I det här fallet omdirigeras åtkomst till lagringskonto transparent till geo-replikerade kopian av storage-konto, utan inblandning av användaren.
- I det här fallet kommer tillståndet för varaktiga funktioner att bevaras upp till den senaste replikeringen av storage-konto, vilket inträffar några minuters mellanrum.

Precis som med andra scenarier, finns det viktiga överväganden:

- Redundansväxlas till repliken görs av datacenteroperatörer och det kan ta lite tid. Tills dess kan blir funktionsappen lidande ett avbrott.
- Det finns en ökad kostnad för att använda geo-replikerade storage-konton.
- GRS sker asynkront. Några av de senaste transaktionerna förlorade på grund av svarstiden för replikeringen.

![Diagram som visar scenario 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om [utforma högtillgängliga program med hjälp av RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
