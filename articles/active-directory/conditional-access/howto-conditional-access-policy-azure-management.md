---
title: Villkorlig åtkomst – Kräv MFA för Azure-hantering – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att kräva multifaktorautentisering för Azure-hanteringsuppgifter
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1dd2da51cb33582c90ff592e0061b5c1ebf8ee1
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631828"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Villkorlig åtkomst: Kräv MFA för Azure-hantering

Organisationer använder en mängd olika Azure-tjänster och hanterar dem från Azure Resource Manager-baserade verktyg som:

* Azure Portal
* Azure PowerShell
* Azure CLI

De här verktygen kan ge mycket privilegierad åtkomst till resurser som kan ändra konfigurationer för hela prenumerationer, tjänstinställningar och prenumerationsfakturering. För att skydda dessa privilegierade resurser rekommenderar Microsoft att du kräver multifaktorautentisering för alla användare som använder dessa resurser.

## <a name="user-exclusions"></a>Undantag från användare

Principer för villkorlig åtkomst är kraftfulla verktyg, vi rekommenderar att du utesluter följande konton från din policy:

* **Nödåtkomst** eller **break-glass-konton** för att förhindra kontoutelåsning för hela klienten. I det osannolika scenariot är alla administratörer utelåsta från din klientorganisation, ditt administrativa konto för nödåtkomst kan användas för att logga in på klienten vidta åtgärder för att återställa åtkomsten.
   * Mer information finns i [artikeln, Hantera konton för nödåtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Tjänstkonton** och **tjänsthuvudnamn**, till exempel Azure AD Connect Sync-konto. Tjänstkonton är icke-interaktiva konton som inte är kopplade till en viss användare. De används normalt av backend-tjänster som möjliggör programmatisk åtkomst till program, men används också för att logga in på system för administrativa ändamål. Tjänstkonton som dessa bör uteslutas eftersom MFA inte kan slutföras programmässigt.
   * Om din organisation har dessa konton som används i skript eller kod kan du överväga att ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan du utesluta dessa specifika konton från originalprincipen.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper till att skapa en princip för villkorlig åtkomst för att kräva att de tilldelade administrativa rollerna utför multifaktorautentisering.

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Alla användare**.
   1. Under **Uteslut**väljer du **Användare och grupper** och väljer organisationens konton för nödåtkomst eller glasbrott. 
   1. Välj **Done** (Klar).
1. Under **Molnappar eller åtgärder** > **Inkludera**väljer du Välj **appar**, väljer Microsoft **Azure Management**och väljer **Välj** sedan **Klar**.
1. Under **Villkor** > **klientappar (förhandsversion)** ställer du in **Konfigurera** till **Ja**och väljer **Klar**.
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**, **Kräv multifaktorautentisering**och välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** för att skapa för att aktivera din princip.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)
