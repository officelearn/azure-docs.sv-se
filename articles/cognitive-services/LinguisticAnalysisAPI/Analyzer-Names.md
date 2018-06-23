---
title: Analyzer naming strukturen i språkliga analys API | Microsoft Docs
description: Lär dig hur språkliga analys API använder namngivning strukturen för analyzers både flexibel och precision.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/23/2016
ms.author: lesun
ms.openlocfilehash: 2729b7126e82862660fc8e1a995cc87ae996ea03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351570"
---
# <a name="analyzer-names"></a>Analyzer namn

Vi använder en ganska komplicerat namngivning struktur för analyzers för att både på analyzers och precision förstå vad det innebär att ett namn.
Analyzer består av fyra delar: ett ID, en typ, en specifikation och en implementering.
Rollen för varje komponent anges nedan.

## <a name="id"></a>ID
Först har en analyzer ett unikt ID; ett GUID.
Dessa GUID ska ändra relativt sällan, men är det enda sättet att beskriva en viss analyzer unikt.

## <a name="kind"></a>Variant
Därefter varje analyzer är ett **typ**.
Detta definierar i mycket bred villkor typ av analys returnerade och bör unikt definiera datastruktur som används för att representera den analysen.
Det finns tre olika typer:
 - [Token](Sentences-and-Tokens.md)
 - [POS-taggar](Pos-Tagging.md)
 - [Område träd](constituency-parsing.md)

## <a name="specification"></a>Specifikationen
Inom en viss typ, men olika experter kan inte samtycka på hur en viss företeelse bör analyseras.
Till skillnad från programmeringsspråk finns det ingen klar och exakt definition av hur det ska ske.

Anta exempelvis att vi försöker hitta token i engelska meningen ”han inte gå”.
Särskilt bör du strängen ”inte”.
En möjlig tolkning är att detta ska delas upp i två token: ”gjorde” och ”inte”.
Sedan skulle den alternativa meningen ”han inte att gå” ha samma uppsättning token.
En annan möjlighet är att säga ska delas in i token ”matchade” och ””.
Denna token inte skulle normalt anses ett ord, men den här metoden behåller mer information om den ytan strängen, som ibland kan vara användbara.
Eller kanske den minskningen ska betraktas som ett enstaka ord.

Oavsett vad görs valet ska göras konsekvent.
Detta är exakt rollen för en **specifikationen**: för att avgöra vad som ska vara en rätt representation.

Analyzer utdata kan endast jämföras ganska med data som överensstämmer med samma specifikationer.

## <a name="implementation"></a>Implementering

Ofta finns flera modeller som försöker få samma resultat, men med olika prestandaegenskaper.
En modell kan vara snabbare men mindre exakt; en annan kan göra en annan kompromiss.

Den **implementering** delen av ett analyzer namn används för att identifiera den här typen av information, så att användarna kan välja den lämpligaste analyzer för deras behov.
