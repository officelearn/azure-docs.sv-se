---
title: Villkorlig åtkomst – Kräv MFA för alla användare – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst så att alla användare måste utföra Multi-Factor Authentication
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0d22e2e6478c265ba9219ae4df5d5ddb34d481
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803894"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Villkorlig åtkomst: Kräv MFA för alla användare

Som Alex-Weinert nämns katalogen med identitets säkerhet på Microsoft, som nämns i blogg inlägget [din pa $ $Word spelar ingen roll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Ditt lösen ord spelar ingen roll, men MFA gör! Utifrån våra studier är ditt konto mer än 99,9% mindre troligt att bli komprometterat om du använder MFA.

Vägledningen i den här artikeln hjälper din organisation att skapa en balanserad MFA-princip för din miljö.

## <a name="user-exclusions"></a>Användar undantag

Principer för villkorlig åtkomst är kraftfulla verktyg, vi rekommenderar att du undantar följande konton från principen:

* **Nöd åtkomst** eller **Bryt** punkts konton för att förhindra konto utelåsning för hela klienten. I det osannolika scenariot är alla administratörer utelåsta från din klient, ditt administrations konto för nöd administration kan användas för att logga in på klienten och vidta åtgärder för att återställa åtkomsten.
   * Mer information finns i artikeln [Hantera nöd åtkomst konton i Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Tjänst konton** och **tjänst principer**, till exempel Azure AD Connect Sync-kontot. Tjänst konton är icke-interaktiva konton som inte är kopplade till någon viss användare. De används vanligt vis av backend-tjänster och ger program mässig åtkomst till program. Tjänst konton ska undantas eftersom MFA inte kan slutföras program mässigt.
   * Om din organisation har dessa konton som används i skript eller kod kan du ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan du undanta dessa konton från bas linje principen.

## <a name="application-exclusions"></a>Program undantag

Organisationer kan ha många moln program som används. Inte alla dessa program kan kräva lika säkerhet. Löne-och närvaro programmen kan till exempel kräva MFA, men cafeteria är förmodligen inte det. Administratörer kan välja att undanta vissa program från sin princip.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst som kräver att de tilldelade administrativa rollerna utför Multi-Factor Authentication.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory** > **säkerhet** > **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare**
   1. Under **exkludera**väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Done** (Klar).
1. Under **molnappar eller åtgärder** > **inkluderar**väljer du **alla molnappar**.
   1. Under **exkludera**väljer du alla program som inte kräver Multi-Factor Authentication.
1. Under **åtkomst kontroller** > **bevilja**, väljer du **bevilja åtkomst**, **kräver Multi-Factor Authentication**och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
