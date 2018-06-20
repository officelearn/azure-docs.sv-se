---
title: Förstå datalagring i THOMAS - Azure | Microsoft Docs
description: Lär dig hur data lagras i språk förstå (THOMAS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: v-geberr
ms.openlocfilehash: f235c787e7d2064696e5421219a297d914b5882d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266013"
---
# <a name="data-storage-and-removal"></a>Lagring av data och borttagning
THOMAS lagrar data som krypterats i ett Azure dataarkiv som motsvarar den region som anges av nyckeln. Dessa data lagras i 30 dagar. 

## <a name="export-and-delete-app"></a>Exportera och ta bort appen
Användarna har full kontroll över [exportera](create-new-app.md#export-app) och [bort](create-new-app.md#delete-app) appen. 

## <a name="utterances-in-an-intent"></a>Utterances i syftet
Ta bort exempel utterances som används för träning [THOMAS][LUIS]. Om du tar bort en exempel-utterance från din THOMAS tas bort från webbtjänsten THOMAS och är inte tillgängligt för export.

## <a name="utterances-in-review"></a>Utterances i granskning
Du kan ta bort utterances från listan över användare utterances som THOMAS föreslås i den  **[granskningssidan endpoint utterances](label-suggested-utterances.md)**. Ta bort utterances från den här listan förhindrar som föreslås, men ta bort inte dem från loggar.

## <a name="accounts"></a>Konton
Om du tar bort ett konto raderas alla appar, tillsammans med deras exempel utterances och loggar. Data finns kvar i 60 dagar innan kontot och data tas bort permanent.

Ta bort konto är tillgänglig från den **inställningar** sidan. Välj namnet på ditt konto i det övre högra navigeringsfältet till den **inställningar** sidan.

## <a name="data-inactivity-as-an-expired-subscription"></a>Data inaktivitet som en utgången prenumeration
Vid tillämpningen av datalagring och borttagning, kan en inaktiv THOMAS app på _Microsofts gottfinnande_ behandlas som en utgången prenumeration. En app betraktas som inaktiv om den uppfyller följande kriterier för de senaste 90 dagarna: 

* Har haft **inga** anrop till den.
* Har inte ändrats.
* Inte har en aktuell nyckel tilldelad till den.
* Har inte en användare loggar in på den.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om hur du exporterar och ta bort en app](create-new-app.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions