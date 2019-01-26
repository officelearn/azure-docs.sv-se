---
title: Affärskontinuitetsplan – QnA Maker
titleSuffix: Azure Cognitive Services
description: Det primära syftet med affärskontinuitetsplan är att skapa en flexibel kunskapsbas-slutpunkt, vilket skulle se till att inget driftstopp för roboten eller ett program som använder den.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: 9d5c6854090b98559d701329a2ff01ed7185d983
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911038"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Skapa en kontinuitetsplan för QnA Maker-tjänsten

Det primära syftet med affärskontinuitetsplan är att skapa en flexibel kunskapsbas-slutpunkt, vilket skulle se till att inget driftstopp för roboten eller ett program som använder den.

![QnA Maker bcp-plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Tanken på hög nivå är som visas ovan följande:

1. Konfigurera två parallella [QnA Maker tjänster](../How-To/set-up-qnamaker-service-azure.md) i [parade Azure-regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Synkronisera primära och sekundära Azure search-index. Använd GitHub-exempel [här](https://github.com/pchoudhari/QnAMakerBackupRestore) att se hur du säkerhetskopiering – återställning av Azure-index.

3. Säkerhetskopiera en Application Insights med hjälp av [löpande export](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. När de primära och sekundära stackarna har ställts in, använder [trafikhantering](https://docs.microsoft.com/azure/traffic-manager/) att konfigurera två slutpunkter och konfigurera en routningsmetod.

5. Du skulle behöva skapa ett SSL-certifikat för din traffic manager-slutpunkt. [Binda SSL-certifikatet](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) i din App services.

6. Använd slutligen traffic manager-slutpunkt i din robot eller App.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj kapacitet för QnA Maker-distribution](../Tutorials/choosing-capacity-qnamaker-deployment.md)
