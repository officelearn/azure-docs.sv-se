---
title: Kontinuitetsplan för verksamheten - QnA Maker
description: Det primära målet med kontinuitetsplanen är att skapa en flexibel slutpunkt för kunskapsbasen, vilket skulle säkerställa att boten inte får någon tid eller programmet konsumerar den.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887084"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Skapa en kontinuitetsplan för qnA Maker-tjänsten

Det primära målet med kontinuitetsplanen är att skapa en flexibel slutpunkt för kunskapsbasen, vilket skulle säkerställa att boten inte får någon tid eller programmet konsumerar den.

## <a name="business-continuity-with-traffic-manager"></a>Affärskontinuitet med trafikchef

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Den höga idén som representeras ovan är följande:

1. Konfigurera två parallella [QnA Maker-tjänster](set-up-qnamaker-service-azure.md) i [Azure-parade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Säkerhetskopiera](../../../app-service/manage-backup.md) din primära QnA Maker App-tjänst och [återställ](../../../app-service/web-sites-restore.md) den i den sekundära installationen. Detta säkerställer att båda installationerna fungerar med samma värdnamn och nycklar.

3. Håll de primära och sekundära Azure-sökindexen synkroniserade. Använd GitHub-exemplet [här](https://github.com/pchoudhari/QnAMakerBackupRestore) för att se hur du säkerhetskopierar Azure-index.

4. Säkerhetskopiera programinsikterna med hjälp av [kontinuerlig export](../../../application-insights/app-insights-export-telemetry.md).

5. När de primära och sekundära stackarna har ställts in använder du [traffic manager](../../../traffic-manager/traffic-manager-overview.md) för att konfigurera de två slutpunkterna och ställa in en routningsmetod.

6. Du måste skapa ett TLS (Transport Layer Security), tidigare kallat SSL (Secure Sockets Layer), för slutpunkten för trafikhanteraren. [Bind TLS/SSL-certifikatet](../../../app-service/configure-ssl-bindings.md) i dina App-tjänster.

7. Slutligen använder du slutpunkten för trafikhanteraren i din Bot eller App.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välja kapacitet](./improve-knowledge-base.md)