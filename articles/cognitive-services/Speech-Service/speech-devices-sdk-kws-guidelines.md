---
title: Rikt linjer för nyckelords namngivning – tal tjänst
titleSuffix: Azure Cognitive Services
description: Att skapa ett effektivt nyckelord är viktigt för att se till att enheten kommer att reagera konsekvent och korrekt.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: c00d27e5e7b7a8d614ce94ea4024b6093669757c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399835"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Rikt linjer för att skapa ett effektivt nyckelord

Att skapa ett effektivt nyckelord är viktigt för att se till att enheten kommer att reagera konsekvent och korrekt. Att anpassa ditt nyckelord är ett effektivt sätt att särskilja din enhet och förbättra ditt varumärke. I den här artikeln lär du dig några GUID-principer för att skapa ett effektivt nyckelord.

## <a name="choose-an-effective-keyword"></a>Välj ett effektivt nyckelord

Överväg följande rikt linjer när du väljer ett nyckelord:

> [!div class="checklist"]
> * Ditt nyckelord bör vara ett engelskt ord eller en fras.
> * Det bör inte ta mer än två sekunder att säga.
> * Ord av 4 till 7 stavelser fungerar bäst. Till exempel "Hej, dator" är ett utmärkt nyckelord. Bara "Hej" är ett dåligt.
> * Nyckelord bör följa vanliga intals regler för engelska uttal.
> * Ett unikt eller till och med ett upprättat ord som följer vanliga regler för engelska uttal kan minska antalet falska positiva identifieringar. Till exempel kan "computerama" vara ett utmärkt nyckelord.
> * Välj inget vanligt ord. Till exempel är "äta" och "go" ord som människor säger ofta i vanlig konversation. De kan vara falska utlösare för enheten.
> * Undvik att använda nyckelord som kan ha alternativa uttal. Användarna måste känna till "Right"-uttal för att få enheten att svara. "509" kan till exempel uttalas "5 0 9", "5 0 9" eller "509". "R.E.I." kan uttalas "r-e-i" eller "Ray". "Live" kan uttalas "/līv/" eller "/liv/".
> * Använd inte specialtecken, symboler eller siffror. Till exempel "go #" och "20 + katter" kan vara problematiska nyckelord. Men "gå skarpa" eller "tjugo plus katter" kan fungera. Du kan fortfarande använda symbolerna i din anpassning och använda marknadsföring och dokumentation för att förstärka rätt uttal.

> [!NOTE]
> Om du väljer ett tilldelat ord som nyckelord måste du vara säker på att du äger detta varumärke eller att du har behörighet från varumärkes ägaren för att använda ordet. Microsoft ansvarar inte för några juridiska problem som kan uppstå från ditt val av nyckelord.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett anpassat nyckelord med tal Studio](speech-devices-sdk-create-kws.md).
