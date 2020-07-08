---
title: Användar flöden i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om den utöknings bara princip ramverket för Azure Active Directory B2C och hur du skapar olika användar flöden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c11bc48742c398d2048a236c7d00af044971f845
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78185628"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Användar flöden i Azure Active Directory B2C

Den utöknings bara princip ramverket för Azure Active Directory B2C (Azure AD B2C) är tjänstens kärn styrka. Principer beskriver fullständigt identitets upplevelser som registrering, inloggning eller profil redigering. För att hjälpa dig att skapa de vanligaste identitets uppgifterna innehåller Azure AD B2C portalen fördefinierade, konfigurerbara principer som kallas **användar flöden**.

## <a name="what-are-user-flows"></a>Vad är användar flöden?

Med ett användar flöde kan du styra beteenden i dina program genom att konfigurera följande inställningar:

- Konto typer som används för inloggning, till exempel sociala konton som ett Facebook-eller lokala konto
- Attribut som ska samlas in från konsumenten, till exempel förnamn, post nummer och sko storlek
- Azure Multi-Factor Authentication
- Anpassning av användar gränssnittet
- Information som programmet tar emot som anspråk i en token

Du kan skapa många användar flöden av olika typer av klienter och använda dem i dina program efter behov. Användar flöden kan återanvändas i olika program. Den här flexibiliteten gör det möjligt att definiera och ändra identitets upplevelser med minimala eller inga ändringar i koden. Ditt program utlöser ett användar flöde genom att använda en standard-HTTP-autentiseringsbegäran som innehåller en användar flödes parameter. En anpassad [token](tokens-overview.md) tas emot som ett svar.

I följande exempel visas frågesträngparametern "p" som anger det användar flöde som ska användas:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Användarflödesversioner

I Azure Portal läggs nya [versioner av användar flöden](user-flow-versions.md) till hela tiden. När du kommer igång med Azure AD B2C rekommenderar vi att du använder och testar användar flöden. När du skapar ett nytt användar flöde väljer du det användar flöde som du behöver från den **rekommenderade** fliken.

Följande användar flöden rekommenderas för närvarande:

- **Registrera dig och logga in** – hanterar både registrerings-och inloggnings upplevelser med en enda konfiguration. Användarna utsätts för rätt sökväg beroende på kontexten. Vi rekommenderar att du använder det här användar flödet över ett **registrerings** användar flöde eller ett **inloggnings** användar flöde.
- **Profil redigering** – gör att användare kan redigera profil informationen.
- **Lösen ords återställning** – låter dig konfigurera om och hur användarna kan återställa sina lösen ord.

## <a name="linking-user-flows"></a>Länka användar flöden

En **registrerings-eller inloggnings** användare med lokala konton innehåller ett **bortglömt lösen ord?** länk på den första sidan i upplevelsen. När du klickar på den här länken utlöses inget användar flöde för lösen ords återställning automatiskt.

I stället returneras felkoden `AADB2C90118` till ditt program. Programmet måste hantera denna felkod genom att köra ett speciellt användar flöde som återställer lösen ordet. Om du vill se ett exempel kan du titta på ett [enkelt ASP.net-exempel](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) som visar hur användar flöden länkas.

## <a name="email-address-storage"></a>Lagring av e-postadress

En e-postadress kan krävas som en del av ett användar flöde. Om användaren autentiseras med en social identitetsprovider lagras e-postadressen i egenskapen **otherMails** . Om ett lokalt konto är baserat på ett användar namn lagras e-postadressen i detalj egenskapen för stark autentisering. Om ett lokalt konto är baserat på en e-postadress lagras e-postadressen i egenskapen **signInNames** .

E-postadressen är inte garanterat verifierad i någon av dessa fall. En klient administratör kan inaktivera e-postverifiering i de grundläggande principerna för lokala konton. Även om verifiering av e-postadresser är aktive rad, kontrol leras inte adresser om de kommer från en social identitetsprovider och de inte har ändrats.

Endast egenskaperna **otherMails** och **signInNames** visas via Microsoft Graph-API: et. E-postadressen i detalj egenskapen för stark autentisering är inte tillgänglig.

## <a name="next-steps"></a>Nästa steg

Om du vill skapa de rekommenderade användar flödena följer du anvisningarna i [Självstudier: skapa ett användar flöde](tutorial-create-user-flows.md).
