---
title: Standardvärden för Azure Active Directory-säkerhet
description: Standardprinciper för säkerhet som hjälper till att skydda organisationer från vanliga attacker
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
ms.openlocfilehash: 3139d39797c3bc576bb39f1438b7e6d3f37e3c5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248849"
---
# <a name="what-are-security-defaults"></a>Vad är standardvärden för säkerhet?

Det kan vara svårt att hantera säkerheten när vanliga identitetsrelaterade attacker blir allt mer populära. Dessa attacker inkluderar lösenordsspray, repris och nätfiske.

Standardvärden för säkerhet i Azure Active Directory (Azure AD) gör det enklare att vara säker och skydda din organisation. Standardinställningar för säkerhet innehåller förkonfigurerade säkerhetsinställningar för vanliga attacker. 

Microsoft gör säkerhetsstandarder tillgängliga för alla. Målet är att se till att alla organisationer har en grundläggande säkerhetsnivå aktiverad utan extra kostnad. Du aktiverar säkerhetsstandarder i Azure-portalen.

![Skärmbild av Azure-portalen med växlingsknappen för att aktivera säkerhetsstandarder](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Om din klient skapades den 22 oktober 2019 eller senare är det möjligt att du upplever det nya beteendet för säker som standard och redan har aktiverat säkerhetsstandarder i din klientorganisation. I ett försök att skydda alla våra användare distribueras säkerhetsinställningar till alla nya klienter som skapas.

Mer information om varför säkerhetsstandarder görs tillgängliga finns i Alex Weinerts blogginlägg, [Introducing security defaults](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="unified-multi-factor-authentication-registration"></a>Enhetlig registrering av multifaktorautentisering

Alla användare i din klient måste registrera sig för MFA (Multi Factor Authentication) i form av Azure Multi-Factor Authentication Service. Användare har 14 dagar på sig att registrera sig för multifaktorautentisering med hjälp av Microsoft Authenticator-appen. När 14 dagar har gått kan användaren inte logga in förrän registreringen av multifaktorautentisering är klar.

Vi förstår att vissa användare kan vara lediga eller inte logga in under de 14 dagarna omedelbart efter att säkerhetsinställningarna har aktiverats. För att säkerställa att varje användare har gott om tid att registrera sig för multifaktorautentisering är 14-dagarsperioden unik för varje användare. En användares 14-dagarsperiod börjar efter deras första lyckade interaktiva inloggning när du har aktiverat standardinställningar för säkerhet.

## <a name="multi-factor-authentication-enforcement"></a>Tvingande autentisering med flera faktorer

### <a name="protecting-administrators"></a>Skydda administratörer

Användare med åtkomst till privilegierade konton har ökad åtkomst till din miljö. På grund av den makt dessa konton har, bör du behandla dem med särskild omsorg. En vanlig metod för att förbättra skyddet av privilegierade konton är att kräva en starkare form av kontoverifiering för inloggning. I Azure AD kan du få en starkare kontoverifiering genom att kräva multifaktorautentisering.

När registreringen med multifaktorautentisering är klar krävs följande nio Azure AD-administratörsroller för att utföra ytterligare autentisering varje gång de loggar in:

- Global administratör
- SharePoint-administratör
- Exchange-administratör
- Administratör för villkorlig åtkomst
- Säkerhetsadministratör
- Helpdesk-administratör eller lösenordsadministratör
- Faktureringsadministratör
- Användaradministratör
- Administratör för autentisering

### <a name="protecting-all-users"></a>Skydda alla användare

Vi tenderar att tro att administratörskonton är de enda konton som behöver extra lager av autentisering. Administratörer har bred åtkomst till känslig information och kan göra ändringar i inställningar för prenumerationsomfattande. Men angripare tenderar att rikta slutanvändare. 

När dessa angripare har fått åtkomst kan de begära åtkomst till privilegierad information för den ursprungliga kontoinnehavarens räkning. De kan även ladda ner hela katalogen för att utföra en phishing-attack på hela organisationen. 

En vanlig metod för att förbättra skyddet för alla användare är att kräva en starkare form av kontoverifiering, till exempel multifaktorautentisering, för alla. När användarna har slutfört multifaktorautentiseringsregistreringen uppmanas de att ange ytterligare autentisering när det behövs.

### <a name="blocking-legacy-authentication"></a>Blockera äldre autentisering

För att ge användarna enkel åtkomst till dina molnappar stöder Azure AD en mängd olika autentiseringsprotokoll, inklusive äldre autentisering. *Äldre autentisering* är en term som refererar till en autentiseringsbegäran som gjorts av:

- Klienter som inte använder modern autentisering (till exempel en Office 2010-klient).
- Alla klienter som använder äldre e-postprotokoll som IMAP, SMTP eller POP3.

Idag kommer majoriteten av komprometterande inloggningsförsök från äldre autentisering. Äldre autentisering stöder inte multifaktorautentisering. Även om du har aktiverat en multifaktorautentiseringsprincip på katalogen kan en angripare autentisera med hjälp av ett äldre protokoll och kringgå multifaktorautentisering. 

När säkerhetsstandarder har aktiverats i din klientorganisation blockeras alla autentiseringsbegäranden som görs av ett äldre protokoll. Standardvärden för säkerhet blockerar grundläggande autentisering av Exchange Active Sync.

> [!WARNING]
> Innan du aktiverar standardinställningar för säkerhet kontrollerar du att administratörerna inte använder äldre autentiseringsprotokoll. Mer information finns i [Så här går du bort från äldre autentisering](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Skydda privilegierade åtgärder

Organisationer använder en mängd olika Azure-tjänster som hanteras via Azure Resource Manager API, inklusive:

- Azure Portal 
- Azure PowerShell 
- Azure CLI

Att använda Azure Resource Manager för att hantera dina tjänster är en mycket privilegierad åtgärd. Azure Resource Manager kan ändra klientomfattande konfigurationer, till exempel tjänstinställningar och prenumerationsfakturering. Autentisering med en faktor är sårbar för en mängd olika attacker som nätfiske och lösenordsspray. 

Det är viktigt att verifiera identiteten för användare som vill komma åt Azure Resource Manager och uppdatera konfigurationer. Du verifierar deras identitet genom att kräva ytterligare autentisering innan du tillåter åtkomst.

När du har aktiverat standardvärden för säkerhet i din klientorganisation måste alla användare som har åtkomst till Azure-portalen, Azure PowerShell eller Azure CLI slutföra ytterligare autentisering. Den här principen gäller för alla användare som har åtkomst till Azure Resource Manager, oavsett om de är administratör eller användare. 

Om användaren inte är registrerad för multifaktorautentisering måste användaren registrera sig med hjälp av Microsoft Authenticator-appen för att kunna fortsätta. Ingen 14-dagars registreringsperiod för multifaktorautentisering kommer att tillhandahållas.

> [!NOTE]
> Exchange Online-klienter före 2017 har modern autentisering inaktiverad som standard. För att undvika möjligheten till en inloggningsloop när du autentiserar via dessa klienter måste du [aktivera modern autentisering](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Azure AD Connect-synkroniseringskontot är uteslutet från säkerhetsstandarder och uppmanas inte att registrera sig för eller utföra multifaktorautentisering. Organisationer bör inte använda det här kontot för andra ändamål.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Följande ytterligare överväganden är relaterade till distribution av säkerhetsstandarder för din klient.

### <a name="authentication-methods"></a>Autentiseringsmetoder

Standardinställningar för säkerhet tillåter registrering och användning av Azure Multi-Factor Authentication **med endast Microsoft Authenticator-appen med hjälp av meddelanden**. Villkorlig åtkomst gör det möjligt att använda alla autentiseringsmetoder som administratören väljer att aktivera.

|   | Standardinställningar för säkerhet | Villkorlig åtkomst |
| --- | --- | --- |
| Avisering via mobilapp | X | X |
| Verifieringskod från mobilapp eller maskinvarutoken |   | X |
| Sms till telefon |   | X |
| Ringa till telefon |   | X |
| Applösenord |   | X** |

** Applösenord är endast tillgängliga i MFA per användare med äldre autentiseringsscenarier endast om de är aktiverade av administratörer.

### <a name="conditional-access"></a>Villkorlig åtkomst

Du kan använda villkorlig åtkomst för att konfigurera principer som liknar säkerhetsstandarder, men med mer detaljerad information inklusive användaruteslutningar, som inte är tillgängliga i säkerhetsstandarder. Om du använder villkorlig åtkomst och har principer för villkorlig åtkomst aktiverade i din miljö är säkerhetsstandarder inte tillgängliga för dig. Om du har en licens som tillhandahåller villkorlig åtkomst men inte har några principer för villkorlig åtkomst aktiverade i din miljö är du välkommen att använda säkerhetsstandarder tills du aktiverar principer för villkorlig åtkomst. Mer information om Azure AD-licensiering finns på [sidan Azure AD-priser](https://azure.microsoft.com/pricing/details/active-directory/).

![Varningsmeddelande om att du kan ha säkerhetsstandarder eller villkorlig åtkomst inte båda](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Här är steg-för-steg-guider om hur du kan använda villkorlig åtkomst för att konfigurera motsvarande principer:

- [Kräv MFA för administratörer](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Kräv MFA för Azure-hantering](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blockera äldre autentisering](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Kräv MFA för alla användare](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Kräv Azure MFA-registrering](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – kräver Azure AD-identitetsskydd

## <a name="enabling-security-defaults"></a>Aktivera standardvärden för säkerhet

Så här aktiverar du standardinställningar för säkerhet i katalogen:

1. Logga in på [Azure-portalen](https://portal.azure.com) som säkerhetsadministratör, administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till Azure > **Active** **Directory-egenskaper**.
1. Välj **Hantera säkerhetsstandarder**.
1. Ange **växlingsknappen Aktivera säkerhet som standard** till **Ja**.
1. Välj **Spara**.

## <a name="disabling-security-defaults"></a>Inaktivera säkerhetsstandarder

Organisationer som väljer att implementera principer för villkorlig åtkomst som ersätter standardvärden för säkerhet måste inaktivera standardvärden för säkerhet. 

![Varningsmeddelande inaktiverar säkerhetsstandarder för att aktivera villkorlig åtkomst](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Så här inaktiverar du standardvärden för säkerhet i katalogen:

1. Logga in på [Azure-portalen](https://portal.azure.com) som säkerhetsadministratör, administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till Azure > **Active** **Directory-egenskaper**.
1. Välj **Hantera säkerhetsstandarder**.
1. Ange **växlingsknappen Aktivera säkerhetsstandarder** till **Nej**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md)
