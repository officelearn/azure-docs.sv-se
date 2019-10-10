---
title: Villkorlig åtkomst – Kräv MFA för Azure-hantering – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att kräva Multi-Factor Authentication för Azure-hanterings uppgifter
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd2259dc715fb54122b721ce40a715c6987947d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170102"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Villkorlig åtkomst: Kräv MFA för Azure-hantering

Organisationer använder en mängd olika Azure-tjänster och hanterar dem från Azure Resource Manager baserade verktyg som:

* Azure portal
* Azure PowerShell
* Azure CLI

Dessa verktyg kan ge hög privilegie rad åtkomst till resurser, som kan ändra konfigurationer för hela konfigurationen, tjänst inställningar och fakturering av prenumerationer. För att skydda dessa privilegierade resurser rekommenderar Microsoft att du kräver Multi-Factor Authentication för alla användare som har åtkomst till dessa resurser.

## <a name="user-exclusions"></a>Användar undantag

Principer för villkorlig åtkomst är kraftfulla verktyg, vi rekommenderar att du undantar följande konton från principen:

* **Nöd åtkomst** eller **Bryt** punkts konton för att förhindra konto utelåsning för hela klienten. I det osannolika scenariot är alla administratörer utelåsta från din klient, ditt administrations konto för nöd administration kan användas för att logga in på klienten och vidta åtgärder för att återställa åtkomsten.
   * Mer information finns i artikeln [Hantera nöd åtkomst konton i Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Tjänst konton** och **tjänst principer**, till exempel Azure AD Connect Sync-kontot. Tjänst konton är icke-interaktiva konton som inte är kopplade till någon viss användare. De används vanligt vis av backend-tjänster och ger program mässig åtkomst till program. Tjänst konton ska undantas eftersom MFA inte kan slutföras program mässigt.
   * Om din organisation har dessa konton som används i skript eller kod kan du ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan du undanta dessa konton från bas linje principen.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst som kräver att de tilldelade administrativa rollerna utför Multi-Factor Authentication.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare**.
   1. Under **exkludera**väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Done** (Klar).
1. Under **molnappar eller åtgärder** > **inkluderar**väljer du **Välj appar**, väljer **Microsoft Azure hantering**och väljer sedan **Välj** och sedan **Slutför**.
1. Under **åtkomst kontroller** > -**beviljande**väljer du **bevilja åtkomst**, **kräver Multi-Factor Authentication**och välj **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
