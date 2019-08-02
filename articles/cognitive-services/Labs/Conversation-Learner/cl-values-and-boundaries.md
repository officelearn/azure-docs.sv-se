---
title: Conversation Learner standard konfiguration – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om standard konfigurationen för Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: aaef6f5498e5a8da65d1c829feae8b3e85dba0fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705300"
---
# <a name="default-values-and-boundaries"></a>Standardvärden och gränser

I det här dokumentet beskrivs standard konfigurationen av Conversation Learner och viktiga tjänst gränser.

## <a name="default-configuration"></a>Standard konfiguration

Parameter | Standardvärde
--- | --- 
Timeout för standardsession | 30 minuter

## <a name="boundaries"></a>Gränser

Parameter | Gräns
--- | --- 
Redigering av API, Max HTTP-anrop per månad | 5 M
Redigerings-API, Max HTTP-anrop per sekund | 25
Sessions-API, Max HTTP-anrop per månad | 500 000
Sessions-API, Max HTTP-anrop per sekund | 10
Maximalt antal anpassade entiteter (icke-programmatiska) per modell | Se [Luis gräns dokument](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries); i praktiken kan det faktiska antalet vara något mindre
Maximalt antal förbyggda entiteter per modell | Se [Luis gräns dokument](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
Max antal entiteter (totalt) per modell | 100
Maximalt antal åtgärder per modell | 32
Maximalt antal träna-dialoger per modell | 1000
Högsta antal användare som aktiverar per träna-dialog ruta | 100
Max antal logg dialog rutor per modell | Ingen fördefinierad gräns, men logg dialog rutor behålls bara för en fast period innan de tas bort.  Dessutom visar Conversation Learner UI 100-logg dialog rutor i taget. 
Högsta antal modeller per användare | Ingen för inställnings gräns
Maximalt antal sekventiella icke-waitiska åtgärder | 5 (*)

(*) Efter 5 sekventiella icke-wait-åtgärder maskeras alla icke-waitiska åtgärder och Conversation Learner väljer mellan tillgängliga wait-åtgärder.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med Conversation Learner](./quickstart.md)
