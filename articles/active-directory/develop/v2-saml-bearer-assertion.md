---
title: Microsoft Identity Platform & SAML Bearer-försäkrat flöde | Azure
description: Lär dig hur du hämtar data från Microsoft Graph utan att fråga användaren om autentiseringsuppgifter med hjälp av SAML Bearer Assertion-flödet.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 47b036f558628d51242a78c00d2ee17332816d25
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348767"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft Identity Platform och OAuth 2,0 SAML Bearer-försäkrat flöde
Med det försäkrade flödet av OAuth 2,0 SAML-Bearer kan du begära en OAuth-åtkomsttoken med en SAML-kontroll när en klient behöver använda en befintlig förtroende relation. Signaturen som används för SAML Assertion ger autentisering av den auktoriserade appen. En SAML-kontroll är en XML-säkerhetstoken som utfärdas av en identitetsprovider och som används av en tjänst leverantör. Tjänste leverantören förlitar sig på sitt innehåll för att identifiera kontrollens ämne för säkerhetsrelaterade orsaker.

SAML-kontrollen skickas till OAuth-token-slutpunkten.  Slut punkten bearbetar kontrollen och utfärdar en åtkomsttoken baserat på ett tidigare godkännande av appen. Klienten måste inte ha eller lagra en uppdateringstoken, eller så är den klient hemlighet som krävs för att skickas till token-slutpunkten.

SAML Bearer-kontroll flöde är användbart när du hämtar data från Microsoft Graph-API: er (som endast stöder delegerade behörigheter) utan att fråga användaren om autentiseringsuppgifter. I det här scenariot fungerar inte tilldelningen av klientautentiseringsuppgifter, som är avsedd för bakgrunds processer.

För program som har interaktiv webbläsarbaserad inloggning för att få en SAML-kontroll och sedan vill lägga till åtkomst till ett OAuth-skyddat API (till exempel Microsoft Graph) kan du göra en OAuth-begäran för att få en åtkomsttoken för API: et. När webbläsaren omdirigeras till Azure AD för att autentisera användaren, kommer webbläsaren att hämta sessionen från SAML-inloggningen och användaren behöver inte ange sina autentiseringsuppgifter.

Det försäkrade OAuth SAML-flödet stöds bara för användare som autentiseras med identitets leverantörer som Active Directory Federation Services (AD FS) (ADFS) federerad till Azure Active Directory.  SAML-kontrollen som hämtades från ADFS kan användas i ett OAuth-flöde för att autentisera användaren.

![OAuth-flöde](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Anropa graf med SAML Bearer-kontroll
Nu ska vi lära oss hur vi faktiskt kan hämta SAML-kontroll program mässigt. Den här metoden har testats med ADFS. Detta fungerar dock med alla identitets leverantörer som har stöd för att returnera SAML-kontroll program mässigt. Den grundläggande processen är: Hämta en SAML-kontroll, hämta en åtkomsttoken och få åtkomst Microsoft Graph.

### <a name="prerequisites"></a>Förutsättningar

Upprätta en förtroende relation mellan auktoriseringsservern/miljön (Microsoft 365) och identitets leverantören, eller utfärdaren av SAML 2,0 Bearer Assertion (ADFS). Om du vill konfigurera ADFS för enkel inloggning och som identitets leverantör kan du se [den här artikeln](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365).

Registrera programmet i [portalen](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Logga in på [bladet program registrering i portalen](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Observera att vi använder v 2.0-slutpunkter för Graph API och måste därför registrera programmet i den här portalen. Annars kunde vi ha använt registreringarna i Azure Active Directory). 
1. Välj **ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation: 
    1. **Namn** – ange ett beskrivande programnamn som ska visas för appens användare.
    1. **Kontotyper som stöds** – välj vilka konton som du vill att programmet ska stödja.
    1. **Omdirigerings-URI (valfritt)** – Välj den typ av app som du skapar, webb eller offentlig klient (mobil & Desktop) och ange omdirigerings-URI (eller svars-URL) för ditt program.
    1. När det är klart väljer du **Registrera**.
1. Anteckna program-ID: t (klient).
1. I den vänstra rutan väljer du **certifikat & hemligheter**. Klicka på **ny klient hemlighet** i avsnittet **klient hemligheter** . Kopiera den nya klient hemligheten. du kommer inte att kunna hämta när du lämnar bladet.
1. I den vänstra rutan väljer du **API-behörigheter** och **lägger sedan till en behörighet**. Välj **Microsoft Graph**, **delegerade behörigheter** och välj sedan **aktiviteter. Read** eftersom vi planerar att använda Outlook-Graph API. 

Installera [Postman](https://www.getpostman.com/), ett verktyg som krävs för att testa exempel förfrågningarna.  Du kan senare konvertera begär anden till kod.

### <a name="get-the-saml-assertion-from-adfs"></a>Hämta SAML-kontroll från ADFS
Skapa en POST-begäran till ADFS-slutpunkten med SOAP-kuvert för att hämta SAML-försäkran:

![Hämta SAML-kontroll](./media/v2-saml-bearer-assertion/2.png)

Rubrik värden:

![Huvud värden](./media/v2-saml-bearer-assertion/3.png)

AD FS-begär ande text:

![AD FS-begärantext](./media/v2-saml-bearer-assertion/4.png)

När den här begäran har publicerats bör du få en SAML-kontroll från ADFS. Endast **SAML: Assertion** -taggattribut krävs, konvertera den till base64-kodning som ska användas i ytterligare begär Anden.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Hämta OAuth2-token med SAML Assertion 
I det här steget hämtar du en OAuth2-token med hjälp av AD-kontrollerad Response.

1. Skapa en POST-begäran som visas nedan med huvud värden:

    ![POST-begäran](./media/v2-saml-bearer-assertion/5.png)
1. I bröd texten i begäran ersätter du **client_id**, **client_secret** och **intyg** (Base64-kodad SAML-kontroll hämtades föregående steg):

    ![Begärandetext](./media/v2-saml-bearer-assertion/6.png)
1. Vid lyckad begäran får du en åtkomsttoken från Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>Hämta data med OAuth-token

Anropa Graph API: erna (Outlook-uppgifter i det här exemplet) när du har tagit emot åtkomsttoken. 

1. Skapa en GET-begäran med den åtkomsttoken som hämtades i föregående steg:

    ![GET-begäranden](./media/v2-saml-bearer-assertion/7.png)

1. Vid lyckad begäran får du ett JSON-svar.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om olika [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md).
