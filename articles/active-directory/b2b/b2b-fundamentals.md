---
title: Metodtips och rekommendationer för Azure Active Directory B2B
description: Lär dig metodtips och rekommendationer för B2B-gästanvändaråtkomst (Business-to-Business) i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050855"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Metodtips för Azure Active Directory B2B
Den här artikeln innehåller rekommendationer och metodtips för B2B-samarbete (Business-to-Business) i Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **Från och med den 31 mars 2021**stöder Microsoft inte längre inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbetsscenarier. Som förberedelse uppmuntrar vi kunder att välja [e-post engångskodautentisering.](one-time-passcode.md) Vi välkomnar din feedback på den här offentliga förhandsversionen och är glada över att skapa ännu fler sätt att samarbeta.

## <a name="b2b-recommendations"></a>B2B-rekommendationer
| Rekommendation | Kommentarer |
| --- | --- |
| För en optimal inloggningsupplevelse, federera med identitetsleverantörer | När det är möjligt kan du federera direkt med identitetsleverantörer så att inbjudna användare kan logga in på dina delade appar och resurser utan att behöva skapa MSA-konton (Microsoft Accounts) eller Azure AD.When possible, federate directly with identity providers to allow invited users to sign in to your shared apps and resources without having to create Microsoft Accounts (MSAs) or Azure AD accounts. Du kan använda [Googles federationsfunktion](google-federation.md) för att låta B2B-gästanvändare logga in med sina Google-konton. Du kan också använda [funktionen Direkt federation (förhandsversion)](direct-federation.md) för att konfigurera direkt federation med en organisation vars identitetsprovider (IdP) stöder SAML 2.0- eller WS-Fed-protokollet. |
| Använd funktionen Skicka en gång till lösenord (förhandsversion) för B2B-gäster som inte kan autentisera på annat sätt | Funktionen [E-postlösenkod (förhandsversion)](one-time-passcode.md) autentiserar B2B-gästanvändare när de inte kan autentiseras på annat sätt som Azure AD, ett Microsoft-konto (MSA) eller Google-federation. När gästanvändaren löser in en inbjudan eller får åtkomst till en delad resurs kan de begära en tillfällig kod som skickas till deras e-postadress. Sedan anger de den här koden för att fortsätta logga in. |
| Lägg till företagsanpassning på inloggningssidan | Du kan anpassa inloggningssidan så att den blir mer intuitiv för dina B2B-gästanvändare. Se hur du lägger till [företagsprofilering för att logga in och komma åt panelsidor](../fundamentals/customize-branding.md). |
| Lägg till din sekretesspolicy i B2B-gästupplevelsen för inlösen av användare | Du kan lägga till webbadressen till organisationens sekretesspolicy i första gången inlösenprocessen för inbjudan så att en inbjuden användare måste samtycka till att dina sekretessvillkor fortsätter. Se [How-to: Lägg till organisationens sekretessinformation i Azure Active Directory](https://aka.ms/adprivacystatement). |
| Använd funktionen massinbjudning (förhandsversion) för att bjuda in flera B2B-gästanvändare samtidigt | Bjud in flera gästanvändare till din organisation samtidigt med hjälp av förhandsversionen av massinbjudningsversionen i Azure-portalen. Med den här funktionen kan du ladda upp en CSV-fil för att skapa B2B-gästanvändare och skicka inbjudningar i grupp. Se [Självstudiekurs för massinbjudande B2B-användare](tutorial-bulk-invite.md). |
| Framtvinga principer för villkorlig åtkomst för MFA -autentisering med flera faktorer | Vi rekommenderar att du tillämpar MFA-principer för de appar som du vill dela med partner-B2B-användare. På så sätt tillämpas MFA konsekvent på apparna i din klientorganisation oavsett om partnerorganisationen använder MFA. Se [Villkorlig åtkomst för B2B-samarbetsanvändare](conditional-access.md). |
| Om du tillämpar enhetsbaserade principer för villkorlig åtkomst använder du undantagslistor för att ge åtkomst till B2B-användare | Om enhetsbaserade principer för villkorlig åtkomst är aktiverade i din organisation blockeras B2B-gästanvändarenheter eftersom de inte hanteras av din organisation. Du kan skapa undantagslistor som innehåller specifika partneranvändare för att utesluta dem från den enhetsbaserade principen för villkorlig åtkomst. Se [Villkorlig åtkomst för B2B-samarbetsanvändare](conditional-access.md). |
| Använd en klientspecifik URL när du tillhandahåller direkta länkar till dina B2B-gästanvändare | Som ett alternativ till e-postmeddelandet med inbjudan kan du ge en gäst en direktlänk till din app eller portal. Den här direktlänken måste vara klientspecifik, vilket innebär att den måste innehålla ett klient-ID eller en verifierad domän så att gästen kan autentiseras i din klient, där den delade appen finns. Se [Inlösenupplevelse för gästanvändaren](redemption-experience.md). |
| När du utvecklar en app använder du UserType för att ta reda på gästanvändarupplevelsen  | Om du utvecklar ett program och vill tillhandahålla olika upplevelser för klientanvändare och gästanvändare använder du egenskapen UserType. UserType-anspråket ingår för närvarande inte i token. Program bör använda Microsoft Graph API för att fråga katalogen för användaren att få sin UserType. |
| Ändra egenskapen UserType *endast* om användarens relation till organisationen ändras | Även om det är möjligt att använda PowerShell för att konvertera egenskapen UserType för en användare från medlem till gäst (och vice versa), bör du bara ändra den här egenskapen om användarens relation till organisationen ändras. Se [Egenskaper för en B2B-gästanvändare](user-properties.md).|

## <a name="next-steps"></a>Nästa steg

[Hantera B2B-delning](delegate-invitations.md)
