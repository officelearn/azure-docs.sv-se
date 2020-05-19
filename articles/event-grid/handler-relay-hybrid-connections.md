---
title: Vidarebefordra hybrid anslutning som en händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Azure Relay hybrid anslutningar som händelse hanterare för Azure Event Grid händelser.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598432"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Vidarebefordra hybrid anslutning som en händelse hanterare för Azure Event Grid händelser
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Relay** är en av dem. 

Använd Azure **Relay-hybridanslutningar** för att skicka händelser till program som finns i ett företags nätverk och inte har en offentligt tillgänglig slut punkt.

## <a name="tutorials"></a>Självstudier
I följande självstudie finns ett exempel på hur du använder en Azure Relay hybrid anslutning som händelse hanterare. 

|Titel  |Beskrivning  |
|---------|---------|
| [Självstudie: skicka händelser till hybrid anslutning](custom-event-to-hybrid-connection.md) | Skickar en anpassad händelse till en befintlig hybrid anslutning för bearbetning av ett lyssnar program. |

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 