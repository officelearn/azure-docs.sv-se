---
title: Azure Active Directory säkerhets inställningar
description: Säkerhets principer som är utformade för att skydda organisationer från vanliga attacker
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453048"
---
# <a name="what-are-security-defaults"></a>Vad är säkerhets inställningar?

Det kan vara svårt att hantera säkerhet om vanliga attacker, till exempel sprayning av lösen ord, uppspelning och nätfiske, blir mer och mer populära. Att skapa ett enklare sätt att göra din organisation mer skyddad från dessa vanliga attacker är målet med säkerhets inställningar i Azure Active Directory (AD). Med säkerhets inställningar blir det enklare att skydda din organisation från vanliga attacker. Säkerhets standarder innehåller förkonfigurerade säkerhets inställningar för dessa vanliga attacker. Microsoft skapar säkerhets inställningar som är tillgängliga för alla. Målet är att säkerställa att alla organisationer har en grundläggande säkerhets nivå som är aktive rad utan extra kostnad.

![Skärm bild av den nya säkerhets standarden UX](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
Följande säkerhetskonfigurationer aktive ras i din klient organisation. 

## <a name="unified-mfa-registration"></a>Enhetlig MFA-registrering

Alla användare i din klient organisation behöver registreras för Azure Multi-Factor Authentication (MFA). Användare kommer att ha 14 dagar på sig att registrera sig för Azure MFA med hjälp av Microsoft Authenticator-appen. När 14 dagar har passerat kommer användaren inte att kunna logga in förrän MFA-registreringen har slutförts.

Vi förstår att vissa användare kan vara frånvarande och/eller att de inte kommer att logga in under 14 dagar direkt efter att säkerhets inställningarna aktive ras. För att se till att alla användare får rimlig tid att registrera sig för MFA är 14-dagars perioden unik för varje användare. En användares 14-dagars period börjar efter sin första lyckade interaktiva inloggning när säkerhets inställningarna är aktiverade.

## <a name="mfa-enforcement"></a>MFA-tvång

### <a name="protecting-administrators"></a>Skydda administratörer

Användare med åtkomst till privilegierade konton har ökat åtkomst till din miljö. På grund av den kraft de här kontona har måste du behandla dem med särskild omsorg. En gemensam metod för att förbättra skyddet av privilegierade konton är att kräva en starkare form av konto verifiering när de används för inloggning. I Azure Active Directory kan du få en bättre konto verifiering genom att kräva Multi-Factor Authentication.

När MFA-registreringen har slutförts krävs följande nio Azure AD-administratörs roller för att utföra MFA varje gång de loggar in.

- Global administratör
- SharePoint-administratör
- Exchange-administratör
- Administratör för villkorlig åtkomst
- Säkerhetsadministratör
- Administratör för supportavdelningen/lösen ords administratör
- Faktureringsadministratör
- Användar administratör
- Administratör för autentisering

### <a name="protecting-all-users"></a>Skydda alla användare

Vi rekommenderar att du tror att administratörs konton är de enda konton som behöver skydd med Multi-Factor Authentication (MFA). Administratörer har bred åtkomst till känslig information och kan göra ändringar i inställningarna för hela prenumerationen. Dåliga aktörer brukar dock vara mål för slutanvändare. När du har fått åtkomst kan de här Felaktiga aktörerna begära åtkomst till privilegie rad information å den ursprungliga konto innehavarens vägnar eller ladda ned hela katalogen för att utföra en nätfiske-attack i hela organisationen. En gemensam metod för att förbättra skyddet för alla användare är att kräva en starkare form av konto verifiering, till exempel Multi Factor Authentication (MFA) för alla. När MFA-registreringen har slutförts uppmanas användarna att ange MFA när det är nödvändigt.

### <a name="blocking-legacy-authentication"></a>Blockerar äldre autentisering

Azure Active Directory (Azure AD) stöder en mängd olika autentiseringsprotokoll, inklusive äldre autentisering, för att ge dina användare enkel åtkomst till dina molnappar. Äldre autentisering är en term som hänvisar till en autentiseringsbegäran som görs av:

- Äldre Office-klienter som inte använder modern autentisering (till exempel Office 2010-klienten)
- Alla klienter som använder äldre e-postprotokoll som IMAP/SMTP/POP3

Idag kommer majoriteten av alla kompromissade inloggnings försök från äldre autentisering. Äldre autentisering stöder inte Multi-Factor Authentication (MFA). Även om du har en MFA-princip aktive rad i din katalog, kan en felaktig aktör autentisera med ett äldre protokoll och kringgå MFA. När säkerhets inställningarna har Aktiver ATS i din klient kommer alla autentiseringsbegäranden som gjorts av ett äldre protokoll att blockeras. Säkerhets standarder blockerar inte Exchange ActiveSync.

### <a name="protecting-privileged-actions"></a>Skydda privilegierade åtgärder

Organisationer använder en mängd olika Azure-tjänster som hanteras via Azure Resource Manager API, inklusive:

- Azure portal 
- Azure PowerShell 
- Azure CLI

Att använda Azure Resource Manager för att hantera dina tjänster är en mycket privilegie rad åtgärd. Azure Resource Manager kan ändra konfigurationer för hela klienten, till exempel tjänst inställningar och fakturering av prenumerationer. Autentisering med en enda faktor är utsatt för en rad olika attacker, t. ex. nätfiske och lösen ords spridning. Därför är det viktigt att verifiera identiteten för användare som vill komma åt Azure Resource Manager och uppdatera konfigurationer genom att kräva Multi-Factor Authentication innan åtkomst tillåts.

När säkerhets inställningarna har Aktiver ATS i din klient, krävs alla användare som har åtkomst till Azure Portal, Azure PowerShell eller Azure CLI för att slutföra Multi-Factor Authentication. Den här principen gäller för alla användare som har åtkomst till Azure Resource Manager, oavsett om de är en administratör eller en användare. Om användaren inte är registrerad för MFA måste användaren registrera sig med hjälp av Microsoft Authenticator-appen för att kunna fortsätta. Ingen 14 dagars MFA-registrerings period kommer att tillhandahållas.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Följande är några ytterligare överväganden som rör distribution av säkerhets inställningar för din klient organisation.

### <a name="legacy-protocols"></a>Äldre protokoll

Bakåtkompatibla autentiseringsprotokoll (IMAP, SMTP, POP3 osv.) används av e-postklienter för att göra autentiseringsbegäranden. Dessa protokoll stöder inte MFA. De flesta konto kompromisser som visas av Microsoft orsakas av felaktiga aktörer som utför attacker mot äldre protokoll som försöker kringgå MFA. För att säkerställa att MFA krävs vid inloggning på ett administrativt konto och felaktiga aktörer inte kan kringgå MFA, blockerar säkerhets standardvärden alla autentiseringsbegäranden som görs till administratörs konton från äldre protokoll.

> [!WARNING]
> Innan du aktiverar den här inställningen bör du kontrol lera att dina administratörer inte använder bakåtkompatibla autentiseringsprotokoll. Mer information finns i artikeln [så här flyttar du bort från äldre autentisering](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Villkorlig åtkomst

Villkorlig åtkomst kan användas för att konfigurera principer som ger samma beteende som säkerhets inställningar. Om du använder villkorlig åtkomst och har principer för villkorlig åtkomst aktive rad i din miljö, är säkerhets inställningarna inte tillgängliga för dig. Om du har en licens som ger villkorlig åtkomst men inte har några principer för villkorlig åtkomst aktive rad i din miljö, är du välkommen att använda säkerhets inställningar tills du aktiverar CA-principer.

![Säkerhets standarder eller villkorlig åtkomst inte båda](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Här följer stegvisa guider om hur villkorlig åtkomst kan användas för att konfigurera likvärdiga principer:

- [Kräv MFA för administratörer](howto-conditional-access-policy-admin-mfa.md)
- [Kräv MFA för Azure-hantering](howto-conditional-access-policy-azure-management.md)
- [Blockera äldre autentisering](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>Aktivera säkerhets inställningar

Så här aktiverar du säkerhets inställningar i din katalog:

1. Logga in på [Azure Portal](https://portal.azure.com) As en säkerhets administratör, en administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till **Azure Active Directory**  @ no__t-2 **Egenskaper**
1. Välj **hantera säkerhets inställningar**
1. Ställ in **Aktivera säkerhets inställningar** växla till **Ja**.
1. Klicka på Spara.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Vad är villkorlig åtkomst?](overview.md)
