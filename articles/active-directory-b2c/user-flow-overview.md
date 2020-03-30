---
title: Användarflöden i Azure Active Directory B2C | Microsoft-dokument
description: Läs mer om det utökningsbara principramverket i Azure Active Directory B2C och hur du skapar olika användarflöden.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185628"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Användarflöden i Azure Active Directory B2C

Det utökningsbara principramverket i Azure Active Directory B2C (Azure AD B2C) är tjänstens kärnstyrka. Principer beskriver helt identitetsupplevelser som registrering, inloggning eller profilredigering. Azure AD B2C-portalen innehåller fördefinierade, konfigurerbara principer som kallas **användarflöden**för att hjälpa dig att konfigurera de vanligaste identitetsuppgifterna.

## <a name="what-are-user-flows"></a>Vad är användarflöden?

Med ett användarflöde kan du styra beteenden i dina program genom att konfigurera följande inställningar:

- Kontotyper som används för inloggning, till exempel sociala konton som ett Facebook- eller lokala konton
- Attribut som ska samlas in från konsumenten, till exempel förnamn, postnummer och skostorlek
- Azure Multi-Factor Authentication
- Anpassning av användargränssnittet
- Information som programmet tar emot som anspråk i en token

Du kan skapa många användarflöden av olika typer i din klientorganisation och använda dem i dina program efter behov. Användarflöden kan återanvändas över program. Med den här flexibiliteten kan du definiera och ändra identitetsupplevelser med minimala eller inga ändringar i koden. Programmet utlöser ett användarflöde med hjälp av en standardbegäran för HTTP-autentisering som innehåller en parameter för användarflöde. En anpassad [token](tokens-overview.md) tas emot som ett svar.

I följande exempel visas frågesträngparametern "p" som anger det användarflöde som ska användas:

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

I Azure-portalen läggs nya [versioner av användarflöden](user-flow-versions.md) till hela tiden. När du kommer igång med Azure AD B2C rekommenderas testade användarflöden för dig att använda. När du skapar ett nytt användarflöde väljer du det användarflöde som du behöver på fliken **Rekommenderad.**

Följande användarflöden rekommenderas för närvarande:

- **Registrera dig och logga in** - Hanterar både registrerings- och inloggningsupplevelser med en enda konfiguration. Användare leds på rätt väg beroende på sammanhanget. Vi rekommenderar att du använder det **sign-up** här användarflödet via ett registreringsanvändarflöde eller ett inloggningsanvändarflöde. **sign-in**
- **Profilredigering** - Gör det möjligt för användare att redigera sin profilinformation.
- **Återställning av lösenord** - Gör att du kan konfigurera om och hur användare kan återställa sitt lösenord.

## <a name="linking-user-flows"></a>Länka användarflöden

Ett **användarflöde** för registrering eller inloggning med lokala konton innehåller länken **Glömt lösenord?** Om du klickar på den här länken utlöses inte ett användarflöde för återställning av lösenord automatiskt.

I stället returneras felkoden `AADB2C90118` till ditt program. Ditt program måste hantera den här felkoden genom att köra ett visst användarflöde som återställer lösenordet. Om du vill se ett exempel kan du ta en titt på ett [enkelt ASP.NET exempel](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) som visar länkning av användarflöden.

## <a name="email-address-storage"></a>Lagring av e-postadress

En e-postadress kan krävas som en del av ett användarflöde. Om användaren autentiserar med en leverantör av social identitet lagras e-postadressen i egenskapen **otherMails.** Om ett lokalt konto baseras på ett användarnamn lagras e-postadressen i egenskapen stark autentiseringsinformation. Om ett lokalt konto baseras på en e-postadress lagras e-postadressen i egenskapen **signInNames.**

E-postadressen är inte garanterad att verifieras i något av dessa fall. En klientadministratör kan inaktivera e-postverifiering i de grundläggande principerna för lokala konton. Även om verifiering av e-postadress är aktiverad verifieras inte adresser om de kommer från en leverantör av social identitet och de inte har ändrats.

Endast egenskaperna **för andra e-post och** **signInNames exponeras** via Microsoft Graph API. E-postadressen i egenskapen stark autentiseringsinformation är inte tillgänglig.

## <a name="next-steps"></a>Nästa steg

Om du vill skapa de rekommenderade användarflödena följer du instruktionerna i [Självstudiekurs: Skapa ett användarflöde](tutorial-create-user-flows.md).
