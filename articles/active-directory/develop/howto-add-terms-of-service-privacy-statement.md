---
title: Användningsvillkor och sekretesspolicy för Azure AD-appar | Microsoft Docs
description: Lär dig hur du kan konfigurera användningsvillkor och sekretesspolicy för appar som har registrerats för användning av Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 21cd4990e2117cef2d36fa2607a601afd1a4c4e4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990509"
---
# <a name="terms-of-service-and-privacy-statement-for-registered-azure-active-directory-apps"></a>Användningsvillkor och sekretesspolicy för registrerade Azure Active Directory-appar

Utvecklare som skapar och hanterar appar som integreras med Azure Active Directory (Azure AD) och Microsoft-konton bör innehålla länkar till appens användningsvillkor och sekretesspolicy. Användningsvillkor och sekretesspolicy exponeras för användare via medgivande användarupplevelsen. De hjälper dina användare veta att de kan lita på din app. Användningsvillkor och sekretesspolicy är särskilt viktigt på användarinriktade appar för flera appar – appar som används av flera kataloger eller som är tillgängliga för alla microsoftkonto.

Du ansvarar för att skapa användningsvillkor och sekretesspolicy instruktionen dokument för din app och för att tillhandahålla de URL: er till dessa dokument. För appar för flera klienter som inte innehåller dessa länkar, visar medgivande användarupplevelsen för din app en avisering som kan hindra användare från att principer för din app.

> [!NOTE]
> * Appar för en enskild klient visas inte en avisering.
> * Om det saknas en eller båda två länkar, visas en avisering i din app.

## <a name="user-consent-experience"></a>Användarupplevelsen för medgivande

I följande exempel visas att användaren samtycke upplevelsen när användningsvillkor och sekretesspolicy konfigureras och när dessa länkar inte har konfigurerats.

![Skärmbilder med och utan en sekretesspolicy och användningsvillkor som har angetts](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatering länkar till tillhörande användningsvillkor och sekretesspolicy instruktionen dokument

Innan du lägger till länkar till appens användningsvillkor och sekretesspolicy instruktionen dokument kan du kontrollera att URL: er följer dessa riktlinjer.

| Riktlinjer     | Beskrivning                           |
|---------------|---------------------------------------|
| Format        | Giltig URL                             |
| Giltigt scheman | HTTP och HTTPS</br>Vi rekommenderar att HTTPS |
| Maxlängd    | 2048 tecken                       |

Exempel: `https://myapp.com/terms-of-service` och `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Att lägga till användningsvillkor och sekretesspolicy

När användningsvillkor och sekretesspolicy är redo kan du lägga till länkar till dessa dokument i din app med någon av följande metoder:

* [Via Azure portal](#registered-in-azure-portal)
* [I Programregistreringsportalen eller Dev Center](#registered-in-app-reg-portal)
* [Med det app-objektet JSON](#app-object-json)
* [Med hjälp av MSGraph beta REST API](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Om du har registrerat din app i Azure portal

Följ dessa steg om du har registrerat din app i Azure-portalen.

1. Logga in på den [Azure-portalen](https://portal.azure.com/).
2. Navigera till den **Appregistreringar** och väljer din app.
3. Öppna den **egenskaper** i appen.
4. Fyll i **villkoren av tjänstens URL** och **URL till sekretesspolicy** fält.
5. Spara ändringarna.

    ![App properties-avsnittet med villkor för och sekretesspolicy instruktionen URL: er](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Om du har registrerat din app i portalen för registrering av program

Följ dessa steg om du registrerade din app på Programregistreringsportalen eller Dev Center.

1. Logga in på den [Programregistreringsportalen](https://apps.dev.microsoft.com/).
2. Välj din app och bläddra till den **profil** avsnittet.
3. Fyll i **villkoren av tjänstens URL** och **URL till sekretesspolicy** fält.
4. Spara ändringarna.

    ![App profilavsnittet med villkor för och sekretesspolicy instruktionen URL: er](./media/howto-add-terms-of-service-privacy-statement/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Med det app-objektet JSON

Om du föredrar att ändra objektet app JSON direkt, kan du använda manifest-redigeraren i Azure portal eller Programregistreringsportalen för att innehålla länkar till appens användningsvillkor och sekretesspolicy.

```json
    "informationalUrls": { 
        "termsOfService": “<your_terms_of_service_url>”, 
        "privacy": “<your_privacy_statement_url>” 
    }
```

### <a name="msgraph-beta-rest-api"></a>Med hjälp av MSGraph beta REST API

För att programmässigt uppdatera alla dina appar, kan du använda MSGraph beta REST API för att uppdatera alla dina appar för att innehålla länkar till tillhörande användningsvillkor och sekretesspolicy instruktionen dokument.

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
> * Var noga med att inte skriva över alla befintliga värden som du har tilldelat till någon av dessa fält: `supportUrl`, `marketingUrl`, och `logoUrl`
> * MSGraph beta REST-API fungerar bara när du loggar in med ett Azure AD-konto. Personliga Microsoft-konton stöds inte.