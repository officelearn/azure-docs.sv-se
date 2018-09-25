---
title: Ordlista – QnA Maker
titleSuffix: Azure Cognitive Services
description: Ordlista
services: cognitive-services
author: tulasim88
manager: cjgronlund
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 65f2aea6ca7b92a1f7d58c607138bbd3388ad30a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954576"
---
# <a name="glossary"></a>Ordlista

## <a name="qna-maker-service"></a>QnA Maker-tjänsten
QnA Maker-tjänsten är ett krav att börja använda QnA Maker. Köpa en QnA Maker-nivå ställer in resurser i din Azure-prenumeration för att skapa och hantera din kunskapsbas. Varje användarkonto för QnA Maker kan skapa flera QnA Maker-tjänster i sin Azure-prenumeration.

## <a name="knowledge-base"></a>Kunskapsbas
En kunskapsbas är lagringsplatsen för frågor och svar skapas, underhålls och användas via QnA Maker. Varje QnA Maker-nivå kan användas för flera kunskapsbaser.

## <a name="endpoint"></a>Slutpunkt
En REST-baserade HTTP-slutpunkt som Underhåll din kunskapsbas-innehåll som kan integreras i ditt program, vanligtvis en chattrobot. 

## <a name="test-knowledge-base"></a>Testa kunskapsbas
En kunskapsbas har två tillstånd - Test och publicerats. Kunskapsbasen test är den version som håller på att redigerar, sparade och testade för korrekta och kompletta svar. Ändringar som gjorts i kunskapsbasen test påverkar inte slutanvändaren av ditt program/chattrobot.

## <a name="published-knowledge-base"></a>Publicerade kunskapsbas
En kunskapsbas har två tillstånd - testning och publicerad.  Publicerade kunskapsbasen är den version som används i din chatt robot eller ditt program. Åtgärd för att publicera en kunskapsbas placerar innehållet i kunskapsbasen Test i den publicerade versionen av kunskapsbasen. Eftersom publicerade kunskapsbasen är den version som används i programmet via slutpunkten kan man att säkerställa att innehållet är korrekt och väl testade.

## <a name="query"></a>Fråga
En användarfråga är frågan som slutanvändaren eller tester frågar i knowledge base. Frågan är ofta på ett naturligt språk-format eller några nyckelord som representerar frågan.

## <a name="response"></a>Svar
Svaret är svaret som hämtats från kunskapsbasen, baserat på bästa möjliga matchning för en viss användare-fråga.

## <a name="confidence-score"></a>Förtroendepoäng
Förtroendepoäng i svaret är ett numeriskt värde mellan 0 och 100, 100 som en exakt fråga matchning mellan användarfråga och en fråga i kunskapsbas som hanteras av svaret är rätt, rätt svar på en viss användare-fråga. Svaren är vanligtvis rankade efter förtroendepoäng och med högre förtroendepoäng skickas som Standardsvaret.
