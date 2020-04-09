---
title: Microsoft identitetsplattform & SAML bärare påstående flöde | Azure
description: Lär dig hur du hämtar data från Microsoft Graph utan att användaren uppmanas att ange autentiseringsuppgifter med hjälp av KONTROLLFLÖDET FÖR SAML-bärare.
services: active-directory
author: umeshbarapatre
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1cd79b1f9e4cd3afadee250da0c184c0c5b8ac07
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886185"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft identitetsplattform och OAuth 2.0 SAML bärare påstående flöde
OAuth 2.0 SAML-bärare kontrollflödet kan du begära en OAuth-åtkomsttoken med hjälp av ett SAML-påstående när en klient behöver använda en befintlig förtroenderelation. Signaturen som tillämpas på SAML-verifieringen ger autentisering av den auktoriserade appen. Ett SAML-påstående är en XML-säkerhetstoken som utfärdats av en identitetsprovider och förbrukas av en tjänsteleverantör. Tjänsteleverantören förlitar sig på sitt innehåll för att identifiera påståendets ämne av säkerhetsrelaterade skäl.

SAML- påståendet bokförs på OAuth-tokenslutpunkten.  Slutpunkten bearbetar y påståendet och utfärdar en åtkomsttoken baserat på förhandsgodkännande av appen. Klienten behöver inte ha eller lagra en uppdateringstoken, inte heller krävs klienthemligheten för att skickas till tokenslutpunkten.

SAML-bärare Kontrollflöde är användbart när du hämtar data från Microsoft Graph API:er (som endast stöder delegerade behörigheter) utan att användaren uppmanas att ange autentiseringsuppgifter. I det här fallet fungerar inte klientautentiseringsbe beviljandet, som är att föredra för bakgrundsprocesser.

För program som gör interaktiva webbläsarbaserade inloggningar för att få ett SAML-påstående och sedan vill lägga till åtkomst till ett OAuth-skyddat API (till exempel Microsoft Graph), kan du göra en OAuth-begäran för att få en åtkomsttoken för API:et. När webbläsaren omdirigeras till Azure AD för att autentisera användaren hämtar webbläsaren sessionen från SAML-inloggningen och användaren behöver inte ange sina autentiseringsuppgifter.

OAuth SAML Bearer Assertion-flödet stöds också för användare som autentiserar med identitetsleverantörer som Active Directory Federation Services (ADFS) som är federerade i Azure Active Directory.  SAML-påståendet som erhållits från ADFS kan användas i ett OAuth-flöde för att autentisera användaren.

![OAuth-flöde](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Anropa graf med HJÄLP AV SAML-bärare påstående
Låt oss nu förstå hur vi faktiskt kan hämta SAML påstående programatically. Den här metoden testas med ADFS. Detta fungerar dock med alla identitetsleverantörer som stöder återlämnande av SAML påstående programatically. Den grundläggande processen är: få ett SAML-påstående, få en åtkomsttoken och få tillgång till Microsoft Graph.

### <a name="prerequisites"></a>Krav

Upprätta en förtroenderelation mellan auktoriseringsservern/auktoriseringsservern (Microsoft 365) och identitetsprovidern eller utfärdaren av SAML 2.0-innehavarens påstående (ADFS). Om du vill konfigurera ADFS för enkel inloggning och som identitetsprovider kan du läsa [den här artikeln](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Registrera programmet i [portalen:](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
1. Logga in på [portalens appregistreringsblad](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Observera att vi använder v2.0-slutpunkterna för Graph API och därför måste registrera programmet i den här portalen. Annars kunde vi ha använt registreringarna i Azure active directory). 
1. Välj **Ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation: 
    1. **Namn** – ange ett beskrivande programnamn som ska visas för appens användare.
    1. **Kontotyper som stöds** – välj vilka konton som du vill att programmet ska stödja.
    1. **Omdirigera URI (valfritt)** – Välj vilken typ av app du skapar, webb eller offentlig klient (mobil & skrivbordet) och ange sedan omdirigerings-URI-adressen (eller svars-URL:en) för ditt program.
    1. När det är klart väljer du **Registrera**.
1. Anteckna program-ID:t för programmet (klienten).
1. Välj Certifikat & **hemligheter i**den vänstra rutan . Klicka på **Ny klienthemlighet** i avsnittet **Klienthemligheter.** Kopiera den nya klienthemligheten, du kommer inte att kunna hämta när du lämnar bladet.
1. I den vänstra rutan väljer du **API-behörigheter** och **sedan lägg till en behörighet**. Välj **Microsoft Graph**, **delegerade behörigheter**och välj sedan **Tasks.read** eftersom vi avser att använda Outlook Graph API. 

Installera [Postman](https://www.getpostman.com/), ett verktyg som krävs för att testa exempelbegäranden.  Senare kan du konvertera begäranden till kod.

### <a name="get-the-saml-assertion-from-adfs"></a>Hämta SAML-påståendet från ADFS
Skapa en POST-begäran till ADFS-slutpunkten med SOAP-kuvertet för att hämta SAML- påståendet:

![Få SAML påstående](./media/v2-saml-bearer-assertion/2.png)

Huvudvärden:

![Huvudvärden](./media/v2-saml-bearer-assertion/3.png)

ADFS begäran organ:

![ADFS begäran organ](./media/v2-saml-bearer-assertion/4.png)

När den här begäran har publicerats bör du få ett SAML-påstående från ADFS. Endast **SAML:Assertion-taggdata** krävs, konvertera den till base64-kodning som ska användas i ytterligare begäranden.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Hämta OAuth2-token med SAML-kontrollen 
I det här steget hämtar du en OAuth2-token med ADFS-kontrollsvaret.

1. Skapa en POST-begäran enligt nedan med rubrikvärdena:

    ![POST-begäran](./media/v2-saml-bearer-assertion/5.png)
1. I brödtexten i begäran ersätter **du client_id**, **client_secret**och **påstående** (det base64-kodade SAML-påståendet erhöll föregående steg):

    ![Begärandetext](./media/v2-saml-bearer-assertion/6.png)
1. Vid lyckad begäran får du en åtkomsttoken från Azure active directory.

### <a name="get-the-data-with-the-oauth-token"></a>Hämta data med Oauth-token

När du har fått åtkomsttoken anropar du Graph API:er (Outlook-uppgifter i det här exemplet). 

1. Skapa en GET-begäran med åtkomsttoken som hämtats i föregående steg:

    ![HÄMTA begäran](./media/v2-saml-bearer-assertion/7.png)

1. På en lyckad begäran får du ett JSON-svar.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om de olika [autentiseringsflödena och programscenarierna](authentication-flows-app-scenarios.md).