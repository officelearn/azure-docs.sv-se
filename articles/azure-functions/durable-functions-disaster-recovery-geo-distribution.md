---
title: Återställning och geo-distribution i varaktiga funktioner – Azure
description: Läs mer om återställning och geo-distribution i varaktiga funktioner.
services: functions
author: MS-Santi
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 8eb42a60045304416ec6aa1099a84b1e264c692d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="disaster-recovery-and-geo-distribution"></a>Haveriberedskap och geo-replikering

## <a name="overview"></a>Översikt
I Azure varaktiga funktion sparas alla tillstånd i Azure Storage. En [aktivitet hubb](durable-functions-task-hubs.md) är en logisk behållare för Azure Storage-resurser som används för orkestreringarna. Orchestrator- och aktivitetshantering funktioner kan endast interagera med varandra när de hör till samma aktivitet hubb.
Beskrivs scenarier föreslå distributionsalternativ för att öka tillgängligheten och minimera driftstopp under disaster recovery-aktiviteter.
Det är viktigt att Observera att dessa scenarier baseras på aktivt-passivt konfigurationer, eftersom de vägledning genom användning av Azure Storage. Det här mönstret består av distribuera en app säkerhetskopieringsfunktionen (passivt) till en annan region. Traffic Manager övervakar appen huvudfunktion (aktiv) för tillgänglighet. Den växlar över till säkerhetskopieringsfunktionen appen om primärt misslyckas. Mer information finns i [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)'s [prioritet Trafikroutning metod.](../traffic-manager/traffic-manager-routing-methods.md#a-name--priorityapriority-traffic-routing-method)


>[!NOTE]
>- Den föreslagna aktiv-passivt konfigurationen garanterar att en klient är alltid kunna utlöser nya orkestreringarna via HTTP. Men en följd med två funktionen appar som delar samma lagringsutrymme, distribueras behandling i bakgrunden mellan båda två konkurrerande för meddelanden på samma köer. Den här konfigurationen orsakar tillagda utgång kostnader för den sekundära funktionsapp.
>- Underliggande storage-konto och uppgiften hubben skapas i den primära regionen och delas av både funktionen appar.
>- Alla funktionen appar som distribueras redundant måste dela samma funktion åtkomstnycklarna när det gäller att aktiveras via HTTP. Functions-Runtime Exponerar en [hanterings-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) som gör att kunder kan lägga till, ta bort och uppdatera funktionstangenter.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scenario 1 - belastningsutjämnade beräkning med delad lagring
Om det inte går att beräkningsinfrastrukturen i Azure, kan funktionen appen bli otillgängliga. Detta scenario används för att minimera risken för sådana driftstopp, två funktionen appar som distribueras till olika regioner. Traffic Manager har konfigurerats för att identifiera problem i appen huvudfunktion och automatiskt omdirigera trafik till funktionsapp i den sekundära regionen. Den här funktionen appen delar samma Azure Storage-konto och hubb för aktiviteten. Därför försvinner inte tillståndet för funktionen appar och arbete kan återuppta normalt. När hälsotillståndet återställs till den primära regionen startar Azure Traffic Manager routning förfrågningar till funktionen appen automatiskt.


![Diagram som visar scenario 1.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Det finns flera fördelar när du använder det här distributionsscenariot:
- Om beräkningsinfrastrukturen misslyckas, återupptas arbete om misslyckas över region utan tillstånd.
- Traffic Manager hand tar om automatisk redundans till Felfri funktionen appen automatiskt.
- Traffic Manager återupprättar automatiskt trafik till appen huvudfunktion när driftstörningarna har åtgärdats.

Med det här scenariot överväga:
- Om funktionsapp distribueras med hjälp av en dedikerad programtjänstplan, ökar replikerar beräkningsinfrastrukturen i misslyckas över datacenter kostnaderna.
- Det här scenariot omfattar avbrott beräknings-infrastruktur, men lagringskontot fortsätter att vara felpunkt för App-funktionen. Om det finns ett avbrott för lagring, drabbas programmet av ett driftstopp.
- Om funktionsapp har redundansväxlats blir ökad latens eftersom den ska komma åt dess lagringskonto över regioner.
- Åtkomst till storage-tjänst från en annan region där det är placerad ådrar sig i högre kostnad på grund av utgående nätverkstrafik.
- Det här scenariot beror på Traffic Manager. Med tanke på [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), det kan vara en stund innan ett klientprogram som använder en beständig funktion behöver fråga igen funktionen app-adress från Traffic Manager. 


## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scenario 2: belastningsutjämnade beräkning med regionala lagring
Det föregående scenariot beskriver endast i fallet med fel i beräkningsinfrastrukturen. Om det inte går att storage-tjänsten, resulterar det i ett avbrott i funktionen appen.
Det här scenariot använder ett konto för lokal lagring på varje region som funktionen appar distribueras för att säkerställa kontinuerlig körning av beständiga funktioner.

![Diagram som visar scenario 2.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Den här metoden lägger till förbättringar på scenariot ovan:
- Om funktionsapp misslyckas hand Traffic Manager tar om misslyckas över till den sekundära regionen. Eftersom funktionen appen beroende egen storage-konto, fortsätta varaktiga funktioner ska fungera.
- Under en redundans finns det ingen ytterligare fördröjning i misslyckas över region, eftersom funktionen appen och storage-konto finns på samma plats.
- Fel i lagringsskiktet kommer att orsaka fel i varaktiga funktioner, som i sin tur utlöser en omdirigering till misslyckas över region. Igen, eftersom funktionsapp och lagring är isolerade per region, beständiga funktioner fortsätter att fungera.
 
Viktiga överväganden för det här scenariot:
- Om funktionsapp distribueras med hjälp av en dedikerad programtjänstplan, ökar replikerar beräkningsinfrastrukturen i misslyckas över datacenter kostnaderna.
- Aktuellt tillstånd är inte växlas vid fel, vilket innebär att köra och kontrollpunkt funktioner misslyckas. Den kan klientprogrammet försök/restart arbetet.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scenario 3 – belastningsutjämnade beräkning med GRS som delad lagring
Det här scenariot är en ändring över det första scenariot implementera ett konto med delad lagring. Den största skillnaden att lagringskontot har skapats med geo-replikering aktiverat.
Funktionen är i det här scenariot ger samma fördelar som Scenario 1, men den ger ytterligare data recovery fördelar:
- GEO-redundant lagring (GRS) och GRS läsbehörighet (RA-GRS) maximera tillgängligheten för ditt lagringskonto.
- Om det finns en region avbrott i lagringstjänsten, är en av möjligheterna att åtgärderna datacenter bestämmer att lagring måste flyttas över till den sekundära regionen. I det här fallet omdirigeras konto lagringsåtkomst transparent för geo-replikerade kopian av lagringskonto utan åtgärder från användaren.
- I det här fallet sparas tillståndet för funktionerna varaktiga upp till den senaste replikeringen som det lagringskonto som uppstår några minuters mellanrum.
Det finns viktiga saker som med andra scenarier:
- Redundans till replik utförs av datacenteroperatörer och det kan ta en stund. Funktionen appen blir lidande ett avbrott innan dess.
- Det finns en ökad kostnad för att använda georeplikerad storage-konton.
- GRS inträffar asynkront. Några av de senaste transaktionerna kan gå förlorade på grund av en fördröjning i replikeringen.

![Diagram som visar scenario 3.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)


## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om [skapar hög tillgängliga program med hjälp av RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)
