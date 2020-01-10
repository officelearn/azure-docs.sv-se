---
title: Bas linje principer för villkorlig åtkomst – Azure Active Directory
description: Grundläggande principer för villkorlig åtkomst för att skydda organisationer från vanliga attacker
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767576"
---
# <a name="what-are-baseline-policies"></a>Vad är bas linje principer?

Bas linje principer är en uppsättning fördefinierade principer som hjälper till att skydda organisationer mot många vanliga attacker. Dessa vanliga attacker kan omfatta sprayning av lösen ord, uppspelning och nätfiske. Bas linje principer finns i alla utgåvor av Azure AD. Microsoft gör de här bas linje skydds principerna tillgängliga för alla eftersom identitetsbaserade attacker har varit i ökningen under de senaste åren. Målet med dessa fyra principer är att se till att alla organisationer har en grundläggande säkerhets nivå som är aktive rad utan extra kostnad.

Att hantera anpassade principer för villkorlig åtkomst kräver en Azure AD Premium-licens.

> [!IMPORTANT]
> Bas linje principer är inaktuella. Se [Vad är nytt i Azure Active Directory?](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults) för mer information.

## <a name="baseline-policies"></a>Baslinjeprinciper

![Bas linje principer för villkorlig åtkomst i Azure Portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Det finns fyra bas linje principer:

* Kräv MFA för administratörer (för hands version)
* Slut användar skydd (för hands version)
* Blockera äldre autentisering (för hands version)
* Kräv MFA för Service Management (för hands version)

Alla fyra av dessa principer påverkar äldre autentiserings flöden som POP, IMAP och äldre Office-klienter.

### <a name="exclusions"></a>Undantag

När bas linje principerna gick in i den första offentliga för hands versionen finns det ett alternativ för att utesluta användare från principerna. Den här funktionen har utvecklats genom för hands versionen och togs bort i juli 2019. Organisationer som redan har skapat undantag kunde fortsätta att behålla dem nya användare kunde inte lägga till undantag i principerna.

### <a name="require-mfa-for-admins-preview"></a>Kräv MFA för administratörer (för hands version)

På grund av den kraft och åtkomst som administratörs kontot har måste du behandla dem med särskild omsorg. En gemensam metod för att förbättra skyddet av privilegierade konton är att kräva en starkare form av konto verifiering när de används för att logga in. I Azure Active Directory kan du få en bättre konto verifiering genom att kräva att administratörer registrerar sig för och använder Azure Multi-Factor Authentication.

Kräv MFA för administratörer (för hands version) är en bas linje princip som kräver Multi-Factor Authentication (MFA) för följande katalog roller, som anses vara de mest privilegierade Azure AD-rollerna:

* Global administratör
* SharePoint-administratör
* Exchange-administratör
* Administratör för villkorlig åtkomst
* Säkerhetsadministratör
* Administratör för supportavdelningen/lösen ords administratör
* Faktureringsadministratör
* Användar administratör

Om din organisation har dessa konton som används i skript eller kod kan du ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Slut användar skydd (för hands version)

Hög privilegierade administratörer är inte de enda som är riktade mot angrepp. Dåliga aktörer brukar vara mål för normala användare. När du har fått åtkomst kan de här Felaktiga aktörerna begära åtkomst till privilegie rad information å den ursprungliga konto innehavarens vägnar eller ladda ned hela katalogen och utföra ett phishing-angrepp i hela organisationen. En gemensam metod för att förbättra skyddet för alla användare är att kräva en starkare form av konto verifiering när en riskfylld inloggning upptäcks.

**End User Protection (för hands version)** är en bas linje princip som skyddar alla användare i en katalog. Att aktivera den här principen kräver att alla användare registrerar sig för Azure Multi-Factor Authentication inom 14 dagar. När de har registrerats uppmanas användarna bara att använda MFA under riskfyllda inloggnings försök. Komprometterade användar konton blockeras tills lösen ordet återställs och risken stängs. 

> [!NOTE]
> Alla användare som tidigare har flaggats för risk blockeras tills lösen ords återställning och risk inaktive ras vid princip aktivering.

### <a name="block-legacy-authentication-preview"></a>Blockera äldre autentisering (för hands version)

Bakåtkompatibla autentiseringsprotokoll (t. ex. IMAP, SMTP, POP3) är protokoll som normalt används av äldre e-postklienter för att autentisera sig. Äldre protokoll stöder inte Multi-Factor Authentication. Även om du har en princip som kräver Multi-Factor Authentication för din katalog kan en felaktig aktör autentisera med hjälp av ett av dessa äldre protokoll och kringgå Multi-Factor Authentication.

Det bästa sättet att skydda ditt konto från skadliga autentiseringsbegäranden som görs av äldre protokoll är att blockera dem.

Bas linje principen **blockera äldre autentisering (förhands granskning)** blockerar autentiseringsbegäranden som görs med hjälp av äldre protokoll. Modern autentisering måste användas för att kunna logga in för alla användare. Som används tillsammans med andra bas linje principer kommer begär Anden som kommer från äldre protokoll att blockeras. Dessutom måste alla användare ha MFA när det behövs. Den här principen blockerar inte Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Kräv MFA för Service Management (för hands version)

Organisationer använder en mängd olika Azure-tjänster och hanterar dem från Azure Resource Manager baserade verktyg som:

* Azure portal
* Azure PowerShell
* Azure CLI

Att använda något av dessa verktyg för att utföra resurs hantering är en hög privilegie rad åtgärd. Dessa verktyg kan ändra konfigurationer för hela prenumerationen, till exempel tjänst inställningar och fakturering av prenumerationer.

För att skydda privilegierade åtgärder kräver detta att multifaktorautentisering **för Service Management (för hands version)** -principen kräver multifaktorautentisering för alla användare som har åtkomst till Azure Portal, Azure PowerShell eller Azure CLI.

## <a name="next-steps"></a>Nästa steg

Mer information finns här:

* [Aktivera säkerhets inställningar](../fundamentals/concept-fundamentals-security-defaults.md)
* [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
* [Fem steg för att skydda er identitetsinfrastruktur](../../security/fundamentals/steps-secure-identity.md)
