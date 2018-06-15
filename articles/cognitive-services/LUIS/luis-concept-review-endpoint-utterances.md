---
title: Granska endpoint utterances att använda active learning i språk förstå (THOMAS) - Azure | Microsoft Docs
description: Funktionen active learning med namnet 'Granska endpoint utterances' att förbättra prestanda förutsägelser snabbare.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: b9672e8e63fb601d4411a342b7f3c00e30f9e002
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356438"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Aktivera active learning genom att granska endpoint utterances
Aktiva learning är en av tre strategier för att förbättra förutsägelsefunktionen och enklast att implementera. 

## <a name="what-is-active-learning"></a>Vad är aktiva learning
Aktiva inlärning är en tvåstegsprocess. Först väljer THOMAS utterances tas emot i appens slutpunkten som validering krävs. Det andra steget utförs av app ägare eller deltagare för att verifiera de valda utterances för [granska](label-suggested-utterances.md), inklusive rätt avsikten och alla entiteter i avsikten. När du har granskat utterances träna och publicera appen igen. 

## <a name="which-utterances-are-on-the-review-list"></a>Vilka utterances finns på listan Granska
THOMAS lägger till utterances granska listan när upp startar avsikten har en låg poäng eller den övre två avsikter resultat för Stäng. 

## <a name="where-are-the-utterances-from"></a>Var finns utterances från
Slutpunkten utterances hämtas från slutanvändarens frågor om programmets HTTP-slutpunkten. Om din app publiceras inte eller har inte tagits emot träffar ännu, har inte någon utterances att granska. Om ingen slutpunkt träffar tas emot för en specifik avsikt eller enhet har inte utterances att granska som dessa. 

## <a name="schedule-review-periodically"></a>Schemalägga Granska regelbundet
Granska föreslagna utterances behöver inte göras varje dag men bör vara en del av din regelbundet underhåll av THOMAS. 

## <a name="delete-review-items-programmatically"></a>Ta bort granska objekt programmässigt
Om din app är stor, kan du granska vissa utterances och ta bort resten från listan över programmässigt. Detta görs genom första [komma](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) i listan och sedan [bort](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) utterances efter-ID.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [granska](Label-Suggested-Utterances.md) endpoint utterances
