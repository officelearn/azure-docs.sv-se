---
title: Användarflöden i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om utökningsbart principramverk för Azure Active Directory B2C och hur du skapar olika användarflöden.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8da3f5b86ccf1d6038ab410fe7fc6e6840fd0404
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845511"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Användarflöden i Azure Active Directory B2C

Utökningsbart principramverk för Azure Active Directory (Azure AD) B2C är core styrkan hos tjänsten. Principer helt beskriver identitetsupplevelser som registrering, inloggning och profilredigering. Som hjälper dig att konfigurera de vanligaste uppgifterna för identitet, Azure AD B2C-portalen innehåller fördefinierade, konfigureras principer som kallas **användarflöden**. 

## <a name="what-are-user-flows"></a>Vad är användarflöden?

Ett användarflöde kan du styra beteenden i dina program genom att konfigurera följande inställningar:

- Kontot som typer används för inloggning, till exempel konton i sociala medier som ett Facebook eller lokala konton
- Attribut som ska samlas in från konsumenten, till exempel förnamn, postnummer och redskap storlek
- Azure Multi-Factor Authentication
- Anpassning av användargränssnittet
- Information som programmet tar emot som anspråk i en token 

Du kan skapa flera användarflöden av olika typer i din klient och använda dem i dina program, vid behov. Användarflöden kan återanvändas i program. Den här flexibiliteten gör att du kan definiera och ändra identitetsupplevelser med minimala kodändringar eller inga ändringar i koden. Programmets utlöser ett användarflöde med hjälp av en standard HTTP-autentisering-begäran som innehåller en parameter för flödet av användaren. En anpassad [token](active-directory-b2c-reference-tokens.md) tas emot som ett svar. 

I följande exempel visas ”p” för frågesträngparametern som anger användarflödet som ska användas:

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

I Azure portal, ny [versioner av användarflöden](user-flow-versions.md) läggs hela tiden. När du komma igång med Azure AD B2C, testas användaren flöden rekommenderas som du kan använda. När du skapar ett nytt användarflöde, väljer du det användarflöde som du behöver från de **rekommenderas** fliken.

För närvarande rekommenderas följande användarflöden:

- **Registrera dig och logga in** -hanterar båda upplevelser för registrering och inloggning med en enda konfiguration. Användare leds av rätt väg beroende på kontext. Vi rekommenderar att du använder det här användarflödet över en **registrering** användarflödet eller en **inloggning** användarflödet.
- **Profilredigering** – gör det möjligt för användare att redigera deras profilinformation.
- **Återställning av lösenord** – kan du konfigurera om och hur användare kan återställa sina lösenord.

## <a name="linking-user-flows"></a>Länkramverk användarflöden

En **registrering eller inloggning** användarflödet med lokala konton omfattar en **har du glömt lösenordet?** länk på första sidan i miljön. Klicka på den här länken återställs inte utlösa ett lösenord automatiskt användarflödet. 

I stället felkoden `AADB2C90118` returneras till programmet. Programmet behöver för att hantera felet genom att köra en specifik användare-flöde som återställer lösenordet. Om du vill se ett exempel kan ta en titt på en [enkel ASP.NET-exemplet](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) som visar länkning av användarflöden.

## <a name="email-address-storage"></a>E-postadress lagring

En e-postadress kan krävas som en del av ett användarflöde. Om användaren autentiseras med en social identitetsprovider, e-postadressen lagras i den **otherMails** egenskapen. Om ett lokalt konto är baserat på ett användarnamn, lagras e-postadressen i en egenskap med stark autentisering-information. Om ett lokalt konto är baserat på en e-postadress och e-postadressen lagras i den **signInNames** egenskapen.
 
E-postadressen är inte säkert att verifieras i båda fallen. En Innehavaradministratör kan inaktivera e-Postverifiering i de grundläggande principerna för lokala konton. Även om e-postadress verifiering är aktiverad, adresser inte verifiera om de kommer från en social identitetsprovider och de inte har ändrats.
 
Endast den **otherMails** och **signInNames** egenskaper exponeras via Active Directory Graph API. E-postadress i egenskapen stark autentisering-information är inte tillgänglig.

## <a name="next-steps"></a>Nästa steg

Om du vill skapa de rekommenderade användarflöden, följer du anvisningarna i [självstudien: Skapa ett användarflöde](tutorial-create-tenant.md).


