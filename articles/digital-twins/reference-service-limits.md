---
title: Tjänstbegränsningar för allmän förhandsversion
titleSuffix: Azure Digital Twins
description: Diagram som visar gränserna för Azure Digitals dubbla tjänster under den offentliga för hands versionen.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 7847964d77e5dc09d2e09f207c47f9504c48e1db
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84729208"
---
# <a name="service-limits-in-public-preview"></a>Tjänst begränsningar i offentlig för hands version

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Det här är tjänst gränserna för Azure Digitals dubbla i den offentliga för hands versionen.

> [!NOTE]
> Vissa tjänster har justerbara gränser som representeras i tabellerna nedan med den *justerbara?* kolumnen. När gränsen kan justeras är värdet för *justerbara?* *Ja*.
>
> Om ditt företag behöver höja en justerbar gräns eller kvot över standard gränsen kan du begära ytterligare resurser genom att [öppna ett support ärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="functional-limits"></a>Funktionella gränser

I tabellen nedan visas funktionella gränser för Azure Digital-dubbla i den aktuella för hands versionen.

| Område | Funktion | Gräns | Justerbar? |
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

## <a name="rate-limits"></a>Hastighets begränsningar

Den här tabellen visar frekvens gränserna för olika API: er.

| API | Funktion | Gräns | Justerbar? |
| --- | --- | --- | --- |
| Modell-API | Antal begär Anden per sekund | 100 | Yes |
| Digitals dubbla API | Antal begär Anden per sekund | 1,000 | Yes |
| Fråge-API | Antal begär Anden per sekund | 500 | Yes |
| Fråge-API | Fråge enheter per sekund | 4 000 | Yes |
| API för händelse vägar | Antal begär Anden per sekund | 100 | Yes |

## <a name="other-limits"></a>Andra gränser

Gränser för data typer och fält i DTDL-dokument för Azure Digitals-modeller finns i Specifikations dokumentationen i GitHub: [digital-DTDL Definition Language ()-version 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Information om svars tid och andra rikt linjer för att skriva frågor under för hands versionen finns i [anvisningar: fråga det dubbla diagrammet](how-to-query-graph.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om den aktuella för hands versionen av Azure Digital-dubbla i Översikt över tjänsten:
* [Översikt: Vad är Azure Digitals dubbla?](overview.md)
