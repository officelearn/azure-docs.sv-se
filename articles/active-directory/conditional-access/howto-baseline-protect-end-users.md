---
title: Slut användar skydd för bas linje princip (för hands version) – Azure Active Directory
description: Princip för villkorlig åtkomst för att kräva Multi-Factor Authentication för användare
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: afcd9c9d3191caeabe182f499b5fd80cd8e1d8dd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608151"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Bas linje princip: Slut användar skydd (för hands version)

Vi rekommenderar att du tror att administratörs konton är de enda konton som behöver skydd med Multi-Factor Authentication (MFA). Administratörer har bred åtkomst till känslig information och kan göra ändringar i inställningarna för hela prenumerationen. Dåliga aktörer brukar dock vara mål för slutanvändare. När du har fått åtkomst kan de här Felaktiga aktörerna begära åtkomst till privilegie rad information å den ursprungliga konto innehavarens vägnar eller ladda ned hela katalogen för att utföra en nätfiske-attack i hela organisationen. En gemensam metod för att förbättra skyddet för alla användare är att kräva en starkare form av konto verifiering, till exempel Multi Factor Authentication (MFA).

För att uppnå en rimlig balans mellan säkerhet och användbarhet behöver användarna inte tillfrågas varje gång de loggar in. Autentiseringsbegäranden som reflekterar normala användar beteenden, t. ex. inloggning från samma enhet från samma plats, har en låg risk för angrepp. Endast inloggningar som bedöms som riskfyllda och visar egenskaper hos en felaktig aktör bör tillfrågas om MFA-utmaningar.

End User Protection är en riskhanterings [bas linje princip](concept-baseline-protection.md) som skyddar alla användare i en katalog, inklusive alla administratörs roller. Att aktivera den här principen kräver att alla användare registreras för MFA med hjälp av Authenticator-appen. Användare kan ignorera MFA-registrerings frågan i 14 dagar, sedan de kommer att blockeras från att logga in tills de registreras för MFA. När de har registrerats för MFA tillfrågas användarna om MFA endast under riskfyllda inloggnings försök. Komprometterade användar konton blockeras tills lösen ordet har återställts och risk händelser har stängts.

> [!NOTE]
> Den här principen gäller alla användare, inklusive gäst konton och kommer att utvärderas när de loggar in på alla program.

## <a name="recovering-compromised-accounts"></a>Återställ komprometterade konton

För att hjälpa till att skydda våra kunder hittar Microsofts läckta autentiseringsuppgifter-tjänsten offentligt tillgängliga användar namn/lösen ord. Om de matchar någon av våra användare bidrar vi till att skydda kontot omedelbart. Användare som identifieras som en läckt autentiseringsuppgifter bekräftas komprometteras. Användarna kommer att blockeras från att logga in tills lösen ordet återställs.

Användare som har tilldelats en Azure AD Premium-licens kan återställa åtkomst via självbetjäning för återställning av lösen ord (SSPR) om funktionen är aktive rad i sin katalog. Användare utan en Premium-licens som blockeras måste kontakta en administratör för att utföra en manuell lösen ords återställning och stänga av den flaggade användar risk händelsen.

### <a name="steps-to-unblock-a-user"></a>Steg för att avblockera en användare

Bekräfta att användaren har blockerats av principen genom att granska användarens inloggnings loggar.

1. En administratör måste logga in på **Azure Portal** och navigera till **Azure Active Directory** > **användare** > klickar på användarens namn och navigerar till inloggningar.
1. För att initiera lösen ords återställning på en blockerad användare måste administratören navigera till **Azure Active Directory** > användare som har**flaggats för risk**
1. Klicka på den användare vars konto är blockerat för att visa information om användarens senaste inloggnings aktivitet.
1. Klicka på Återställ lösen ord för att tilldela ett tillfälligt lösen ord som måste ändras vid nästa inloggning.
1. Klicka på Stäng alla händelser för att återställa användarens risk poäng.

Användaren kan nu logga in, återställa sitt lösen ord och få åtkomst till programmet.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Eftersom principen **för** slutanvändare gäller för alla användare i din katalog måste flera saker göras för att säkerställa en smidig distribution. Dessa överväganden omfattar att identifiera användare och tjänst principer i Azure AD som inte kan eller inte bör utföra MFA, samt program och klienter som används av din organisation som inte stöder modern autentisering.

### <a name="legacy-protocols"></a>Äldre protokoll

Bakåtkompatibla autentiseringsprotokoll (IMAP, SMTP, POP3 osv.) används av e-postklienter för att göra autentiseringsbegäranden. Dessa protokoll stöder inte MFA.  De flesta konto kompromisser som visas av Microsoft orsakas av felaktiga aktörer som utför attacker mot äldre protokoll som försöker kringgå MFA. För att säkerställa att MFA krävs vid inloggning till ett konto och felaktiga aktörer inte kan kringgå MFA, blockerar den här principen alla autentiseringsbegäranden som görs till administratörs konton från äldre protokoll.

> [!WARNING]
> Innan du aktiverar den här principen måste du se till att användarna inte använder äldre autentiseringsprotokoll. Se artikeln [How to: Blockera äldre autentisering till Azure AD med villkorlig åtkomst](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) för mer information.

## <a name="enable-the-baseline-policy"></a>Aktivera bas linje principen

Princip **bas linje princip: End User Protection (för hands** version) är förkonfigurerad och visas överst när du navigerar till bladet för villkorlig åtkomst i Azure Portal.

Så här aktiverar du den här principen och skyddar dina användare:

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. I listan med principer väljer **du bas linje princip: Slut användar skydd (för hands**version).
1. Ange principen för att **aktivera principen** **direkt**.
1. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Principer för bas linje skydd för villkorlig åtkomst](concept-baseline-protection.md)
* [Fem steg för att skydda din identitets infrastruktur](../../security/fundamentals/steps-secure-identity.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)
