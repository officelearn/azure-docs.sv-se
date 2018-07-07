---
title: Förstå datalagring i LUIS - Azure | Microsoft Docs
description: Lär dig hur data lagras i Språkförståelse (LUIS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: v-geberr
ms.openlocfilehash: 24e179e24423412a5ff25a64157e273b1a025a6f
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888702"
---
# <a name="data-storage-and-removal"></a>Datalagring och borttagning
LUIS lagrar krypterade i ett Azure datalager som motsvarar den region som anges av nyckeln. Dessa data lagras i 30 dagar. 

## <a name="export-and-delete-app"></a>Exportera och ta bort appen
Användarna har full kontroll över [exportera](create-new-app.md#export-app) och [tar bort](create-new-app.md#delete-app) appen. 

## <a name="utterances-in-an-intent"></a>Yttranden i ett intent
Ta bort exempel yttranden som används för träning [LUIS](luis-reference-regions.md). Om du tar bort en exempel-uttryck från LUIS-appen tas bort från LUIS-webbtjänsten och är inte tillgänglig för export.

## <a name="utterances-in-review"></a>Yttranden i granskning
Du kan ta bort yttranden från listan över användare yttranden som LUIS föreslår i den  **[granskningssidan endpoint yttranden](label-suggested-utterances.md)**. Tar bort yttranden i den här listan förhindrar du att de ska visas som förslag, men ta bort inte dem från loggar.

## <a name="accounts"></a>Konton
Om du tar bort ett konto raderas alla appar, och deras exempel yttranden och loggar. Dessa data hålls kvar i 60 dagar innan kontot och data tas bort permanent.

Tar bort kontot är tillgänglig från den **inställningar** sidan. Välj namnet på ditt konto i det övre högra navigeringsfältet för att komma till den **inställningar** sidan.

## <a name="data-inactivity-as-an-expired-subscription"></a>Data inaktivitet som en prenumeration som har upphört att gälla
För datakvarhållning och borttagning, kan en inaktiv LUIS-app på _Microsofts gottfinnande_ behandlas som en prenumeration som har upphört att gälla. En app betraktas som inaktiv om den uppfyller följande kriterier för de senaste 90 dagarna: 

* Har haft **inga** anrop som görs till den.
* Har inte ändrats.
* Inte har en aktuell nyckel tilldelat till den.
* Inte har haft en användarinloggning till den.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om att exportera och ta bort en app](create-new-app.md)