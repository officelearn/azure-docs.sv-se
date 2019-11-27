---
title: Azure Active Directory säkerhets inställningar
description: Säkerhets standard principer som skyddar organisationer från vanliga attacker
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 806447f788b7a394b29ee08e8b562662c2cc3e1b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208404"
---
# <a name="what-are-security-defaults"></a>Vad är säkerhets inställningar?

Det kan vara svårt att hantera säkerhet när vanliga problem med identitets attacker blir mer populära. Bland dessa attacker ingår lösen ords spridning, uppspelning och nätfiske.

Standardvärden för säkerhet i Azure Active Directory (Azure AD) gör det enklare att skydda din organisation. Säkerhets standarder innehåller förkonfigurerade säkerhets inställningar för vanliga attacker. 

Microsoft skapar säkerhets inställningar som är tillgängliga för alla. Målet är att säkerställa att alla organisationer har en grundläggande säkerhets nivå som är aktive rad utan extra kostnad. Du aktiverar säkerhets inställningarna i Azure Portal.

![Skärm bild av Azure Portal med växla för att aktivera säkerhets inställningar](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
Följande säkerhetskonfigurationer aktive ras i din klient organisation. 

## <a name="unified-multi-factor-authentication-registration"></a>Enhetlig Multi-Factor Authentication registrering

Alla användare i din klient organisation måste registrera sig för Multi-Factor Authentication (MFA) i form av Azure Multi-Factor Authentication-tjänsten. Användare har 14 dagar på sig att registrera sig för Multi-Factor Authentication med hjälp av Microsoft Authenticator-appen. När 14 dagar har passerat kan användaren inte logga in förrän Multi-Factor Authentication registreringen är klar.

Vi förstår att vissa användare kan vara frånvarande eller att inte logga in under 14 dagar omedelbart efter att säkerhets inställningarna har Aktiver ATS. För att säkerställa att varje användare har rimlig tid att registrera sig för Multi-Factor Authentication, är 14-dagars perioden unik för varje användare. En användares 14-dagars period börjar efter sin första lyckade interaktiva inloggning när du har aktiverat säkerhets inställningarna.

## <a name="multi-factor-authentication-enforcement"></a>Multi-Factor Authentication tillämpning

### <a name="protecting-administrators"></a>Skydda administratörer

Användare med åtkomst till privilegierade konton har ökat åtkomst till din miljö. På grund av den kraft de här kontona har måste du behandla dem med särskild omsorg. En gemensam metod för att förbättra skyddet av privilegierade konton är att kräva en starkare form av konto verifiering för inloggning. I Azure AD kan du få en bättre konto verifiering genom att kräva Multi-Factor Authentication.

När registreringen med Multi-Factor Authentication har avslut ATS krävs följande nio Azure AD-administratörs roller för att utföra ytterligare autentisering varje gång de loggar in:

- Global administratör
- SharePoint-administratör
- Exchange-administratör
- Administratör för villkorlig åtkomst
- Säkerhetsadministratör
- Administratör för supportavdelningen eller lösen ords administratör
- Faktureringsadministratör
- Användar administratör
- Administratör för autentisering

### <a name="protecting-all-users"></a>Skydda alla användare

Vi tenderar att tro att administratörs konton är de enda konton som behöver extra lager av autentisering. Administratörer har bred åtkomst till känslig information och kan göra ändringar i inställningarna för hela prenumerationen. Men angripare brukar riktas mot slutanvändare. 

När angripare får åtkomst kan de begära åtkomst till privilegie rad information å den ursprungliga konto innehavarens vägnar. De kan till och med Ladda ned hela katalogen för att utföra en nätfiske-attack på hela organisationen. 

En gemensam metod för att förbättra skyddet för alla användare är att kräva en starkare form av konto verifiering, till exempel Multi-Factor Authentication, för alla. När användarna har slutfört Multi-Factor Authentication registreringen uppmanas de att ange ytterligare autentisering när det behövs.

### <a name="blocking-legacy-authentication"></a>Blockerar äldre autentisering

För att ge dina användare enkel åtkomst till dina molnappar stöder Azure AD olika autentiseringsprotokoll, inklusive äldre autentisering. *Äldre autentisering* är en term som hänvisar till en autentiseringsbegäran som görs av:

- Äldre Office-klienter som inte använder modern autentisering (till exempel en Office 2010-klient).
- Alla klienter som använder äldre e-postprotokoll som IMAP, SMTP eller POP3.

Idag kommer majoriteten av att kompromissa inloggnings försök komma från äldre autentisering. Äldre autentisering stöder inte Multi-Factor Authentication. Även om du har aktiverat en Multi-Factor Authentication princip i din katalog kan en angripare autentisera med hjälp av ett äldre protokoll och kringgå Multi-Factor Authentication. 

När säkerhets inställningarna har Aktiver ATS i din klient kommer alla autentiseringsbegäranden som gjorts av ett äldre protokoll att blockeras. Säkerhets standarder blockerar inte Exchange ActiveSync.

### <a name="protecting-privileged-actions"></a>Skydda privilegierade åtgärder

Organisationer använder en mängd olika Azure-tjänster som hanteras via Azure Resource Manager API, inklusive:

- Azure Portal 
- Azure PowerShell 
- Azure CLI

Att använda Azure Resource Manager för att hantera dina tjänster är en mycket privilegie rad åtgärd. Azure Resource Manager kan ändra konfigurationer för hela klienten, till exempel tjänst inställningar och fakturering av prenumerationer. Autentisering med en faktor är utsatt för en rad olika attacker, t. ex. nätfiske och lösen ords sprayning. 

Det är viktigt att du verifierar identiteten för användare som vill komma åt Azure Resource Manager och uppdatera konfigurationer. Du verifierar identiteten genom att kräva ytterligare autentisering innan du tillåter åtkomst.

När du har aktiverat säkerhets inställningarna i din klient, måste alla användare som har åtkomst till Azure Portal, Azure PowerShell eller Azure CLI slutföra ytterligare autentisering. Den här principen gäller för alla användare som har åtkomst till Azure Resource Manager, oavsett om de är en administratör eller en användare. 

Om användaren inte har registrerats för Multi-Factor Authentication måste användaren registrera sig med hjälp av Microsoft Authenticator-appen för att kunna fortsätta. Ingen 14-dagars Multi-Factor Authentication registrerings period kommer att tillhandahållas.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Följande ytterligare överväganden är relaterade till distribution av säkerhets inställningar för din klient.

### <a name="older-protocols"></a>Äldre protokoll

E-postklienter använder äldre autentiseringsprotokoll (t. ex. IMAP, SMTP och POP3) för att göra autentiseringsbegäranden. Dessa protokoll stöder inte Multi-Factor Authentication. De flesta konto kompromisser som Microsoft ser är från attacker mot äldre protokoll som försöker kringgå Multi-Factor Authentication. 

För att säkerställa att Multi-Factor Authentication krävs för att logga in på ett administrativt konto och att angripare inte kan kringgå det, blockerar säkerhets standardvärden alla autentiseringsbegäranden till administratörs konton från äldre protokoll.

> [!WARNING]
> Innan du aktiverar den här inställningen ser du till att administratörer inte använder äldre autentiseringsprotokoll. Mer information finns i [så här flyttar du bort från äldre autentisering](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Villkorlig åtkomst

Du kan använda villkorlig åtkomst för att konfigurera principer som ger samma beteende som säkerhets inställningar. Om du använder villkorlig åtkomst och har principer för villkorlig åtkomst aktive rad i din miljö är säkerhets inställningar inte tillgängliga för dig. Om du har en licens som ger villkorlig åtkomst men inte har några principer för villkorlig åtkomst aktive rad i din miljö, är du välkommen att använda säkerhets inställningar tills du aktiverar principer för villkorlig åtkomst.

![Varnings meddelande om att du kan ha säkerhets inställningar eller villkorlig åtkomst inte båda](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Här följer stegvisa guider om hur du kan använda villkorlig åtkomst för att konfigurera likvärdiga principer:

- [Kräv MFA för administratörer](howto-conditional-access-policy-admin-mfa.md)
- [Kräv MFA för Azure-hantering](howto-conditional-access-policy-azure-management.md)
- [Blockera äldre autentisering](howto-conditional-access-policy-block-legacy.md)
- [Kräv MFA för alla användare](howto-conditional-access-policy-all-users-mfa.md)
- [Kräv Azure MFA-registrering](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – kräver Azure AD Identity Protection

## <a name="enabling-security-defaults"></a>Aktivera säkerhets inställningar

Så här aktiverar du säkerhets inställningar i din katalog:

1. Logga in på [Azure Portal](https://portal.azure.com) som säkerhets administratör, administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till **Azure Active Directory** > **Egenskaper**.
1. Välj **hantera säkerhets inställningar**.
1. Ställ in **Aktivera säkerhets inställningar** växla till **Ja**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Vad är villkorlig åtkomst?](overview.md)
