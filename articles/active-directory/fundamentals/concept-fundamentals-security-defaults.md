---
title: Azure Active Directory säkerhets inställningar
description: Säkerhets standard principer som skyddar organisationer från vanliga attacker
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73de446513915bbc5cba9d8b9cf23d08d18cc362
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585993"
---
# <a name="what-are-security-defaults"></a>Vad är säkerhets inställningar?

Det kan vara svårt att hantera säkerhet när vanliga problem med identitets attacker blir mer populära. Bland dessa attacker ingår lösen ords spridning, uppspelning och nätfiske.

Standardvärden för säkerhet i Azure Active Directory (Azure AD) gör det enklare att skydda din organisation. Säkerhets standarder innehåller förkonfigurerade säkerhets inställningar för vanliga attacker. 

Microsoft skapar säkerhets inställningar som är tillgängliga för alla. Målet är att säkerställa att alla organisationer har en grundläggande säkerhets nivå som är aktive rad utan extra kostnad. Du aktiverar säkerhets inställningarna i Azure Portal.

![Skärm bild av Azure Portal med växla för att aktivera säkerhets inställningar](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Om din klient skapades på eller efter den 22 oktober 2019, är det möjligt att du har den nya säkraste standarden och redan har säkerhets standardvärden aktiverade i din klient. I ett arbete för att skydda alla våra användare kommer säkerhets inställningarna att distribueras till alla nya klienter som skapats.

Mer information om varför säkerhets inställningar görs tillgängliga finns i blogg inlägget Alex Weinert, och [vi presenterar säkerhets inställningar](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

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

När säkerhets inställningarna har Aktiver ATS i din klient kommer alla autentiseringsbegäranden som gjorts av ett äldre protokoll att blockeras. Säkerhets standardvärden blockerar Exchange Active Sync grundläggande autentisering.

> [!WARNING]
> Innan du aktiverar säkerhets inställningarna kontrollerar du att administratörerna inte använder äldre autentiseringsprotokoll. Mer information finns i [så här flyttar du bort från äldre autentisering](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Skydda privilegierade åtgärder

Organisationer använder en mängd olika Azure-tjänster som hanteras via Azure Resource Manager API, inklusive:

- Azure-portalen 
- Azure PowerShell 
- Azure CLI

Att använda Azure Resource Manager för att hantera dina tjänster är en mycket privilegie rad åtgärd. Azure Resource Manager kan ändra konfigurationer för hela klienten, till exempel tjänst inställningar och fakturering av prenumerationer. Autentisering med en faktor är utsatt för en rad olika attacker, t. ex. nätfiske och lösen ords sprayning. 

Det är viktigt att du verifierar identiteten för användare som vill komma åt Azure Resource Manager och uppdatera konfigurationer. Du verifierar identiteten genom att kräva ytterligare autentisering innan du tillåter åtkomst.

När du har aktiverat säkerhets inställningarna i din klient, måste alla användare som har åtkomst till Azure Portal, Azure PowerShell eller Azure CLI slutföra ytterligare autentisering. Den här principen gäller för alla användare som har åtkomst till Azure Resource Manager, oavsett om de är en administratör eller en användare. 

Om användaren inte har registrerats för Multi-Factor Authentication måste användaren registrera sig med hjälp av Microsoft Authenticator-appen för att kunna fortsätta. Ingen 14-dagars Multi-Factor Authentication registrerings period kommer att tillhandahållas.

> [!NOTE]
> Exchange Online-klienter före 2017 har modern autentisering inaktive rad som standard. För att undvika risken för en inloggnings slinga vid autentisering via dessa klienter måste du [Aktivera modern autentisering](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Kontot för Azure AD Connect-synkronisering exkluderas från säkerhets inställningarna och kommer inte att uppmanas att registrera sig för eller utföra Multi-Factor Authentication. Organisationer bör inte använda det här kontot för andra orsaker.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Följande ytterligare överväganden är relaterade till distribution av säkerhets inställningar för din klient.

### <a name="authentication-methods"></a>Autentiseringsmetoder

Säkerhets standarder tillåter registrering och användning av Azure-Multi-Factor Authentication att **bara använda Microsoft Authenticator-appen med hjälp av meddelanden**. Villkorlig åtkomst tillåter användning av alla autentiseringsmetoder som administratören väljer att aktivera.

|   | Standardinställningar för säkerhet | Villkorlig åtkomst |
| --- | --- | --- |
| Meddelande via mobilapp | X | X |
| Verifierings kod från mobilapp eller maskinvaru-token |   | X |
| Textmeddelande till telefon |   | X |
| Ring till telefon |   | X |
| Applösenord |   | X * * |

\* * Applösenord är bara tillgängliga i MFA per användare med äldre autentiseringar endast om de aktive ras av administratörer.

### <a name="conditional-access"></a>Villkorlig åtkomst

Du kan använda villkorlig åtkomst för att konfigurera principer som liknar säkerhets inställningarna, men med mer detaljerad information, inklusive användar undantag, som inte är tillgängliga i säkerhets inställningarna. Om du använder villkorlig åtkomst och har principer för villkorlig åtkomst aktive rad i din miljö är säkerhets inställningar inte tillgängliga för dig. Om du har en licens som ger villkorlig åtkomst men inte har några principer för villkorlig åtkomst aktive rad i din miljö, är du välkommen att använda säkerhets inställningar tills du aktiverar principer för villkorlig åtkomst. Mer information om Azure AD-licensiering finns på [prissättnings sidan för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Varnings meddelande om att du kan ha säkerhets inställningar eller villkorlig åtkomst inte båda](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Här följer stegvisa guider om hur du kan använda villkorlig åtkomst för att konfigurera likvärdiga principer:

- [Kräv MFA för administratörer](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Kräv MFA för Azure-hantering](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blockera äldre autentisering](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Kräv MFA för alla användare](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Kräv Azure MFA-registrering](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – kräver Azure AD Identity Protection

## <a name="enabling-security-defaults"></a>Aktivera säkerhets inställningar

Så här aktiverar du säkerhets inställningar i din katalog:

1. Logga in på [Azure Portal](https://portal.azure.com) som säkerhets administratör, administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till **Azure Active Directory** > **Egenskaper**.
1. Välj **hantera säkerhets inställningar**.
1. Ställ in **Aktivera säkerhets inställningar** växla till **Ja**.
1. Välj **Spara**.

## <a name="disabling-security-defaults"></a>Inaktivera säkerhets inställningar

Organisationer som väljer att implementera principer för villkorlig åtkomst som ersätter säkerhets standarder måste inaktivera säkerhets inställningar. 

![Varnings meddelande inaktivera säkerhets inställningar för att aktivera villkorlig åtkomst](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Så här inaktiverar du säkerhets inställningar i din katalog:

1. Logga in på [Azure Portal](https://portal.azure.com) som säkerhets administratör, administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till **Azure Active Directory** > **Egenskaper**.
1. Välj **hantera säkerhets inställningar**.
1. Ställ in **Aktivera säkerhets standardvärden** växla till **Nej**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md)
