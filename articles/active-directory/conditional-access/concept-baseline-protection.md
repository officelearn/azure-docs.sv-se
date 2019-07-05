---
title: Principer för villkorlig åtkomst-skydd för baslinje - Azure Active Directory
description: Baslinjen villkorliga åtkomstprinciper för att skydda organisationer mot vanliga attacker
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
ms.openlocfilehash: 970fdaba1870097e253b51c70e523e399bc88dfc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440779"
---
# <a name="what-are-baseline-policies"></a>Vad är principer för baslinjen?

Grundläggande principer är en uppsättning fördefinierade principer som skyddar organisationer mot många vanliga attacker. Dessa vanliga attacker kan inbegripa lösenord besprutningsmedel repetitionsattacker och nätfiske. Grundläggande principer är tillgängliga i alla utgåvor av Azure AD. Microsoft gör dessa principer för skydd av baslinjen tillgängliga för alla eftersom identitetsbaserade attacker har gått ökar under de senaste åren. Målet med dessa fyra principer är att säkerställa att alla organisationer har en baslinje säkerhetsnivå aktiverat utan extra kostnad.  

Hantera anpassade principer för villkorlig åtkomst kräver en Azure AD Premium-licens.

## <a name="baseline-policies"></a>Baslinjeprinciper

![Grundläggande principer för villkorlig åtkomst i Azure portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Det finns fyra grundläggande principer som organisationer kan aktivera:

* [Kräva MFA för administratörer (förhandsversion)](howto-baseline-protect-administrators.md)
* [Slutanvändaren protection (förhandsgranskningsversion)](howto-baseline-protect-end-users.md)
* [Blockera äldre authentication (förhandsversion)](howto-baseline-protect-legacy-auth.md)
* [Kräva MFA för tjänsthantering (förhandsversion)](howto-baseline-protect-azure.md)

Alla fyra av dessa principer påverkar äldre autentiseringsflöden som POP, IMAP och äldre stationära Office-klienter.

### <a name="require-mfa-for-admins-preview"></a>Kräva MFA för administratörer (förhandsversion)

På grund av den kraft och åtkomst-administratörskonton har, bör du hantera dem med särskild försiktighet. En vanlig metod för att förbättra skyddet av Privilegierade konton är att kräva en starkare form av verifiering för kontot när de används för att logga in. Du kan få en starkare Kontoverifiering genom att kräva att administratörer kan registrera sig för och använder Azure Multi-Factor Authentication i Azure Active Directory.

[Kräva MFA för administratörer (förhandsversion)](howto-baseline-protect-administrators.md) är en baslinjeprincip som kräver multifaktorautentisering (MFA) för följande katalogroller, anses vara de mest Privilegierade rollerna för Azure AD:

* Global administratör
* SharePoint-administratör
* Exchange-administratör
* Administratör för villkorlig åtkomst
* Säkerhetsadministratör
* Supportavdelningsadministratören / lösenordsadministratör
* Faktureringsadministratör
* Användaradministratör

Om din organisation har dessa konton som används i skript eller kod kan du överväga att ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Slutanvändaren protection (förhandsgranskningsversion)

Hög Privilegierade administratörer inte är de enda som riktas mot attacker. Illvilliga aktörer brukar för normal användare. När du har fått åtkomst kan dessa illvilliga aktörer begär åtkomst till hemlig information åt den ursprungliga kontoinnehavaren eller ladda ned hela katalogen och utföra en nätfiske attack på hela organisationen. En vanlig metod för att förbättra skyddet för alla användare är att kräva en starkare form av verifiering för kontot när en riskfylld inloggning har identifierats.

**Slutanvändaren protection (förhandsgranskningsversion)** är en baslinjeprincip som skyddar alla användare i en katalog. När den här principen kräver att alla användare att registrera dig för Azure Multi-Factor Authentication inom 14 dagar. När registrerad, uppmanas användare för MFA endast under riskfyllda inloggningsförsök. Komprometterade användarkonton blockeras tills lösenordet återställa och riskerar avsked.

### <a name="block-legacy-authentication-preview"></a>Blockera äldre authentication (förhandsversion)

Äldre autentiseringsprotokoll (t.ex.: IMAP, SMTP-, POP3) är protokoll som normalt används av äldre e-postklienter för att autentisera. Äldre protokoll har inte stöd för multifaktorautentisering. Även om du har en princip för att kräva multifaktorautentisering för din katalog kan en obehörig autentisera med någon av dessa äldre protokoll och koppla förbi Multi-Factor authentication.

Det bästa sättet att skydda ditt konto från skadliga autentiseringsbegäranden av äldre protokoll är att blockera dem.

Den **blockera äldre authentication (förhandsversion)** baslinjeprincip blockerar autentiseringsförfrågningar som görs med hjälp av äldre protokoll. Modern autentisering måste användas för att logga in för alla användare. Används tillsammans med andra baslinje-principer, kommer begäranden som kommer från äldre protokoll att blockeras. Dessutom kommer alla användare att behöva MFA varje gång som krävs. Den här principen blockerar inte Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Kräva MFA för tjänsthantering (förhandsversion)

Organisationer använder flera Azure-tjänster och hantera dem från Azure Resource Manager-baserade verktyg som:

* Azure Portal
* Azure PowerShell
* Azure CLI

Använda någon av dessa verktyg för resurshantering är en mycket Privilegierade åtgärd. Dessa verktyg kan ändra prenumerationen hela konfigurationer, till exempel prenumerationsärenden och inställningar.

Att skydda Privilegierade åtgärder detta **kräver MFA för tjänsthantering (förhandsversion)** princip kräver multifaktorautentisering för alla användare åtkomst till Azure portal, Azure PowerShell eller Azure CLI.

## <a name="enable-a-baseline-policy"></a>Aktivera en baslinjeprincip

För att aktivera en baslinjeprincip:

1. Logga in på den **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. I listan med principer, väljer du en baslinjeprincip som du vill aktivera.
1. Ange **aktiverar principen** till **på**.
1. Klicka på Spara.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Fem steg för att skydda din infrastruktur för Identitetshantering](../../security/azure-ad-secure-steps.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)
* [Kräva MFA för administratörer (förhandsversion)](howto-baseline-protect-administrators.md)
* [Slutanvändaren protection (förhandsgranskningsversion)](howto-baseline-protect-end-users.md)
* [Blockera äldre authentication (förhandsversion)](howto-baseline-protect-legacy-auth.md)
* [Kräva MFA för tjänsthantering (förhandsversion)](howto-baseline-protect-azure.md)
