---
title: Tjänstbegränsningar för allmän förhandsversion
titleSuffix: Azure Digital Twins
description: Diagram som visar gränserna för Azure Digitals dubbla tjänster under den offentliga för hands versionen.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 4497e1222904b1dad5fbeccd942854443e756ed5
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612847"
---
# <a name="service-limits-in-public-preview"></a>Tjänst begränsningar i offentlig för hands version

Det här är tjänst gränserna för Azure Digitals dubbla i den offentliga för hands versionen.

> [!NOTE]
> Vissa tjänster har justerbara gränser som representeras i tabellerna nedan med den *justerbara?* kolumnen. När gränsen kan justeras är värdet för *justerbara?* *Ja*.
>
> Om ditt företag behöver höja en justerbar gräns eller kvot över standard gränsen kan du begära ytterligare resurser genom att [öppna ett support ärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="functional-limits"></a>Funktionella gränser

I tabellen nedan visas funktionella gränser för Azure Digital-dubbla i den aktuella för hands versionen.

| Område | Kapacitet | Gräns | Justerbar? |
| --- | --- | --- | --- |
| Azure-resurs | Antal Azure Digitals dubbla instanser i en region, per prenumeration | 10 | Ja |
| Digitala dubbla | Antal dubbla i en digital Azure-instans | 200 000 | Ja |
| Routning | Antal slut punkter för en enskild Azure Digitals dubbla instans | 6 | Nej |
| Routning | Antal vägar för en enda digital Azure-instans | 6 | Ja |
| Modeller | Antal modeller inom en enskild Azure Digital-instans | 10 000 | Ja |
| Modeller | Antal modeller som kan överföras i ett enda API-anrop | 250 | Nej |
| Modeller | Antal objekt som har returnerats på en enda sida | 100 | Nej |
| Söka i data | Antal objekt som har returnerats på en enda sida | 100 | Nej |
| Söka i data | Antal `AND`  /  `OR` uttryck i en fråga | 50 | Ja |
| Söka i data | Antal mat ris objekt i en `IN`  /  `NOT IN` sats | 50 | Ja |
| Söka i data | Antal tecken i en fråga | 8,000 | Ja |
| Söka i data | Antal `JOINS` i en fråga | 1 | Ja |

## <a name="rate-limits"></a>Hastighets begränsningar

Den här tabellen visar frekvens gränserna för olika API: er.

| API | Kapacitet | Gräns | Justerbar? |
| --- | --- | --- | --- |
| Modell-API | Antal begär Anden per sekund | 100 | Ja |
| Digitals dubbla API | Antal begär Anden per sekund | 1,000 | Ja |
| Fråge-API | Antal begär Anden per sekund | 500 | Ja |
| Fråge-API | Fråge enheter per sekund | 4 000 | Ja |
| API för händelse vägar | Antal begär Anden per sekund | 100 | Ja |

## <a name="other-limits"></a>Andra gränser

Gränser för data typer och fält i DTDL-dokument för Azure Digitals-modeller finns i Specifikations dokumentationen i GitHub: [digital-DTDL Definition Language ()-version 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Information om svars tid och andra rikt linjer för att skriva frågor under för hands versionen finns i [anvisningar: fråga det dubbla diagrammet](how-to-query-graph.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om den aktuella för hands versionen av Azure Digital-dubbla i Översikt över tjänsten:
* [Översikt: Vad är Azure Digitals dubbla?](overview.md)
