---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400806"
---
Tal resurs prenumerations nyckel och region krävs för att skapa ett tal konfigurations objekt. Konfigurationsobjektet krävs för att instansiera ett tal igenkännings objekt.

Tolks instansen visar flera olika sätt att känna igen tal. I det här exemplet identifieras tal en gång. Den här funktionen gör att tjänsten Speech vet att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta identifiera tal. När resultatet har lämnats kommer koden att skriva igenkännings orsaken till-konsolen.

> [!TIP]
> Tal-SDK: n kommer att känna igen `en-us` med språket, se [Ange käll språk för tal till text](../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.