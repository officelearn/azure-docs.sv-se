---
title: Skapa en business continuty plan för frågor och svar om Maker tjänsten - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Så här skapar du en kontinuitetsplan för frågor och svar om Maker tjänsten
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353985"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Skapa en kontinuitetsplan för frågor och svar om Maker tjänsten

Huvudsyftet med kontinuitetsplan är att skapa en flexibel knowledge base-slutpunkt som skulle se till att ingen stillestånd för Bot eller ett program som använder den.

![Frågor och svar om Maker bcp plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Övergripande idé är som visas ovan följande:

1. Ställa in två parallell [frågor och svar om Maker services](../How-To/set-up-qnamaker-service-azure.md) i [Azure länkas regioner](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

2. Synkronisera de primära och sekundära Azure search-index. Använda github-exemplet [här](https://github.com/pchoudhari/QnAMakerBackupRestore) att se hur du säkerhetskopiering återställning Azure index.

3. Säkerhetskopiera Application Insights med [löpande export](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry).

4. När primära och sekundära platser har konfigurerats kan du använda [trafikhanterarprofil](https://docs.microsoft.com/en-us/azure/traffic-manager/) att konfigurera två slutpunkter och konfigurera en routningsmetod.

5. Du behöver skapa ett SSL-certifikat för din traffic manager-slutpunkt. [Binda SSL-certifikatet](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl) i App-tjänster.

6. Använd slutligen traffic manager-slutpunkt i Bot eller App.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj kapacitet för frågor och svar om Maker distributionen](../Tutorials/choosing-capacity-qnamaker-deployment.md)
