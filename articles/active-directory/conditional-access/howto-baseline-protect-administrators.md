---
title: Baslinjeprincip kräver MFA för administratörer – Azure Active Directory
description: Princip för villkorlig åtkomst att kräva multifaktorautentisering för administratörer
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
ms.openlocfilehash: 4f8b7f281ad5ed8424110696544ffdb49e50ce59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112429"
---
# <a name="baseline-policy-require-mfa-for-admins"></a>Baslinjeprincip: Kräva MFA för administratörer

Användare med åtkomst till behöriga konton har obegränsad åtkomst till din miljö. På grund av strömmen dessa konton, ska du hantera dem med särskild försiktighet. En vanlig metod för att förbättra skyddet av Privilegierade konton är att kräva en starkare form av verifiering för kontot när de används för att logga in. I Azure Active Directory, kan du få en starkare Kontoverifiering genom att kräva multifaktorautentisering (MFA).

**Kräva MFA för administratörer** är en [baslinjeprincip](concept-baseline-protection.md) som kräver MFA varje gång som något av följande administratörsroller i privileged loggar in:

* Global administratör
* SharePoint-administratör
* Exchange-administratör
* Administratör för villkorlig åtkomst
* Säkerhetsadministratör
* Supportavdelningsadministratören / lösenordsadministratör
* Faktureringsadministratör
* Användaradministratör

När du aktiverar den kräver MFA för administratörer principen kommer ovan nio administratörsroller att behöva registrera för MFA med Autentiseringsappen. När MFA-registrering är klar, behöver administratörer och genomför MFA varje gång de loggar in.

![Kräva MFA för administratörer baslinjeprincip](./media/howto-baseline-protect-administrators/baseline-policy-require-mfa-for-admins.png)

## <a name="deployment-considerations"></a>Distributionsöverväganden

Eftersom den **kräver MFA för administratörer** principen gäller för alla kritiska administratörer, flera saker som behöver göras för att säkerställa en smidig distribution. Dessa överväganden omfattar identifiera användare och tjänsten principer i Azure AD som inte kan eller inte utför MFA, samt program och klienter som används av din organisation och som inte stöder modern autentisering.

### <a name="legacy-protocols"></a>Äldre protokoll

Äldre autentiseringsprotokoll (IMAP, SMTP-, POP3 osv.) som används av e-postklienter för autentisering. Dessa protokoll har inte stöd för MFA. De flesta av de konto kompromisser som setts av Microsoft orsakas av illvilliga aktörer utför attacker mot äldre protokoll försök att kringgå MFA. För att säkerställa att MFA krävs när du loggar in ett administratörskonto och obehöriga inte kringgå MFA, blockerar den här principen alla autentiseringsbegäranden administratörskonton från äldre protokoll.

> [!WARNING]
> Innan du aktiverar den här principen måste du kontrollera att dina administratörer inte använder äldre autentiseringsprotokoll. Finns i artikeln [så här: Blockera äldre autentisering till Azure AD med villkorlig åtkomst](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) för mer information.

### <a name="user-exclusions"></a>Undantag för användaren

Den här baslinjeprincip ger dig möjlighet att exkludera användare. Innan du aktiverar principen för din klient, rekommenderar vi att du exkluderar följande konton:

* **För åtkomst vid akutfall** eller **glas** konton för att förhindra klienttäckande kontoutelåsning. I det osannolika scenariot alla administratörer har låsts ute från din klient, kan dina administrativa åtkomstkonto i nödfall användas för att logga in på klienten gör stegen tillgång.
   * Mer information finns i artikeln [hantera åtkomst vid akutfall i Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Tjänstkonton** och **tjänsten principer**, till exempel Azure AD Connect Sync-kontot. Tjänstkonton är icke-interaktiva konton som inte är knutna till en viss användare. De som vanligtvis används av backend-tjänster och Tillåt Programmeringsåtkomst till program. Tjänstkonton ska uteslutas eftersom inte går att slutföra MFA genom programmering.
   * Om din organisation har dessa konton som används i skript eller kod kan du överväga att ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan undanta du dessa specifika konton från baslinje-principen.
* Användare som inte har eller inte kommer att kunna använda en Smartphone.
   * Den här principen kräver att registrera för MFA med hjälp av Microsoft Authenticator-appen administratörer.

## <a name="enable-the-baseline-policy"></a>Aktivera baslinjeprincip för

Principen **baslinjeprincip: Kräva MFA för administratörer** är förkonfigurerad och visas högst upp när du navigerar till bladet för villkorlig åtkomst i Azure-portalen.

Att aktivera den här principen och skydda dina administratörer:

1. Logga in på den **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj i listan med principer, **baslinjeprincip: Kräva MFA för administratörer**.
1. Ange **aktiverar principen** till **Använd principen omedelbart**.
1. Lägg till användare undantag genom att klicka på **användare** > **Välj exkluderade användare** och välja de användare som behöver som ska undantas. Klicka på **Välj** sedan **klar**.
1. Klicka på **spara**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Protection principer för villkorlig åtkomst baslinje](concept-baseline-protection.md)
* [Fem steg för att skydda din infrastruktur för Identitetshantering](../../security/azure-ad-secure-steps.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)
