---
title: Azure Active Directory REST API-autentisering
description: Använd Azure Active Directory för att autentisera till Azure App konfiguration med hjälp av REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424417"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory-autentisering

HTTP-begäranden kan autentiseras med hjälp av **Bearer** -autentiseringsschemat med en token som hämtats från Azure Active Directory (Azure AD). Dessa begär Anden måste överföras via TLS.

## <a name="prerequisites"></a>Förutsättningar

Huvud kontot som ska användas för att begära en Azure AD-token måste tilldelas en av de tillämpliga [konfigurations rollerna för appen](./rest-api-authorization-azure-ad.md)

Ange varje begäran med alla HTTP-huvuden som krävs för autentisering. Minimi kravet är:

|  Begärandehuvud | Description  |
| --------------- | ------------ |
| **Auktorisering** | Autentiseringsinformation som krävs av **Bearer** -schemat. Format och information beskrivs nedan. |

**Exempel:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Hämtning av Azure Active Directory-token

Innan du skaffar en Azure AD-token måste du identifiera vilken användare de vill autentisera som, vilken mål grupp de begär token för, och vilken Azure AD-slutpunkt (auktoritet) som de ska använda.

### <a name="audience"></a>Målgrupp

Azure AD-token måste begäras med rätt mål grupp. För Azure App konfiguration ska en av följande mål grupper anges när du begär en token. Mål gruppen kan också kallas "resurs" som token begärs för.

- {configurationStoreName}. azconfig. io
- *. azconfig.io

> [!IMPORTANT]
> När den begärda mål gruppen är {configurationStoreName}. azconfig. io måste den exakt matcha "värd" begär ande huvudet (Skift läges känsligt) som används för att skicka begäran.

### <a name="azure-ad-authority"></a>Azure AD-auktoritet

Azure AD-utfärdaren är den slut punkt som används för att förvärva en Azure AD-token. Den är i form av `https://login.microsoftonline.com/{tenantId}` . `{tenantId}`Segmentet refererar till Azure Active Directory klient-ID som användaren/programmet som försöker autentisera tillhör.

### <a name="authentication-libraries"></a>Autentiseringsbibliotek

Azure tillhandahåller en uppsättning bibliotek som kallas Azure Active Directory ADAL (Authentication Libraries) för att förenkla processen att förvärva en Azure AD-token. Dessa bibliotek skapas för flera språk. Dokumentation hittar du [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).

## <a name="errors"></a>**Fel**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Orsak:** Huvud för auktoriseringsbegäran med Bearer-schema har inte angetts.
**Lösning:** Ange ett giltigt ```Authorization``` http-begär ande huvud

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Orsak:** Azure AD-token är inte giltig.
**Lösning:** Skaffa en Azure AD-token från Azure AD-utfärdaren och se till att rätt mål grupp används.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Orsak:** Azure AD-token är inte giltig.
**Lösning:** Skaffa en Azure AD-token från Azure AD-utfärdaren och se till att Azure AD-klienten är den som är associerad med den prenumeration som konfigurations lagret tillhör. Det här felet kan uppstå om huvudobjektet tillhör fler än en Azure AD-klient.
