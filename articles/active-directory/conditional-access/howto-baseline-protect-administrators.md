---
title: Bas linje princip Kräv MFA för administratörer – Azure Active Directory
description: Princip för villkorlig åtkomst för att kräva Multi-Factor Authentication för administratörer
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
ms.openlocfilehash: b00b061b1763d4b4e7236d8dc9ac1eedf7f923bc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533012"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Bas linje princip: Kräv MFA för administratörer (för hands version)

Användare med åtkomst till privilegierade konton har obegränsad åtkomst till din miljö. På grund av den kraft de här kontona har måste du behandla dem med särskild omsorg. En gemensam metod för att förbättra skyddet av privilegierade konton är att kräva en starkare form av konto verifiering när de används för inloggning. I Azure Active Directory kan du få en bättre konto verifiering genom att kräva Multi-Factor Authentication (MFA).

**KRÄV MFA för administratörer (för hands version)** är en [bas linje princip](concept-baseline-protection.md) som kräver MFA varje gång en av följande privilegierade administratörs roller loggar in:

* Global administratör
* SharePoint-administratör
* Exchange-administratör
* Administratör av villkorsstyrd åtkomst
* Säkerhetsadministratör
* Administratör för supportavdelningen/lösen ords administratör
* Faktureringsadministratör
* Användaradministratör

När du aktiverar principen Kräv MFA för administratörer krävs de ovannämnda nio administratörs rollerna som ska registreras för MFA med hjälp av Authenticator-appen. När MFA-registreringen är klar måste administratörerna utföra MFA varje gång de loggar in.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Eftersom principen **KRÄV MFA för administratörer (för hands version)** gäller för alla kritiska administratörer måste flera saker vidtas för att säkerställa en smidig distribution. Dessa överväganden omfattar att identifiera användare och tjänst principer i Azure AD som inte kan eller inte bör utföra MFA, samt program och klienter som används av din organisation som inte stöder modern autentisering.

### <a name="legacy-protocols"></a>Äldre protokoll

Bakåtkompatibla autentiseringsprotokoll (IMAP, SMTP, POP3 osv.) används av e-postklienter för att göra autentiseringsbegäranden. Dessa protokoll stöder inte MFA. De flesta konto kompromisser som visas av Microsoft orsakas av felaktiga aktörer som utför attacker mot äldre protokoll som försöker kringgå MFA. För att säkerställa att MFA krävs vid inloggning på ett administrativt konto och felaktiga aktörer inte kan kringgå MFA, blockerar den här principen alla autentiseringsbegäranden som görs till administratörs konton från äldre protokoll.

> [!WARNING]
> Innan du aktiverar den här principen bör du kontrol lera att dina administratörer inte använder äldre autentiseringsprotokoll. Se artikeln [How to: Blockera äldre autentisering till Azure AD med villkorlig åtkomst](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) för mer information.

## <a name="enable-the-baseline-policy"></a>Aktivera bas linje principen

Princip **bas linje princip: Kräv MFA för administratörer (för hands** version) är förkonfigurerade och visas överst när du navigerar till bladet för villkorlig åtkomst i Azure Portal.

Så här aktiverar du den här principen och skyddar dina administratörer:

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. I listan med principer väljer **du bas linje princip: Kräv MFA för administratörer (för hands**version).
1. Ange principen för att **aktivera principen** **direkt**.
1. Klicka på **Spara**.

> [!WARNING]
> Det uppstod ett alternativ **för att automatiskt aktivera principen i framtiden** när den här principen var i för hands version. Vi har tagit bort det här alternativet för att minimera plötslig användar påverkan. Om du har valt det här alternativet när det var tillgängligt ska **du inte använda principen** automatiskt nu. Om de vill använda den här bas linje principen, se stegen ovan för att aktivera den.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Principer för bas linje skydd för villkorlig åtkomst](concept-baseline-protection.md)
* [Fem steg för att skydda din identitets infrastruktur](../../security/fundamentals/steps-secure-identity.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)
