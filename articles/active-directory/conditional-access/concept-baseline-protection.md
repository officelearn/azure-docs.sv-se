---
title: Principer för villkorlig åtkomst - Azure Active Directory
description: Principer för grundläggande villkorlig åtkomst för att skydda organisationer från vanliga attacker
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767576"
---
# <a name="what-are-baseline-policies"></a>Vad är baslinjeprinciper?

Baslinjeprinciper är en uppsättning fördefinierade principer som hjälper till att skydda organisationer mot många vanliga attacker. Dessa vanliga attacker kan omfatta lösenordsspray, uppspelning och nätfiske. Baslinjeprinciper är tillgängliga i alla versioner av Azure AD. Microsoft gör dessa grundläggande skyddsprinciper tillgängliga för alla eftersom identitetsbaserade attacker har ökat under de senaste åren. Målet med dessa fyra principer är att se till att alla organisationer har en grundläggande säkerhetsnivå aktiverad utan extra kostnad.

För att hantera anpassade principer för villkorlig åtkomst krävs en Azure AD Premium-licens.

> [!IMPORTANT]
> Baslinjeprinciper är inaktuella. Mer information [finns i Vad är nytt i Azure Active Directory?](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults)

## <a name="baseline-policies"></a>Baslinjeprinciper

![Grundläggande principer för villkorlig åtkomst i Azure-portalen](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Det finns fyra grundläggande principer:

* Kräv MFA för administratörer (förhandsgranskning)
* Skydd för slutanvändare (förhandsgranskning)
* Blockera äldre autentisering (förhandsgranskning)
* Kräv MFA för tjänsthantering (förhandsversion)

Alla fyra dessa principer påverkar äldre autentiseringsflöden som POP, IMAP och äldre Office-skrivbordsklienter.

### <a name="exclusions"></a>Undantag

När baslinjeprinciper gick in i den första offentliga förhandsversionen fanns det ett alternativ för att utesluta användare från principerna. Den här funktionen utvecklades genom förhandsversionen och togs bort i juli 2019. Organisationer som redan hade skapat undantag kunde fortsätta att hålla dem nya användare kunde inte lägga till undantag till principerna.

### <a name="require-mfa-for-admins-preview"></a>Kräv MFA för administratörer (förhandsgranskning)

På grund av den makt och åtkomst som administratörskonton har, bör du behandla dem med särskild omsorg. En vanlig metod för att förbättra skyddet av privilegierade konton är att kräva en starkare form av kontoverifiering när de används för att logga in. I Azure Active Directory kan du få en starkare kontoverifiering genom att kräva att administratörer registrerar sig för och använder Azure Multi-Factor Authentication.

Kräv MFA för administratörer (förhandsversion) är en baslinjeprincip som kräver MFA (Multifaktor authentication) för följande katalogroller, som anses vara de mest privilegierade Azure AD-rollerna:

* Global administratör
* SharePoint-administratör
* Exchange-administratör
* Administratör för villkorlig åtkomst
* Säkerhetsadministratör
* Helpdesk-administratör / Lösenordsadministratör
* Faktureringsadministratör
* Användaradministratör

Om din organisation har dessa konton som används i skript eller kod kan du överväga att ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Skydd för slutanvändare (förhandsgranskning)

Hög privilegierade administratörer är inte de enda som är inriktade på attacker. Dåliga aktörer tenderar att rikta sig till vanliga användare. När dessa dåliga aktörer har fått åtkomst kan de begära åtkomst till privilegierad information för den ursprungliga kontoinnehavarens räkning eller ladda ned hela katalogen och utföra en nätfiskeattack på hela organisationen. En vanlig metod för att förbättra skyddet för alla användare är att kräva en starkare form av kontoverifiering när en riskfylld inloggning upptäcks.

**Skydd för slutanvändare (förhandsversion)** är en baslinjeprincip som skyddar alla användare i en katalog. Om du aktiverar den här principen måste alla användare registrera sig för Azure Multi-Factor-autentisering inom 14 dagar. När användarna har registrerats uppmanas de endast att ange MFA under riskfyllda inloggningsförsök. Komprometterade användarkonton blockeras tills lösenordet återställs och riskerar uppsägning. 

> [!NOTE]
> Alla användare som tidigare flaggats för risk blockeras tills återställning av lösenord och riskerar uppsägning vid principaktivering.

### <a name="block-legacy-authentication-preview"></a>Blockera äldre autentisering (förhandsgranskning)

Äldre autentiseringsprotokoll (t.ex. IMAP, SMTP, POP3) är protokoll som normalt används av äldre e-postklienter för att autentisera. Äldre protokoll stöder inte multifaktorautentisering. Även om du har en princip som kräver multifaktorautentisering för din katalog kan en dålig aktör autentisera med ett av dessa äldre protokoll och kringgå multifaktorautentisering.

Det bästa sättet att skydda ditt konto från begäranden om skadlig autentisering som görs av äldre protokoll är att blockera dem.

Baslinjeprincipen **Blockera äldre autentisering (förhandsversion)** blockerar autentiseringsbegäranden som görs med äldre protokoll. Modern autentisering måste användas för att logga in för alla användare. Används tillsammans med andra baslinjeprinciper, begäranden som kommer från äldre protokoll kommer att blockeras. Dessutom kommer alla användare att vara skyldiga att MFA när det behövs. Den här principen blockerar inte Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Kräv MFA för tjänsthantering (förhandsversion)

Organisationer använder en mängd olika Azure-tjänster och hanterar dem från Azure Resource Manager-baserade verktyg som:

* Azure Portal
* Azure PowerShell
* Azure CLI

Att använda något av dessa verktyg för att utföra resurshantering är en mycket privilegierad åtgärd. Dessa verktyg kan ändra konfigurationer för hela prenumerationer, till exempel tjänstinställningar och prenumerationsfakturering.

För att skydda privilegierade åtgärder kräver den här principen **kräver MFA för tjänsthantering (förhandsversion)** multifaktorautentisering för alla användare som använder Azure-portalen, Azure PowerShell eller Azure CLI.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Aktivera standardvärden för säkerhet](../fundamentals/concept-fundamentals-security-defaults.md)
* [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
* [Fem steg för att skydda din identitetsinfrastruktur](../../security/fundamentals/steps-secure-identity.md)
