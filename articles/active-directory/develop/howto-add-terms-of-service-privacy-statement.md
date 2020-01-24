---
title: Villkor för service och sekretess policy för appar | Azure
description: Lär dig hur du kan konfigurera villkoren för tjänsten och sekretess policyn för registrerade appar för att använda Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: f28c33f20556825d84edda34752ac64714327526
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697344"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Gör så här: konfigurera villkor för service och sekretess policy för en app

Utvecklare som skapar och hanterar appar som integreras med Azure Active Directory (Azure AD) och Microsoft-konton bör innehålla länkar till appens användnings villkor och sekretess policy. Användnings villkoren och sekretess policyn är tilldelad till användarna genom användar medgivande upplevelsen. De hjälper användarna att veta att de kan lita på din app. Villkoren för service och sekretess är särskilt viktiga för användare som använder appar för flera klient organisationer – appar som används av flera kataloger eller som är tillgängliga för alla Microsoft-konto.

Du ansvarar för att skapa användar villkoren för tjänsten och sekretess policyn för din app och för att tillhandahålla URL: erna till dessa dokument. För appar för flera klienter som inte kan tillhandahålla dessa länkar visas en avisering i användar medgivande för appen, vilket kan hindra användarna från att bli meddelad till din app.

> [!NOTE]
> * Appar för en enda klient visas inte en avisering.
> * Om en eller båda av de två länkarna saknas visas en avisering i appen.

## <a name="user-consent-experience"></a>Användar tillstånds upplevelse

I följande exempel visas användarnas medgivande när tjänst villkoren och sekretess policyn konfigureras och när dessa länkar inte har kon figurer ATS.

![Skärm dum par med och utan sekretess policy och tillhandahållna tjänster](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatera länkar till villkoren för service och sekretess policy

Innan du lägger till länkar till appens användar villkor och sekretess policys-dokument måste du kontrol lera att URL: erna följer dessa rikt linjer.

| Riktlinjer     | Beskrivning                           |
|---------------|---------------------------------------|
| Format        | Giltig URL                             |
| Giltiga scheman | HTTP och HTTPS<br/>Vi rekommenderar HTTPS |
| Högsta längd    | 2048 tecken                       |

Exempel: `https://myapp.com/terms-of-service` och `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Lägga till länkar till villkoren för tjänsten och sekretess policyn

När tjänst villkoren och sekretess policyn är klara kan du lägga till länkar till dessa dokument i din app med någon av följande metoder:

* [Via Azure Portal](#azure-portal)
* [Använda app Object JSON](#app-object-json)
* [Använda MSGraph beta REST API](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Använda Azure Portal
Följ de här stegen i Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till avsnittet **app Registration** och välj din app.
3. Öppna fönstret **varumärkes anpassning** .
4. Fyll i URL **-fälten för användnings villkoren** och **URL: en för sekretess policy** .
5. Spara ändringarna.

    ![App-egenskaperna innehåller URL: er för användnings villkor och sekretess policy](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Använda app Object JSON

Om du föredrar att ändra programobjekt-JSON direkt kan du använda manifest redigeraren i Azure Portal-eller program registrerings portalen för att ta med länkar till appens användnings villkor och sekretess policy.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Använda MSGraph beta REST API

Om du vill uppdatera alla dina appar program mässigt kan du använda MSGraph beta REST API för att uppdatera alla dina appar för att inkludera länkar till villkoren för service och sekretess policy.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Var noga med att inte skriva över befintliga värden som du har tilldelat något av dessa fält: `supportUrl`, `marketingUrl`och `logoUrl`
> * MSGraph beta-REST API fungerar bara när du loggar in med ett Azure AD-konto. Personliga Microsoft-konton stöds inte.
