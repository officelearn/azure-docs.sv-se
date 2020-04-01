---
title: Kontinuitetsplan för verksamheten - QnA Maker
titleSuffix: Azure Cognitive Services
description: Det primära målet med kontinuitetsplanen är att skapa en flexibel slutpunkt för kunskapsbasen, vilket skulle säkerställa att boten inte får någon tid eller programmet konsumerar den.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 2100426923418104c31933bf9d1406a6441d3841
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410918"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Skapa en kontinuitetsplan för qnA Maker-tjänsten

Det primära målet med kontinuitetsplanen är att skapa en flexibel slutpunkt för kunskapsbasen, vilket skulle säkerställa att boten inte får någon tid eller programmet konsumerar den.

![QnA Maker bcp plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Den höga idén som representeras ovan är följande:

1. Konfigurera två parallella [QnA Maker-tjänster](../How-To/set-up-qnamaker-service-azure.md) i [Azure-parade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Håll de primära och sekundära Azure-sökindexen synkroniserade. Använd GitHub-exemplet [här](https://github.com/pchoudhari/QnAMakerBackupRestore) för att se hur du säkerhetskopierar Azure-index.

3. Säkerhetskopiera programinsikterna med hjälp av [kontinuerlig export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. När de primära och sekundära stackarna har ställts in använder du [traffic manager](https://docs.microsoft.com/azure/traffic-manager/) för att konfigurera de två slutpunkterna och ställa in en routningsmetod.

5. Du måste skapa ett TLS (Transport Layer Security), tidigare kallat SSL (Secure Sockets Layer), för slutpunkten för trafikhanteraren. [Bind TLS/SSL-certifikatet](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) i dina App-tjänster.

6. Slutligen använder du slutpunkten för trafikhanteraren i din Bot eller App.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj capactiy](./improve-knowledge-base.md)
