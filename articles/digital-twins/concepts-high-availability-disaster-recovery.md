---
title: Hög tillgänglighet och haveriberedskap
titleSuffix: Azure Digital Twins
description: Beskriver Azure och Azures digitala dubbla funktioner som hjälper dig att skapa hög tillgängliga Azure IoT-lösningar med haveri beredskap.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ac75a5b0b59a06855b7ee88d971c269ca915e429
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763175"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digitals dubblare hög tillgänglighet och haveri beredskap

Ett viktigt område med överväganden för elastiska IoT-lösningar är verksamhets kontinuitet och haveri beredskap. Design för **hög tillgänglighet (ha)** och **HAVERI beredskap (Dr)** kan hjälpa dig att definiera och uppnå lämpliga drift tids mål för din lösning.

Den här artikeln beskriver de HA och DR-funktioner som erbjuds specifikt av tjänsten Azure Digitals dubbla.

Azure Digitals dubbla har stöd för dessa funktioner:
* *Intra-region ha* – inbyggd redundans för att leverera drift tiden för tjänsten
* *Mellan region Dr* – redundans till en geo-kopplad Azure-region i händelse av ett oväntat Data Center fel

Du kan också se avsnittet [*metod tips*](#best-practices) för allmän Azure-vägledning om design för ha/Dr.

## <a name="intra-region-ha"></a>HA inom region
 
Azure Digitals dubbla är en del regions hektar genom att implementera redundans inom tjänsten. Detta återspeglas i [service avtalet för tjänsten](https://azure.microsoft.com/support/legal/sla/digital-twins) för drift tid. **Det krävs inget ytterligare arbete för utvecklare av en Azure digital-lösning för att dra nytta av dessa HA-funktioner.** Även om Azures digitala dubbla enheter erbjuder en rimlig hög drifts garanti, kan tillfälliga problem fortfarande förväntas, som med alla distribuerade dator plattformar. Lämpliga principer för återförsök bör vara inbyggda i komponenterna som samverkar med ett moln program för att hantera tillfälliga fel.

## <a name="cross-region-dr"></a>DR mellan regioner

Det kan finnas sällsynta situationer när ett Data Center upplever utökade avbrott på grund av strömavbrott eller andra händelser i regionen. Sådana händelser är sällsynta, och under sådana fel är det inte säkert att de regioner som beskrivs ovan beskrivits. Azure Digital-adresserna löser detta med Microsoft-initierad redundans.

**Microsoft-initierad redundans** utnyttjas av Microsoft i sällsynta fall för att redundansväxla alla Azures digitala dubbla instanser från en berörd region till motsvarande geo-kopplade region. Den här processen är ett standard alternativ (utan möjlighet för användare att avanmäla) och kräver ingen åtgärd från användaren. Microsoft förbehåller sig rätten att fastställa när det här alternativet kommer att användas. Den här mekanismen omfattar inte användarens medgivande innan användarens instans redundansväxlas.

>[!NOTE]
> Vissa Azure-tjänster tillhandahåller också ytterligare ett alternativ som kallas **kundinitierad redundans**, vilket gör det möjligt för kunder att initiera en redundans just för sin instans, till exempel för att köra en Dr-granskning. Den här mekanismen stöds för närvarande **inte** av digitala Azure-dubbla. 

## <a name="best-practices"></a>Bästa praxis

Bästa praxis för HA/DR finns i följande Azure-vägledning om det här ämnet: 
* Den [*tekniska vägledningen för Azures affärs kontinuitet*](/azure/architecture/framework/resiliency/overview) beskriver ett allmänt ramverk som hjälper dig att tänka på verksamhets kontinuitet och haveri beredskap. 
* [*Haveri beredskap och hög tillgänglighet för Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) -programpapper ger arkitektur vägledning om strategier för Azure-program för att uppnå hög tillgänglighet (ha) och haveri beredskap (Dr).

## <a name="next-steps"></a>Nästa steg 

Läs mer om att komma igång med Azure Digitals dubbla lösningar:
 
* [*Vad är Azure Digital Twins?*](overview.md)
* [*Snabb start: utforska ett exempel scenario*](quickstart-adt-explorer.md)