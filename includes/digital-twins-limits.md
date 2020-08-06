---
author: baanders
description: inkludera fil för Azure Digitals dubbla gränser
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 22b00b41c7fce0af57fd9f92b0f42bbd9412afda
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87771108"
---
### <a name="functional-limits"></a>Funktionella gränser

I tabellen nedan visas funktionella gränser för Azure Digital-dubbla i den aktuella för hands versionen.

| Område | Kapacitet | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Azure-resurs | Antal Azure Digitals dubbla instanser i en region, per prenumeration | 10 | Ja |
| Digitala tvillingenheter | Antal dubbla i en digital Azure-instans | 200 000 | Ja |
| Digitala tvillingenheter | Antal inkommande relationer till en enda | 5 000 | Nej |
| Digitala tvillingenheter | Antal utgående relationer från en enda delad | 5 000 | Nej |
| Routning | Antal slut punkter för en enskild Azure Digitals dubbla instans | 6 | Nej |
| Routning | Antal vägar för en enda digital Azure-instans | 6 | Ja |
| Modeller | Antal modeller inom en enskild Azure Digital-instans | 10 000 | Ja |
| Modeller | Antal modeller som kan överföras i ett enda API-anrop | 250 | Nej |
| Modeller | Antal objekt som har returnerats på en enda sida | 100 | Nej |
| Söka i data | Antal objekt som har returnerats på en enda sida | 100 | Nej |
| Söka i data | Antal `AND`  /  `OR` uttryck i en fråga | 50 | Ja |
| Söka i data | Antal mat ris objekt i en `IN`  /  `NOT IN` sats | 50 | Ja |
| Söka i data | Antal tecken i en fråga | 8,000 | Ja |
| Söka i data | Antal `JOINS` i en fråga | 5 | Ja |

### <a name="rate-limits"></a>Hastighetsbegränsningar

Den här tabellen visar frekvens gränserna för olika API: er.

| API | Kapacitet | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Modell-API | Antal begär Anden per sekund | 100 | Ja |
| Digitals dubbla API | Antal begär Anden per sekund | 1 000 | Ja |
| Fråge-API | Antal begär Anden per sekund | 500 | Ja |
| Fråge-API | Fråge enheter per sekund | 4 000 | Ja |
| API för händelse vägar | Antal begär Anden per sekund | 100 | Ja |

### <a name="other-limits"></a>Andra gränser

Gränser för data typer och fält i DTDL-dokument för Azure Digitals-modeller finns i Specifikations dokumentationen i GitHub: [*digital-DTDL Definition Language ()-version 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Information om svars tid och andra rikt linjer för att skriva frågor under för hands versionen finns i [*anvisningar: fråga det dubbla diagrammet*](../articles/digital-twins/how-to-query-graph.md).