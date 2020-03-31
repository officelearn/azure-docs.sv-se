---
title: Vad är Blockchain Data Manager för Azure Blockchain Service
description: Blockchain Data Manager för att samla in, transformera och leverera blockchain-data till Event Grid Topics.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209451"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Vad är Blockchain Data Manager för Azure Blockchain Service?

Blockchain Data Manager samlar in, transformerar och levererar Azure Blockchain Service-transaktionsdata till Azure Event Grid-ämnen som ger tillförlitlig och skalbar blockchain ledger-integrering med Azure-tjänster.

I de flesta företags blockchain-scenarier är en blockchain-huvudbok en del av en lösning. Om du till exempel vill överföra en tillgång från en entitet till en annan behöver du en mekanism för att skicka transaktionen. Du behöver sedan en mekanism för att läsa redovisningsdata för att säkerställa att transaktionen inträffade, accepterades och de resulterande tillståndsändringarna integreras sedan med din end-to-end-lösning. I det här exemplet, om du skriver ett smart kontrakt för att överföra tillgångar, kan du använda Blockchain Data Manager för att integrera appar och datalager utanför kedjan. För exemplet med överföring av tillgångar, när en tillgång överförs på blockchain, levereras händelser och egendomstillståndsändringar av Blockchain Data Manager via Event Grid. Du kan sedan använda flera möjliga händelsehanterare för Event Grid för att lagra blockchain-data utanför kedjan eller reagera på tillståndsändringar i realtid.

Blockchain Data Manager utför tre huvudfunktioner: fånga, transformera och leverera.

![Funktioner i Blockchain Data Manager](./media/data-manager/functions.png)

## <a name="capture"></a>Capture

Varje Blockchain Data Manager-instans ansluter till en Azure Blockchain-tjänstmedlemstransaktionsnod. Endast användare med åtkomst till transaktionsnoden kan skapa en anslutning som säkerställer korrekt åtkomstkontroll till kunddata. En Blockchain Data Manager-instans samlar tillförlitligt in alla råblock- och råtransaktionsdata från transaktionsnoden och kan skalas för att stödja företagets arbetsbelastningar.

## <a name="transform"></a>Transformering

Du kan använda Blockchain Data Manager för att avkoda händelse- och egenskapstillstånd genom att konfigurera smarta kontraktsprogram i Blockchain Data Manager. Om du vill lägga till ett smart kontrakt anger du kontraktet ABI och bytecode. Blockchain Data Manager använder smarta kontraktsartefakter för att avkoda och identifiera kontraktsadresser. När blockchain-programmet har lagt till i instansen identifierar Blockchain Data Manager dynamiskt den smarta kontraktsadressen när det smarta kontraktet distribueras till konsortiet och skickar avkodade händelse- och egenskapstillstånd till konfigurerade destinationer.

## <a name="deliver"></a>Leverera

Blockchain Data Manager stöder utgående anslutningar för flera utgående eventrutnät för en viss Blockchain Data Manager-instans. Du kan skicka blockchain-data till en enda destination eller skicka blockchain-data till flera destinationer. Med Blockchain Data Manager kan du skapa en skalbar händelsebaserad datapubliceringslösning för alla blockchain-distributioner.

## <a name="configuration-options"></a>Konfigurationsalternativ

Du kan konfigurera Blockchain Data Manager så att den uppfyller behoven hos din lösning. Du kan till exempel etablera:

* En enda Blockchain Data Manager-instans för en Azure Blockchain Service-medlem.
* En Blockchain Data Manager-instans per Azure Blockchain Service-transaktionsnod. Privata transaktionsnoder kan till exempel ha en egen Blockchain Data Manager-instans för att upprätthålla sekretessen.
* En Blockchain Data Manager-instans kan stödja flera utdataanslutningar. En Blockchain Data Manager-instans kan användas för att hantera alla integrationspunkter för datapublicering för en Azure Blockchain Service-medlem.

## <a name="next-steps"></a>Nästa steg

Prova [att skapa en Blockchain Data Manager-instans](data-manager-portal.md) för en Azure Blockchain Service-medlem.
