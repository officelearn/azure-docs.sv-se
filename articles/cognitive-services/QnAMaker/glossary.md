---
title: Ordlista - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Ordlista
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e28cddec005cb6ba99b9f60d8b03a11f1bc97062
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354108"
---
# <a name="glossary"></a>Ordlista

## <a name="qna-maker-service"></a>Frågor och svar om Maker Service
En tjänst för frågor och svar om Maker är ett krav att börja använda frågor och svar om Maker. Köp en nivå med frågor och svar om Maker ordnar resurser i din Azure-prenumeration för att skapa och hantera kunskapsbasen. Varje användarkonto i frågor och svar om Maker kan skapa flera frågor och svar om Maker tjänster i sina Azure-prenumeration.

## <a name="knowledge-base"></a>Kunskapsbas
En Knowledge base är lagringsplatsen för frågor och svar skapas, underhålls och används via frågor och svar om Maker. Varje nivå i frågor och svar om Maker kan användas för flera Knowledge baser.

## <a name="endpoint"></a>Slutpunkt
En REST-baserad HTTP-slutpunkten servicing ditt knowledge base-innehåll som kan integreras i ditt program, vanligtvis en chatt bot. 

## <a name="test-knowledge-base"></a>Testa kunskapsbas
En knowledge base har två lägen - Test och publiceras. Testa knowledge base är den version som håller på att redigerade sparade och testade för korrekta och kompletta svar. Ändringar i kunskapsbasen test påverkar inte slutanvändare för ditt program/chatt bot.

## <a name="published-knowledge-base"></a>Publicerade kunskapsbas
En knowledge base har två lägen - testet och publicerad.  Publicerade kunskapsbasen är den version som används i din chatt bot/application. Åtgärden för att publicera en kunskapsbas placerar innehållet i kunskapsbasen Test i den publicerade versionen av knowledge base. Eftersom publicerade kunskapsbasen är den version som programmet använder via slutpunkten, bör vara försiktig så att innehållet är korrekta och väl testade.

## <a name="query"></a>Fråga
En användarfråga är frågan som slutanvändare eller tester frågar om knowledge base. Frågan är ofta i formatet naturligt språk eller några ord som representerar frågan.

## <a name="response"></a>Svar
Svaret är svaret som hämtats från kunskapsbas, baserat på matchning för en viss användarfråga.

## <a name="confidence-score"></a>Förtroendepoäng
Förtroende poängen för svaret är ett numeriskt värde mellan 0 och 100, 100 som en exakt frågan matchning mellan användarfrågan och en fråga i knowledge base som hanteras av svaret är korrekt, rätt svar för en viss användarfråga. Svaren rangordnas vanligtvis förtroende poäng och hanteras med högre förtroende poängsättningen som standard svaret.
