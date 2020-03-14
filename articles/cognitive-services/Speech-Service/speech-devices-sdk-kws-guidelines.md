---
title: Rikt linjer för nyckelords namngivning – tal tjänst
titleSuffix: Azure Cognitive Services
description: Att skapa ett effektivt nyckelord är viktigt för att se till att enheten kommer att reagera konsekvent och korrekt.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 3c3943f7269fa2d0ed25acf2215549635b5f16ac
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219570"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Rikt linjer för att skapa ett effektivt nyckelord

Att skapa ett effektivt nyckelord är viktigt för att se till att enheten kommer att reagera konsekvent och korrekt. Att anpassa ditt nyckelord är ett effektivt sätt att särskilja din enhet och förbättra ditt varumärke. I den här artikeln lär du dig några GUID-principer för att skapa ett effektivt nyckelord.

## <a name="choose-an-effective-keyword"></a>Välj ett effektivt nyckelord

Överväg följande rikt linjer när du väljer ett nyckelord:

* Ditt nyckelord bör vara ett engelskt ord eller en fras.

* Det bör ta högst två sekunder för att säga.

* Ord av 4-7 stavelser fungerar bäst. Till exempel "Hej, dator" är ett utmärkt nyckelord. Bara ”Hey” är dålig.

* Nyckelord bör följa vanliga intals regler för engelska uttal.

* Ett unikt eller ett sydda ord som följer den vanliga engelska uttal reglerna kan minska falska positiva identifieringar. Till exempel kan "computerama" vara ett utmärkt nyckelord.

* Välj inte ett vanligt ord. Till exempel ”äta” och ”gå” är ord som personer säga ofta i vanlig konversationen. De kan vara falskt utlösare för din enhet.

* Undvik att använda nyckelord som kan ha alternativa uttal. Användare behöver veta ”höger” uttal att få sin enhet för att svara. Till exempel ”509” förstärkas ”fem noll nio”, ”fem ojsan nio”, eller ”fem hundra och nio”. ”R.E.I.” förstärkas ”r-e-i” eller ”ray”. ”Live” förstärkas ”/līv/” eller ”/liv/”.

* Använd inte specialtecken, symboler och siffror. Till exempel "go #" och "20 + katter" kan vara problematiska nyckelord. Dock ”gå sharp” eller ”tjugo plus katt” fungerar. Du kan fortfarande använda symbolerna i din företagsanpassning och använda marknadsföring och dokumentation för att säkerställa rätt uttal.

> [!NOTE]
> Om du väljer ett tilldelat ord som nyckelord måste du vara säker på att du äger detta varumärke eller att du har behörighet från varumärkes ägaren för att använda ordet. Microsoft ansvarar inte för några juridiska problem som kan uppstå från ditt val av nyckelord.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett anpassat nyckelord med tal Studio](speech-devices-sdk-create-kws.md).
