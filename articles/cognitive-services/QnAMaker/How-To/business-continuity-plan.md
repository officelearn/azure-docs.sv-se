---
title: Verksamhets kontinuitets plan – QnA Maker
titleSuffix: Azure Cognitive Services
description: Det främsta målet med affärs kontinuitets planen är att skapa en elastisk kunskaps bas slut punkt, vilket skulle göra att det inte går att stänga av roboten eller programmet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 0e748e81de39b2bef14b543063adeb51b8b3ecdb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486713"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Skapa en verksamhets kontinuitets plan för din QnA Maker-tjänst

Det främsta målet med affärs kontinuitets planen är att skapa en elastisk kunskaps bas slut punkt, vilket skulle göra att det inte går att stänga av roboten eller programmet.

![QnA Maker BCP-plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Den övergripande idén som visas ovan är följande:

1. Konfigurera två parallella [QNA Maker tjänster](../How-To/set-up-qnamaker-service-azure.md) i [Azure-kopplade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Behåll de primära och sekundära Azure Search-indexen synkroniserade. Använd exemplet GitHub [här](https://github.com/pchoudhari/QnAMakerBackupRestore) för att se hur du säkerhetskopierar Azure index.

3. Säkerhetskopiera Application Insights med [kontinuerlig export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. När de primära och sekundära stackarna har kon figurer ATS använder du [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) för att konfigurera de två slut punkterna och konfigurera en routningsmetod.

5. Du måste skapa ett SSL-certifikat för din Traffic Manager-slutpunkt. [BIND SSL-certifikatet](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) i dina app Services.

6. Använd slutligen Traffic Manager-slutpunkten i din robot eller app.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj kapacitet för din QnA Maker-distribution](../Tutorials/choosing-capacity-qnamaker-deployment.md)
