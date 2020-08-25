---
title: Självstudie för att konfigurera Azure Active Directory B2C med whoIam
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C-autentisering med whoIam för användar verifiering
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817824"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera WhoIAM med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du konfigurerar [WhoIAM](https://www.whoiam.ai/brims/) -BRIMS (märkes hanterings system) i din miljö och integrerar det med Azure AD B2C.

WhoIAM-BRIMS är en uppsättning appar och tjänster som distribueras i din miljö. Det ger dig röst-, SMS-och e-postverifiering av användar basen. BRIMS fungerar tillsammans med din befintliga identitets-och åtkomst hanterings lösning och är plattforms oberoende.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- [En Azure AD B2C klient](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) som är länkad till din Azure-prenumeration.

- Ett WhoIAM [utvärderings konto](https://www.whoiam.ai/contact-us/)

## <a name="scenario-description"></a>Scenariobeskrivning

WhoIAM-integreringen innehåller följande komponenter:

- Azure AD B2C klient – auktoriseringsservern som ansvarar för att verifiera användarens autentiseringsuppgifter baserat på anpassade principer som definierats i klient organisationen. Det kallas även för identitets leverantören.

- En administrations Portal för att hantera klienter och deras konfigurationer

- En API-tjänst som visar olika funktioner via slut punkter  

- Azure Cosmos DB som används som Server del för både BRIMS administrations Portal och API-tjänsten

I följande arkitektur diagram visas implementeringen.

![skärm bild för whoiam-Architecture-diagram](media/partner-whoiam/whoiam-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | Användaren kommer till inloggnings sidan. Användaren startar en registrerings-eller inloggnings förfrågan till en app som använder Azure AD B2C som identitets leverantör.
| 2. | Som en del av autentiseringen begär användaren att antingen Verifiera ägarskapet till sin e-post eller telefon, eller använda sin röst som bio metrisk verifierings faktor.  
| 3. | Azure AD B2C gör ett anrop till BRIMS-API-tjänsten genom att skicka in användarens e-postadress, telefonnummer och röst inspelning
| 4. | BRIMS använder fördefinierade konfigurationer, till exempel fullständigt anpassningsbar e-post och SMS-mallar för att interagera med användaren på respektive språk som är konsekvent med appens utseende och känsla.
| 5. | När en användares identitets verifiering är klar returnerar BRIMS en token för att Azure AD B2C som indikerar resultatet av verifieringen. Azure AD B2C beviljar sedan användaren åtkomst till appen eller Miss lyckas med ett autentiseringsförsök.  

## <a name="onboard-with-whoiam"></a>Publicera med WhoIAM

1. Kontakta [WhoIAM](https://www.whoiam.ai/contact-us/) och skapa ett BRIMS-konto.

2. När ett konto har skapats använder du rikt linjerna för onboarding som du har gjort tillgängliga och konfigurerar följande Azure-tjänster:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) – används för säker lagring av lösen ord, t. ex. lösen ord för e-posttjänst.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/) – används som värd för BRIMS API och administrations Portal tjänster

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) – används för att autentisera administrativa användare för administratörs portalen

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) – används för att lagra och hämta inställningar

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (valfritt) – används för att logga in på både API-och admin-portalen

3. Distribuera BRIMS-API: et och BRIMS administrations portal i din Azure-miljö.

4. Azure AD B2C anpassade princip exempel finns i din BRIMS onboarding-dokumentation. Följ dokumentet för att konfigurera din app och Använd BRIMS-plattformen för verifiering av användar identitet.  

Mer information om WhoIAM-BRIMS finns i [produkt dokumentation](https://www.whoiam.ai/brims/)

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C-klienten och under principer väljer du **identitets miljö ramverk**.

2. Välj din tidigare skapade **SignUpSignIn**.

3. Välj **Kör användar flöde** och välj inställningarna:

   a. **Program**: Välj den registrerade appen (EXEMPLET är JWT)

   b. **Svars-URL**: Välj **omdirigerings-URL**

   c. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto

5. BRIMS-tjänsten anropas under flödet efter att användarattribut har skapats. Om flödet är ofullständigt, kontrollerar du att användaren inte har sparats i katalogen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Kom igång med anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
