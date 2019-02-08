---
title: Analyzer naming struktur - API för Lingvistisk analys
titlesuffix: Azure Cognitive Services
description: Lär dig hur namngivningsstrukturen för Lingvistisk analys API Analyzer ger både flexibilitet och precision.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: b2e956b099022751712f96ab0b15aa57c752b458
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873384"
---
# <a name="analyzer-names"></a>Analyzer-namn

> [!IMPORTANT]
> Förhandsversionen av Språkanalys upphörde den 9 augusti 2018. Vi rekommenderar att du använder [Azure Machine Learning-textanalysmoduler](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) för textbearbetning och -analys.

Vi använder en något komplicerade namngivningsstrukturen för analysverktyg för båda flexibelt på analysverktyg och precision förstå vad ett namn innebär.
Analyzer-namn som består av fyra delar: ett ID, en typ, en specifikation och en implementering.
Rollen för varje komponent definieras nedan.

## <a name="id"></a>ID
Först måste har en analyzer ett unikt ID; ett GUID.
Dessa GUID bör ändras relativt sällan, men är det enda sättet att beskriva en viss analyzer unikt.

## <a name="kind"></a>Typ
Sedan måste varje analyzer är ett **typ**.
Detta definierar mycket mer allmänt typ av analys returneras och unikt bör definiera datastrukturen som används för att representera den här analysen.
Det finns tre olika typer:
 - [Token](Sentences-and-Tokens.md)
 - [POS Tags](Pos-Tagging.md)
 - [Av valkrets trädet](constituency-parsing.md)

## <a name="specification"></a>Specifikationen
Inom en viss typ, men olika experter kan inte samtycka på hur ett visst fenomen bör analyseras.
Till skillnad från programmeringsspråk finns det ingen klar och exakt definition av hur detta ska göras.

Anta exempelvis att vi försöker hitta token i engelska meningen ”han inte gå”.
Tänk särskilt strängen ”inte”.
En möjlig tolkning är att detta ska delas upp i två token: ”gjorde” och ”not”.
Sedan skulle den alternativa meningen ”han inte gå” ha samma uppsättning token.
En annan möjlighet är att säga att det ska delas upp i token ”gjorde” och ””.
Denna token kan inte normalt betraktas som ett ord, men den här metoden innehåller mer information om surface strängen, som ibland kan vara användbara.
Eller kanske den minskningen av ska betraktas som ett enstaka ord.

Oavsett vilken väljs automatiskt, att välja ska göras konsekvent.
Detta är exakt vilken roll en **specifikationen**: för att bestämma vad en korrekt återgivning ska vara.

Endast ganska att jämfört med Analyzer utdata till data som överensstämmer med samma-specifikationen.

## <a name="implementation"></a>Implementering

Det finns ofta flera modeller som försöker uppnå samma resultat, men med olika prestandaegenskaper.
En modell kan vara snabbare dock mindre exakta; en annan kan vara en annan kompromiss.

Den **implementering** delen av ett analyzer-namn används för att identifiera den här typen av information, så att användarna kan välja den lämpligaste analyzer för deras behov.
