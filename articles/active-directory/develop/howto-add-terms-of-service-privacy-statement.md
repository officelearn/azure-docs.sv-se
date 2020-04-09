---
title: Användarvillkor och sekretesspolicy för appar | Azure
description: Lär dig hur du kan konfigurera användarvillkoren och sekretesspolicyn för appar som är registrerade för att använda Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 40e7a05505bc501c1c622e627a6d97cc57db1cfa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884247"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Så här konfigurerar du användarvillkor och sekretesspolicy för en app

Utvecklare som skapar och hanterar appar som integreras med Azure Active Directory (Azure AD) och Microsoft-konton bör inkludera länkar till appens användarvillkor och sekretesspolicy. Villkoren för tjänsten och sekretesspolicyn visas för användarna genom användarsamtyckesupplevelsen. De hjälper användarna att veta att de kan lita på din app. Användarvillkoren och sekretesspolicyn är särskilt viktiga för appar med flera innehavare som används av flera kataloger eller som är tillgängliga för alla Microsoft-konton.

Du är ansvarig för att skapa användarvillkor och sekretessutdragsdokument för din app och för att tillhandahålla webbadresserna till dessa dokument. För appar med flera innehavare som inte tillhandahåller dessa länkar visar användarens medgivandeupplevelse för din app en avisering som kan avskräcka användare från att samtycka till din app.

> [!NOTE]
> * Appar med en klient visar ingen avisering.
> * Om en eller båda länkarna saknas visas en avisering i appen.

## <a name="user-consent-experience"></a>Erfarenhet av användarens medgivande

Följande exempel visar användarmedgivandeupplevelsen när användarvillkoren och sekretesspolicyn är konfigurerade och när dessa länkar inte är konfigurerade.

![Skärmdumpar med och utan en sekretesspolicy och användarvillkor som tillhandahålls](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatera länkar till villkoren för tjänst- och sekretesspolicydokument

Innan du lägger till länkar till appens användarvillkor och sekretesspolicydokument kontrollerar du att webbadresserna följer dessa riktlinjer.

| Riktlinjer     | Beskrivning                           |
|---------------|---------------------------------------|
| Format        | Giltig URL                             |
| Giltiga scheman | HTTP och HTTPS<br/>Vi rekommenderar HTTPS |
| Högsta längd    | 2048 tecken                       |

Exempel: `https://myapp.com/terms-of-service` och`https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Lägga till länkar till användarvillkoren och sekretesspolicyn

När användarvillkoren och sekretesspolicyn är klara kan du lägga till länkar till dessa dokument i appen med någon av följande metoder:

* [Via Azure-portalen](#azure-portal)
* [Använda appobjektet JSON](#app-object-json)
* [Använda Microsoft Graph API](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Använda Azure Portal
Följ dessa steg i Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Navigera till avsnittet **Appregistreringar** och välj din app.
3. Öppna **fönstret Branding.**
4. Fyll i **adressvillkorens webbadress** och **url-fält för sekretesspolicyn.**
5. Spara ändringarna.

    ![Appegenskaper innehåller villkor för tjänst- och sekretesspolicyns webbadresser](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>Använda appobjektet JSON

Om du föredrar att ändra appobjektet JSON direkt kan du använda manifestredigeraren i Azure-portalen eller programregistreringsportalen för att inkludera länkar till appens användarvillkor och sekretesspolicy.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Använda Microsoft Graph API

Om du vill uppdatera alla dina appar program på ett programmatiskt kan du använda Microsoft Graph API för att uppdatera alla dina appar så att de innehåller länkar till villkoren för tjänst- och sekretesspolicydokument.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
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
> * Var noga med att inte skriva över några befintliga värden som `supportUrl`du `marketingUrl`har tilldelat något av dessa fält: , och`logoUrl`
> * Microsoft Graph API fungerar bara när du loggar in med ett Azure AD-konto. Personliga Microsoft-konton stöds inte.
