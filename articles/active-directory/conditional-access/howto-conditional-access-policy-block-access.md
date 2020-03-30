---
title: Villkorlig åtkomst - Blockera åtkomst - Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst till
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbf4f352bc7d6874cab9c12adf5f1717d4b8f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295733"
---
# <a name="conditional-access-block-access"></a>Villkorlig åtkomst: Blockera åtkomst

För organisationer med en konservativ molnmigreringsmetod är block alla principen ett alternativ som kan användas. 

> [!CAUTION]
> Felkonfiguration av en blockeringsprincip kan leda till att organisationer utestängs från Azure-portalen.

Politik som dessa kan ha oavsiktliga biverkningar. Korrekt testning och validering är avgörande innan du aktiverar. Administratörer bör använda verktyg som [rapportläge med villkorsstyrd åtkomst](concept-conditional-access-report-only.md) och [verktyget Vad händer i villkorlig åtkomst](what-if-tool.md) när du gör ändringar.

## <a name="user-exclusions"></a>Undantag från användare

Principer för villkorlig åtkomst är kraftfulla verktyg, vi rekommenderar att du utesluter följande konton från din policy:

* **Nödåtkomst** eller **break-glass-konton** för att förhindra kontoutelåsning för hela klienten. I det osannolika scenariot är alla administratörer utelåsta från din klientorganisation, ditt administrativa konto för nödåtkomst kan användas för att logga in på klienten vidta åtgärder för att återställa åtkomsten.
   * Mer information finns i [artikeln, Hantera konton för nödåtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Tjänstkonton** och **tjänsthuvudnamn**, till exempel Azure AD Connect Sync-konto. Tjänstkonton är icke-interaktiva konton som inte är kopplade till en viss användare. De används normalt av backend-tjänster och ger programmatisk åtkomst till program. Tjänstkonton bör uteslutas eftersom MFA inte kan slutföras programmässigt.
   * Om din organisation har dessa konton som används i skript eller kod kan du överväga att ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan du utesluta dessa specifika konton från originalprincipen.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa principer för villkorlig åtkomst för att blockera åtkomst till alla appar utom [Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) om användarna inte finns i ett betrott nätverk. Dessa principer sätts i [läge Endast för rapport](howto-conditional-access-report-only.md) för att starta så att administratörer kan avgöra vilken inverkan de kommer att ha på befintliga användare. När administratörer är bekväma med att principerna gäller som de vill kan de växla dem till **På**.

Den första principen blockerar åtkomst till alla appar utom Office 365-program om de inte finns på en betrodd plats.

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar** väljer du **Användare och grupper**.
   1. Under **Inkludera**väljer du **Alla användare**.
   1. Under **Uteslut**väljer du **Användare och grupper** och väljer organisationens konton för nödåtkomst eller glasbrott. 
   1. Välj **Done** (Klar).
1. Under **Cloud-appar eller -åtgärder**väljer du följande alternativ:
   1. Under **Inkludera**väljer du **Alla molnappar**.
   1. Under **Uteslut**väljer du **Office 365 (förhandsgranskning)** och väljer **Välj**och välj sedan **Klar**.
1. Under **förhållanden:**
   1. Under **förhållanden** > **plats**.
      1. Ange **Konfigurera** till **Ja**
      1. Under **Inkludera**väljer du **Valfri plats**.
      1. Under **Uteslut**väljer du **Alla betrodda platser**.
      1. Välj **Done** (Klar).
   1. Under **Klientappar (förhandsversion)** anger du **Konfigurera** till **Ja**och väljer **Klar**och sedan **Klar**.
1. Under **Access-kontroller** > **Bevilja**väljer du Blockera **åtkomst**och väljer sedan **Välj**.
1. Bekräfta dina inställningar och ange **Aktivera princip** till **endast rapport**.
1. Välj **Skapa** för att skapa för att aktivera din princip.

En andra princip skapas nedan för att kräva multifaktorautentisering eller en kompatibel enhet för användare av Office 365.

1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar** väljer du **Användare och grupper**.
   1. Under **Inkludera**väljer du **Alla användare**.
   1. Under **Uteslut**väljer du **Användare och grupper** och väljer organisationens konton för nödåtkomst eller glasbrott. 
   1. Välj **Done** (Klar).
1. Under **Cloud-appar eller -åtgärder** > **Inkludera**väljer du Välj **appar**, väljer Office **365 (förhandsgranskning)** och väljer **Välj**och sedan **Klar**.
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Bevilja **åtkomst**.
   1. Välj **Kräv multifaktorautentisering** och **Kräv att enheten ska markeras som kompatibel** väljer **.**
   1. Kontrollera **att kräv att alla markerade kontroller** är markerade.
   1. Välj **Välj**.
1. Bekräfta dina inställningar och ange **Aktivera princip** till **endast rapport**.
1. Välj **Skapa** för att skapa för att aktivera din princip.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)
