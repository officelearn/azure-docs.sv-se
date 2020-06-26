---
author: baanders
description: inkludera fil för Azure Digitals dubbla gränser
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 76ccd6f42f2cfd83512a2e817e8c2ac9aa61a28b
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391507"
---
### <a name="functional-limits"></a>Funktionella gränser

I tabellen nedan visas funktionella gränser för Azure Digital-dubbla i den aktuella för hands versionen.

| Område | Funktion | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Azure-resurs | Antal Azure Digitals dubbla instanser i en region, per prenumeration | 10 | Yes |
| Digitala dubbla | Antal dubbla i en digital Azure-instans | 200 000 | Yes |
| Routning | Antal slut punkter för en enskild Azure Digitals dubbla instans | 6 | No |
| Routning | Antal vägar för en enda digital Azure-instans | 6 | Yes |
| Modeller | Antal modeller inom en enskild Azure Digital-instans | 10 000 | Yes |
| Modeller | Antal modeller som kan överföras i ett enda API-anrop | 250 | No |
| Modeller | Antal objekt som har returnerats på en enda sida | 100 | No |
| Söka i data | Antal objekt som har returnerats på en enda sida | 100 | No |
| Söka i data | Antal `AND`  /  `OR` uttryck i en fråga | 50 | Ja |
| Söka i data | Antal mat ris objekt i en `IN`  /  `NOT IN` sats | 50 | Ja |
| Söka i data | Antal tecken i en fråga | 8,000 | Ja |
| Söka i data | Antal `JOINS` i en fråga | 1 | Yes |

### <a name="rate-limits"></a>Hastighets begränsningar

Den här tabellen visar frekvens gränserna för olika API: er.

| API | Funktion | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Modell-API | Antal begär Anden per sekund | 100 | Yes |
| Digitals dubbla API | Antal begär Anden per sekund | 1,000 | Yes |
| Fråge-API | Antal begär Anden per sekund | 500 | Yes |
| Fråge-API | Fråge enheter per sekund | 4 000 | Yes |
| API för händelse vägar | Antal begär Anden per sekund | 100 | Yes |

### <a name="other-limits"></a>Andra gränser

Gränser för data typer och fält i DTDL-dokument för Azure Digitals-modeller finns i Specifikations dokumentationen i GitHub: [digital-DTDL Definition Language ()-version 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Information om svars tid och andra rikt linjer för att skriva frågor under för hands versionen finns i [anvisningar: fråga det dubbla diagrammet](../articles/digital-twins/how-to-query-graph.md).