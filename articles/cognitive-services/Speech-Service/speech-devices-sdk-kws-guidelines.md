---
title: Riktlinjer för namngivning för nyckelord - Taltjänst
titleSuffix: Azure Cognitive Services
description: Att skapa ett effektivt sökord är avgörande för att säkerställa att enheten konsekvent och korrekt svarar.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 1d363f9f6f9cb5b1ea28e6ae15a9cef857304cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370686"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Riktlinjer för att skapa ett effektivt sökord

Att skapa ett effektivt sökord är avgörande för att säkerställa att enheten konsekvent och korrekt svarar. Anpassa ditt sökord är ett effektivt sätt att differentiera din enhet och stärka ditt varumärke. I den här artikeln lär du dig några vägledande principer för att skapa ett effektivt sökord.

## <a name="choose-an-effective-keyword"></a>Välj ett effektivt sökord

Tänk på följande riktlinjer när du väljer ett sökord:

> [!div class="checklist"]
> * Ditt sökord ska vara ett engelskt ord eller en fras.
> * Det bör inte ta längre tid än två sekunder att säga.
> * Ord av 4 till 7 stavelser fungerar bäst. Till exempel är "Hej, dator" ett bra sökord. Bara "Hej" är en dålig en.
> * Nyckelord bör följa vanliga engelska uttalsregler.
> * Ett unikt eller till och med ett påhittad ord som följer vanliga engelska uttalsregler kan minska falska positiva identifieringar. Till exempel kan "computerama" vara ett bra nyckelord.
> * Välj inte ett vanligt ord. Till exempel "äta" och "gå" är ord som folk säger ofta i vanliga samtal. De kan vara falska utlösare för din enhet.
> * Undvik att använda ett sökord som kan ha alternativa uttal. Användare skulle behöva veta "rätt" uttal för att få sin enhet att svara. Till exempel kan "509" uttalas "fem noll nio", "fem oh nio" eller "femhundranio" "R.E.I." kan uttalas "r-e-i" eller "ray". "Live" kan uttalas "/līv/" eller "/liv/".
> * Använd inte specialtecken, symboler eller siffror. Till exempel kan "Go#" och "20 + cats" vara problematiska sökord. Men "gå skarp" eller "tjugo plus katter" kan fungera. Du kan fortfarande använda symbolerna i ditt varumärke och använda marknadsföring och dokumentation för att förstärka korrekt uttal.

> [!NOTE]
> Om du väljer ett varumärkesskyddat ord som sökord måste du se till att du äger varumärket eller att du har tillstånd från varumärkesägaren att använda ordet. Microsoft ansvarar inte för eventuella juridiska problem som kan uppstå vid val av sökord.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett anpassat nyckelord med Speech Studio](speech-devices-sdk-create-kws.md).
