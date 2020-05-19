---
title: Lagrings kön som en händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Azure Storage-köer som händelse hanterare för Azure Event Grid händelser.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598544"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Lagrings kön som en händelse hanterare för Azure Event Grid händelser
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Queue Storage** är en av dem. 

Använd **Queue Storage** för att ta emot händelser som behöver hämtas. Du kan använda Queue Storage när du har en lång körnings process som tar för lång tid att svara. Genom att skicka händelser till Queue Storage kan appen Hämta och bearbeta händelser enligt sitt eget schema.

## <a name="tutorials"></a>Självstudier
I följande självstudie finns ett exempel på hur du använder Queue Storage som en händelse hanterare. 

|Titel  |Beskrivning  |
|---------|---------|
| [Snabb start: dirigera anpassade händelser till Azure Queue Storage med Azure CLI och Event Grid](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till en Queue Storage. |

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 
