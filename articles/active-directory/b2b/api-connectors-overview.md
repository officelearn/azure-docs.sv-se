---
title: Om API-kopplingar i självbetjänings registrerings flöden – Azure AD
description: 'Använd Azure Active Directory (Azure AD) API-kopplingar för att anpassa och utöka dina självbetjänings registrerings användar flöden med webb-API: er.'
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c5e546c6eac77c4952a0d32d360f49d4251d49d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905185"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>Använd API-kopplingar för att anpassa och utöka självbetjänings registrering 

## <a name="overview"></a>Översikt 
Som utvecklare eller IT-administratör kan du använda API-kopplingar för att integrera dina självbetjänings [registrerings användar flöden](self-service-sign-up-overview.md) med externa system genom att använda webb-API: er. Du kan till exempel använda API-kopplingar för att:

- [**Integrera med ett anpassat godkännande arbets flöde**](self-service-sign-up-add-approvals.md). Anslut till ett anpassat godkännande system för att skapa och hantera konton.
- [**Utföra identitets verifiering**](code-samples-self-service-sign-up.md#identity-verification). Använd en identitets verifierings tjänst för att lägga till ytterligare en säkerhets nivå för besluts fattande.
- **Verifiera indata från användaren**. Verifiera mot felaktiga eller ogiltiga användar data. Du kan till exempel validera data som tillhandahålls av användaren mot befintliga data i ett externt data lager eller en lista över tillåtna värden. Om det är ogiltigt kan du be en användare att tillhandahålla giltiga data eller hindra användaren från att fortsätta registrerings flödet.
- **Skriv över användarattribut**. Formatera om eller tilldela ett värde till ett attribut som samlas in från användaren. Om en användare till exempel anger det första namnet i gemener eller versaler, kan du formatera namnet med enbart den första bokstaven versaler. 
<!-- - **Enrich user data**. Integrate with your external cloud systems that store user information to integrate them with the sign-up flow. For example, your API can receive the user's email address, query a CRM system, and return the user's loyalty number. Returned claims can be used to pre-fill form fields or return additional data in the application token.  -->
- **Kör anpassad affärs logik**. Du kan utlösa underordnade händelser i moln systemen för att skicka push-meddelanden, uppdatera företags databaser, hantera behörigheter, granska databaser och utföra andra anpassade åtgärder.

En API-anslutning representerar ett kontrakt mellan Azure AD och en API-slutpunkt genom att definiera HTTP-slutpunkten, autentiseringen, begäran och förväntat svar. När du har konfigurerat en API-anslutning kan du aktivera den för ett särskilt steg i ett användar flöde. När en användare når det steget i registrerings flödet anropas API-kopplingen och materialiseras som en HTTP POST-begäran, vilket skickar valda anspråk som nyckel/värde-par i en JSON-text. API-svaret kan påverka körningen av användar flödet. API-svaret kan till exempel blockera en användare från att registrera sig, be användaren att ange information igen eller skriva över och lägga till användarattribut.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Där du kan aktivera en API-koppling i ett användar flöde

Det finns två platser i ett användar flöde där du kan aktivera en API-koppling:

- När du har loggat in med en identitets leverantör
- Innan du skapar användaren

I båda fallen anropas API-kopplingarna under registreringen, inte för inloggning.

### <a name="after-signing-in-with-an-identity-provider"></a>När du har loggat in med en identitets leverantör

En API-anslutning i det här steget i registrerings processen anropas omedelbart när användaren autentiseras med en identitetsprovider (Google, Facebook, Azure AD). Det här steget föregår ***sidan för attribut samling***, som är det formulär som visas för användaren att samla in användarattribut. Följande är exempel på scenarier med API-anslutningar som du kan aktivera i det här steget:

- Använd e-postadressen eller den federerade identiteten som användaren angav för att söka efter anspråk i ett befintligt system. Returnera dessa anspråk från det befintliga systemet, Fyll i sidan samling för attribut och gör dem tillgängliga för att returnera i token.
- Verifiera om användaren ingår i en lista över tillåtna eller nekade och kontrol lera om de kan fortsätta med registrerings flödet.

### <a name="before-creating-the-user"></a>Innan du skapar användaren

En API-anslutning i det här steget i registrerings processen anropas efter sidan Attribute Collection, om en sådan ingår. Det här steget anropas alltid innan ett användar konto skapas i Azure AD. Följande är exempel på scenarier som du kan aktivera vid följande tidpunkt under registreringen:

- Verifiera indata från användaren och be användaren att skicka in data igen.
- Blockera en användar registrering baserat på data som anges av användaren.
- Utföra identitets verifiering.
- Fråga externa system efter befintliga data om användaren att returnera dem i Application-token eller lagra dem i Azure AD.

<!-- > [!IMPORTANT]
> If an invalid response is returned or another error occurs (for example, a network error), the user will be redirected to the app with the error re -->

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [lägger till en API-anslutning till ett användar flöde](self-service-sign-up-add-api-connector.md)
- Lär dig hur du [lägger till ett anpassat godkännande system till självbetjänings registrering](self-service-sign-up-add-approvals.md)