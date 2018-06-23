---
title: Villkor för tjänsten och sekretesspolicy för Azure AD-appar | Microsoft Docs
description: Lär dig hur du kan konfigurera villkor för tjänsten och sekretesspolicy av appar som har registrerats för användning av Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2018
ms.author: celested
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 2dff8c25ae0704a15cf02eec7a380b6d894b9ce4
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316458"
---
# <a name="terms-of-service-and-privacy-statement-for-registered-azure-active-directory-apps"></a>Villkor för tjänsten och sekretesspolicyn för registrerade Azure Active Directory-appar

Utvecklare som skapar och hanterar appar som integreras med Azure Active Directory (Azure AD) och Microsoft-konton ska innehålla länkar till appens villkor för tjänsten och sekretesspolicy. Villkor för tjänsten och sekretesspolicy är anslutna till användare via medgivande användarupplevelsen. De hjälper användarna vet att de kan lita på din app. Villkor för tjänsten och sekretesspolicy är särskilt viktiga för användarinriktad flera innehavare appar – appar som används av flera kataloger eller som är tillgängliga för alla Microsoft-konto.

Du ansvarar för att skapa villkor för tjänsten och sekretess instruktionen dokument för din app och för att tillhandahålla de URL: er till dessa dokument. För flera innehavare-appar som inte innehåller dessa länkar visas medgivande användarupplevelsen för din app en avisering som kan hindra användare från principer för din app.

> [!NOTE]
> * Stöd för en innehavare appar visas inte en avisering.
> * Om det saknas en eller båda av de två länkarna, visas en avisering i din app.

## <a name="user-consent-experience"></a>Medgivande användarupplevelse

Följande exempel visar användaren samtycke upplevelse när villkoren i tjänsten och sekretesspolicy konfigureras och när dessa länkar inte har konfigurerats.

![Skärmbilder med och utan en sekretesspolicy och villkor för tjänsten som tillhandahållits](./media/active-directory-integrating-applications/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatering länkar till villkoren i tjänsten och sekretess instruktionen dokument

Kontrollera URL: er följer dessa riktlinjer innan du lägger till länkar för din app service och sekretess instruktionen dokument.

| Riktlinje     | Beskrivning                           |
|---------------|---------------------------------------|
| Format        | Ogiltig URL-adress                             |
| Giltig scheman | HTTP och HTTPS</br>Vi rekommenderar att HTTPS |
| Maxlängd    | 2048 tecken                       |

Exempel: `https://myapp.com/terms-of-service` och `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Lägga till länkar i villkoren för tjänsten och sekretesspolicy

När villkoren i tjänsten och sekretesspolicy är klar kan du lägga till länkar till dessa dokument i din app använder någon av följande metoder:
* [Via Azure portal](#registered-in-azure-portal)
* [I portalen för registrering av program eller Dev Center](#registered-in-app-reg-portal)
* [Med app-objektet JSON](#app-object-json)
* [Med hjälp av MSGraph beta REST API](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Om du har registrerat din app i Azure-portalen
Följ dessa steg om du har registrerat din app i Azure-portalen.

1. Logga in på den [Azure-portalen](https://portal.azure.com/).
2. Navigera till den **App registreringar** och välj din app.
3. Öppna den **egenskaper** avsnitt i appen.
4. Fyll i den **villkoren Tjänstwebbadress** och **URL till sekretesspolicy** fält.
5. Spara ändringarna.

![Appen properties-avsnittet med villkor för tjänsten och sekretess instruktionen URL: er](./media/active-directory-integrating-applications/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Om du har registrerat din app i portalen för registrering av programmet
Följ dessa steg om du har registrerat din app i portalen för registrering av program eller Dev Center.

1. Logga in på den [Programregistreringsportalen](https://apps.dev.microsoft.com/).
2. Välj din app och bläddra till den **profil** avsnitt.
3. Fyll i den **villkoren Tjänstwebbadress** och **URL till sekretesspolicy** fält.
4. Spara ändringarna.

![Appen profil avsnitt med villkor i tjänsten och sekretess instruktionen URL: er](./media/active-directory-integrating-applications/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Med app-objektet JSON
Om du vill ändra objektet app JSON direkt, kan du använda redigeraren manifestet i Azure-portalen eller Programregistreringsportalen för att innehålla länkar till appens villkor för tjänsten och sekretesspolicy.

```json
    "informationalUrls": { 
        "termsOfService": “<your_terms_of_service_url>”, 
        "privacy": “<your_privacy_statement_url>” 
    }
```

### <a name="msgraph-beta-rest-api"></a>Med hjälp av MSGraph beta REST API
Om du vill uppdatera alla dina appar programmässigt, kan du använda MSGraph beta REST API för att uppdatera alla dina appar för att innehålla länkar till villkoren i tjänsten och sekretess instruktionen dokument.

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
> * MSGraph beta REST API fungerar endast när du loggar in med ett konto i Azure AD. Personliga Microsoft-konton stöds inte.