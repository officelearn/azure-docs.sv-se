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
ms.openlocfilehash: 4474283b9a233e39497cd05f0f04ea0984f02401
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560939"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Baslinjeprincip: Kräva MFA för administratörer (förhandsversion)

Användare med åtkomst till behöriga konton har obegränsad åtkomst till din miljö. På grund av strömmen dessa konton, ska du hantera dem med särskild försiktighet. En vanlig metod för att förbättra skyddet av Privilegierade konton är att kräva en starkare form av verifiering för kontot när de används för att logga in. I Azure Active Directory, kan du få en starkare Kontoverifiering genom att kräva multifaktorautentisering (MFA).

**Kräva MFA för administratörer (förhandsversion)**  är en [baslinjeprincip](concept-baseline-protection.md) som kräver MFA varje gång som något av följande administratörsroller i privileged loggar in:

* Global administratör
* SharePoint-administratör
* Exchange-administratör
* Administratör för villkorlig åtkomst
* Säkerhetsadministratör
* Supportavdelningsadministratören / lösenordsadministratör
* Faktureringsadministratör
* Användaradministratör

När du aktiverar den kräver MFA för administratörer principen kommer ovan nio administratörsroller att behöva registrera för MFA med Autentiseringsappen. När MFA-registrering är klar, behöver administratörer och genomför MFA varje gång de loggar in.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Eftersom den **kräver MFA för administratörer (förhandsversion)** principen gäller för alla kritiska administratörer, flera saker som behöver göras för att säkerställa en smidig distribution. Dessa överväganden omfattar identifiera användare och tjänsten principer i Azure AD som inte kan eller inte utför MFA, samt program och klienter som används av din organisation och som inte stöder modern autentisering.

### <a name="legacy-protocols"></a>Äldre protokoll

Äldre autentiseringsprotokoll (IMAP, SMTP-, POP3 osv.) som används av e-postklienter för autentisering. Dessa protokoll har inte stöd för MFA. De flesta av de konto kompromisser som setts av Microsoft orsakas av illvilliga aktörer utför attacker mot äldre protokoll försök att kringgå MFA. För att säkerställa att MFA krävs när du loggar in ett administratörskonto och obehöriga inte kringgå MFA, blockerar den här principen alla autentiseringsbegäranden administratörskonton från äldre protokoll.

> [!WARNING]
> Innan du aktiverar den här principen måste du kontrollera att dina administratörer inte använder äldre autentiseringsprotokoll. Finns i artikeln [så här: Blockera äldre autentisering till Azure AD med villkorlig åtkomst](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) för mer information.

## <a name="enable-the-baseline-policy"></a>Aktivera baslinjeprincip för

Principen **baslinjeprincip: Kräva MFA för administratörer (förhandsversion)** är förkonfigurerad och visas högst upp när du navigerar till bladet för villkorlig åtkomst i Azure-portalen.

Att aktivera den här principen och skydda dina administratörer:

1. Logga in på den **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj i listan med principer, **baslinjeprincip: Kräva MFA för administratörer (förhandsversion)** .
1. Ange **aktiverar principen** till **Använd principen omedelbart**.
1. Klicka på **spara**.

> [!WARNING]
> Det uppstod ett alternativ **aktivera principen automatiskt i framtiden** när den här principen fanns i en förhandsversion. Vi har tagit bort det här alternativet för att minimera plötsliga användarna påverkas. Om du har valt det här alternativet när det inte fanns **Använd inte principen** automatiskt nu markerade. Om de vill använda den här baslinjeprincip, se stegen ovan för att aktivera den.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Protection principer för villkorlig åtkomst baslinje](concept-baseline-protection.md)
* [Fem steg för att skydda din infrastruktur för Identitetshantering](../../security/azure-ad-secure-steps.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)
