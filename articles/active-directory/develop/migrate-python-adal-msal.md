---
title: Python ADAL till MSAL-migreringsguide | Azure
description: Lär dig hur du migrerar ADAL-appen (Azure Active Directory Authentication Library) till MSAL (Microsoft Authentication Library) för Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696571"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>ADAL till MSAL-migreringsguide för Python

I den här artikeln beskrivs de ändringar du behöver göra för att migrera en app som använder Azure Active Directory Authentication Library (ADAL) för att använda Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Skillnaden höjdpunkter

ADAL fungerar med Azure Active Directory (Azure AD) v1.0-slutpunkten. Microsoft Authentication Library (MSAL) fungerar med Microsoft identity-plattformen – tidigare känt som Slutpunkten för Azure Active Directory v2.0. Microsoft-identitetsplattformen skiljer sig från Azure AD v1.0 genom att den:

Stöder:
  - Arbets- och skolkonton (Azure AD-etablerade konton)
  - Personliga konton (till exempel Outlook.com eller Hotmail.com)
  - Dina kunder som tar med egen e-post eller social identitet (till exempel LinkedIn, Facebook, Google) via Azure AD B2C-erbjudandet

- Är standarder kompatibla med:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Mer information [finns i Microsoft identity-plattformen (v2.0).Se What's different about the Microsoft identity platform (v2.0) endpoint?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

### <a name="scopes-not-resources"></a>Scope inte resurser

ADAL Python hämtar token för resurser, men MSAL Python hämtar token för scope. API-ytan i MSAL Python har inte längre resursparametern. Du måste ange scope som en lista över strängar som deklarerar önskade behörigheter och resurser som begärs. Information om några exempel på scope finns i [Microsoft Graph scope](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Felhantering

Azure Active Directory Authentication Library (ADAL) `AdalError` för Python använder undantaget för att indikera att det har uppstått ett problem. MSAL för Python använder vanligtvis felkoder i stället. Mer information finns i [MSAL för Python-felhantering](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>API-ändringar

I följande tabell visas ett API i ADAL för Python och det som ska användas i dess ställe i MSAL för Python:

| ADAL för Python API  | MSAL för Python API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication eller ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Ej tillämpligt  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Ej tillämpligt |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) och [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) och [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| Ej tillämpligt | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| Ej tillämpligt | Cache med persistens, tillgänglig från [MSAL-tillägg](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrera befintliga uppdateringstoken för MSAL Python

I Microsofts autentiseringsbibliotek (MSAL) sammanfattas begreppet uppdateringstoken. MSAL Python tillhandahåller som standard en tokencache i minnet så att du inte behöver lagra, söka efter eller uppdatera uppdateringstoken. Användarna ser också färre inloggningsanytor eftersom uppdateringstoken vanligtvis kan uppdateras utan att användaren behöver göra något. Mer information om tokencachen finns [i Anpassad tokencacheseriering i MSAL för Python](msal-python-token-cache-serialization.md).

Följande kod hjälper dig att migrera dina uppdateringstoken som hanteras av ett annat OAuth2-bibliotek (inklusive men inte begränsat till ADAL Python) som ska hanteras av MSAL för Python. En orsak till att migrera dessa uppdateringstoken är att förhindra att befintliga användare behöver logga in igen när du migrerar appen till MSAL för Python.

Metoden för att migrera en uppdateringstoken är att använda MSAL för Python för att hämta en ny åtkomsttoken med hjälp av föregående uppdateringstoken. När den nya uppdateringstoken returneras lagras den i cachen MSAL för Python. Här är ett exempel på hur man gör det:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [v1.0 och v2.0-jämförelsen](active-directory-v2-compare.md).
