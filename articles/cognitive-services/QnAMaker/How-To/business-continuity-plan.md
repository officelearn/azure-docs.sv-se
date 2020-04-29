---
title: Verksamhets kontinuitets plan – QnA Maker
description: Det främsta målet med affärs kontinuitets planen är att skapa en elastisk kunskaps bas slut punkt, vilket skulle göra att det inte går att stänga av roboten eller programmet.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887084"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Skapa en verksamhets kontinuitets plan för din QnA Maker-tjänst

Det främsta målet med affärs kontinuitets planen är att skapa en elastisk kunskaps bas slut punkt, vilket skulle göra att det inte går att stänga av roboten eller programmet.

## <a name="business-continuity-with-traffic-manager"></a>Affärs kontinuitet med Traffic Manager

> [!div class="mx-imgBorder"]
> ![QnA Maker BCP-plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Den övergripande idén som visas ovan är följande:

1. Konfigurera två parallella [QNA Maker tjänster](set-up-qnamaker-service-azure.md) i [Azure-kopplade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Säkerhetskopiera](../../../app-service/manage-backup.md) den primära QNA Maker app-tjänsten och [Återställ](../../../app-service/web-sites-restore.md) den i den sekundära installationen. Detta säkerställer att båda konfigurationerna fungerar med samma värdnamn och nycklar.

3. Behåll de primära och sekundära Azure Search-indexen synkroniserade. Använd exemplet GitHub [här](https://github.com/pchoudhari/QnAMakerBackupRestore) för att se hur du säkerhetskopierar Azure index.

4. Säkerhetskopiera Application Insights med [kontinuerlig export](../../../application-insights/app-insights-export-telemetry.md).

5. När de primära och sekundära stackarna har kon figurer ATS använder du [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) för att konfigurera de två slut punkterna och konfigurera en routningsmetod.

6. Du måste skapa en Transport Layer Security (TLS), som tidigare kallades Secure Sockets Layer (SSL), certifikat för din Traffic Manager-slutpunkt. [BIND TLS/SSL-certifikatet](../../../app-service/configure-ssl-bindings.md) i dina app Services.

7. Använd slutligen Traffic Manager-slutpunkten i din robot eller app.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välja kapacitet](./improve-knowledge-base.md)