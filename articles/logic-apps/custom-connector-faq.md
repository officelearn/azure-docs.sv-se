---
title: "Anpassad koppling vanliga frågor och svar – Azure Logic Apps | Microsoft Docs"
description: "Vanliga frågor om krav, utlösare, och så vidare om hur du skapar anpassade kopplingar"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>Vanliga frågor och svar: Anpassad kopplingar

## <a name="requirements"></a>Krav

**F:** kan jag skapa en anslutning utan REST API: er? </br>
**S:** Nej, för att skapa en koppling, du måste ha stöd för stabil http-REST API: er för din tjänst. 

**F:** vilka verktyg kan jag använda för att skapa en koppling? </br>
**S:** Azure har funktioner och tjänster som du kan använda för att visa alla tjänster som en API, till exempel Azure App Service som värd, API-hantering och mycket mer.

**F:** vilka typer av autentisering stöds? </br>
**S:** du kan använda dessa standarder för autentiseringsmetoder som stöds:

* [OAuth 2.0](https://oauth.net/2/), inklusive [Azure Active Directory](https://azure.microsoft.com/develop/identity/) eller specifika tjänster, till exempel Dropbox, GitHub och SalesForce
* Allmän OAuth 2.0
* [Grundläggande autentisering](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [API-nyckel](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>Utlösare

**F:** kan jag skapa utlösare utan webhooks? </br>
**S:** Nej, anpassade kopplingar för Logikappar i Azure och Microsoft Flow stöd endast webhook-baserade utlösare. Om du vill begära andra mönster för implementering kontaktar [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com) med mer information om din API.

## <a name="certification"></a>Certifiering

**Q**: Jag är inte ett Microsoft-partner eller oberoende leverantör (ISV). Kan jag fortfarande skapa kopplingar? </br>
**En**: Ja, kan du registrera dessa kopplingar för intern användning i din organisation, men om du vill att certifiera och offentligt frigöra en koppling, måste du antingen äga den underliggande tjänsten eller finns explicit behörighet för att använda API: et.

## <a name="other"></a>Annat

**F:** Mina API: er använder en dynamisk värd. Hur jag implementera dem med OpenAPI? </br>
**S:** anpassad kopplingar stöder inte dynamisk värdar. Använd istället en statisk värd för utveckling och testning. Om du vill att certifiera din koppling, be din Microsoft-kontakt om dynamiska implementering.

**F:** stöder du Postman samling V2? </br>
**S:** Nej, endast Postman samling V1 stöds för närvarande.

**F:** stöder du OpenAPI 3.0? </br>
**S:** Nej, endast OpenAPI 2.0 stöds för närvarande.