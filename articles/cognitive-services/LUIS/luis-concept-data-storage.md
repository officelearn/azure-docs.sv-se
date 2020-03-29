---
title: Datalagring - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS lagrar data krypterade i ett Azure-datalager som motsvarar den region som anges av nyckeln.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220021"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Datalagring och borttagning i cognitive services (Language Understanding)
LUIS lagrar data krypterade i ett Azure-datalager som motsvarar den region som anges av nyckeln. Dessa data lagras i 30 dagar. 

## <a name="export-and-delete-app"></a>Exportera och ta bort app
Användarna har full kontroll över [att exportera](luis-how-to-start-new-app.md#export-app) och [ta bort](luis-how-to-start-new-app.md#delete-app) appen. 

## <a name="utterances"></a>Yttranden

Yttranden kan lagras på två olika platser. 

* Under **redigeringsprocessen**skapas och lagras yttranden i avsikten. Yttranden i avsikter krävs för en framgångsrik LUIS-app. När appen har publicerats och tar emot frågor vid slutpunkten avgör `log=false`slutpunktsbegärans frågesträng , om slutpunktsyttrandet lagras. Om slutpunkten lagras blir den en del av de aktiva inlärningsyttranden som finns i avsnittet **Skapa** i portalen i avsnittet **Granska slutpunktsyttranden.** 
* När du **granskar slutpunktsyttranden**och lägger till ett uttryck i en avsikt lagras uttrycket inte längre som en del av slutpunktsyttrandena som ska granskas. Det läggs till i appens avsikter. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Ta bort exempelyttranden från en avsikt

Ta bort exempelyttranden som används för att träna [LUIS](luis-reference-regions.md). Om du tar bort ett exempelutseende från LUIS-appen tas det bort från LUIS-webbtjänsten och är inte tillgänglig för export.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Ta bort yttranden i granskning från aktiv inlärning

Du kan ta bort yttranden från listan över användaryttranden som LUIS föreslår på **[sidan Granska slutpunktsyttranden](luis-how-to-review-endpoint-utterances.md)**. Om du tar bort yttranden från den här listan kan de föreslås, men de tas inte bort från loggar.

Om du inte vill ha aktiva inlärningsyttranden kan du [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Om du inaktiverar aktiv inlärning inaktiveras även loggning.

### <a name="disable-logging-utterances"></a>Inaktivera loggningsyttranden
[Om du inaktiverar aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning) inaktiveras loggning.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Ta bort ett konto
Om du tar bort ett konto tas alla appar bort tillsammans med deras exempelyttranden och loggar. Uppgifterna lagras i 60 dagar innan kontot och data raderas permanent.

Det är tillgängligt för borttagning av konto på sidan **Inställningar.** Välj ditt kontonamn i det övre högra navigeringsfältet för att komma till sidan **Inställningar.**

## <a name="data-inactivity-as-an-expired-subscription"></a>Datainaktivitet som en utgången prenumeration
För lagring och borttagning av data kan en inaktiv LUIS-app _efter Microsofts gottfinnande_ behandlas som en utgången prenumeration. En app anses vara inaktiv om den uppfyller följande kriterier för de senaste 90 dagarna: 

* Har **inte** haft några samtal till det.
* Har inte ändrats.
* Har ingen aktuell nyckel tilldelad.
* Har inte haft en användare logga in på den.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om att exportera och ta bort en app](luis-how-to-start-new-app.md)
