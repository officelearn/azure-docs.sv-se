---
title: Använda Min personal för att delegera användarhantering (förhandsversion) – Azure AD | Microsoft-dokument
description: Delegera användarhantering med min personal och administrativa enheter
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394220"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Hantera användarna med Min personal (förhandsgranskning)

Med min personal kan du delegera till en auktoritet, till exempel en butikschef eller en gruppledning, behörigheterna för att säkerställa att deras anställda kan komma åt sina Azure AD-konton. I stället för att förlita sig på en central helpdesk kan organisationer delegera vanliga uppgifter som att återställa lösenord eller ändra telefonnummer till en teammanager. Med Min personal kan en användare som inte kan komma åt sitt konto få åtkomst med bara ett par klick, utan att någon helpdesk eller IT-personal krävs.

Innan du konfigurerar Min personal för din organisation rekommenderar vi att du granskar den här dokumentationen samt [användardokumentationen](../user-help/my-staff-team-manager.md) för att se till att du förstår funktionens funktioner och inverkan på användarna. Du kan använda användardokumentationen för att träna och förbereda användarna för den nya upplevelsen och bidra till att säkerställa en lyckad distribution.

## <a name="how-my-staff-works"></a>Så här fungerar min personal

Min personal baseras på administrativa enheter som är en behållare med resurser som kan användas för att begränsa omfattningen av ett rolltilldelnings administrativa kontroll. I Min personal används AUs för att definiera en delmängd av en organisations användare, till exempel ett lager eller en avdelning. Sedan kan till exempel en gruppchef tilldelas en roll vars omfattning är en eller flera AUs. I exemplet nedan har användaren tilldelats rollen Autentiseringsarministrativa och de tre AUs är omfattningen av rollen. Mer information om administrativa enheter finns [i Hantering av administrativa enheter i Azure Active Directory](directory-administrative-units.md).

## <a name="how-to-enable-my-staff"></a>Så här aktiverar du Min personal

När du har konfigurerat AUs kan du använda det här scopet på användarna som har åtkomst till Min personal. Endast användare som har tilldelats en administrativ roll kan komma åt Min personal. Så här aktiverar du Min personal:

1. Logga in på Azure-portalen som användaradministratör.
2. Bläddra till **Azure Active Directory** > **Användarinställningar** > **Användarfunktionsförhandsvisningar** > **Hantera förhandsgranskningsinställningar för användarfunktionen**.
3. Under **Administratörer kan komma åt Min personal**kan du välja att aktivera för alla användare, valda användare eller ingen användaråtkomst.

> [!Note]
> Endast användare som har tilldelats en administratörsroll kan komma åt Min personal. Om du aktiverar Min personal för en användare som inte har tilldelats en administratörsroll kan de inte komma åt Min personal.

## <a name="using-my-staff"></a>Använda min personal

När en användare går till Min personal visas namnen på de [administrativa enheter](directory-administrative-units.md) som de har administratörsbehörighet över. I [användardokumentationen För Min personal](../user-help/my-staff-team-manager.md)använder vi termen "plats" för att referera till administrativa enheter. Om en administratörs behörigheter inte har au-scope gäller behörigheterna i hela organisationen. När Min personal har aktiverats kan de användare som är aktiverade och [https://mystaff.microsoft.com](https://mystaff.microsoft.com)har tilldelats en administrativ roll komma åt den via . De kan välja en AU för att visa användarna i au:et och välja en användare för att öppna sin profil.

## <a name="licenses"></a>Licenser

Varje användare som är aktiverad i Min personal måste vara licensierad, även om de inte använder portalen Min personal. Varje aktiverad användare måste ha någon av följande Azure AD- eller Microsoft 365-licenser:

- Azure AD Premium P1 eller P2
- Microsoft 365 F1 eller F3

## <a name="reset-a-users-password"></a>Återställa ett användarlösenord

Följande roller har behörighet att återställa en användares lösenord:

- [Administratör för autentisering](directory-assign-admin-roles.md#authentication-administrator)
- [Privilegierad autentiseringsadministratör](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Global administratör](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Helpdesk-administratör](directory-assign-admin-roles.md#helpdesk-administrator)
- [Användaradministratör](directory-assign-admin-roles.md#user-administrator)
- [Lösenordsadministratör](directory-assign-admin-roles.md#password-administrator)

Öppna en användares profil från **Min personal.** Välj **Återställ lösenord**.

- Om användaren endast är molnfri kan du se ett tillfälligt lösenord som du kan ge till användaren.
- Om användaren synkroniseras från den lokala Active Directory kan du ange ett lösenord som uppfyller dina lokala AD-principer. Du kan sedan ge det lösenordet till användaren.

    ![Statusindikator för återställning av lösenord och meddelande om lyckad gång](media/my-staff-configure/reset-password.png)

Användaren måste ändra sitt lösenord nästa gång de loggar in.

## <a name="manage-a-phone-number"></a>Hantera ett telefonnummer

Öppna en användares profil från **Min personal.**

- Välj **Lägg till telefonnummeravsnitt** för att lägga till ett telefonnummer för användaren
- Välj **Redigera telefonnummer** om du vill ändra telefonnumret
- Välj **Ta bort telefonnummer** för att ta bort telefonnumret för användaren

Beroende på dina inställningar kan användaren sedan använda det telefonnummer som du ställer in för att logga in med SMS, utföra multifaktorautentisering och utföra återställning av lösenord för självbetjäning.

Om du vill hantera en användares telefonnummer måste du tilldelas en av följande roller:

- [Administratör för autentisering](directory-assign-admin-roles.md#authentication-administrator)
- [Privilegierad autentiseringsadministratör](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Global administratör](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Search

Du kan söka efter AUs och användare i organisationen med hjälp av sökfältet i Min personal. Du kan söka bland alla ru:er och användare i organisationen, men du kan bara göra ändringar för användare som befinner sig i en AU som du har fått administratörsbehörighet över.

Du kan också söka efter en användare inom en AU. Det gör du genom att använda sökfältet högst upp i användarlistan.

## <a name="audit-logs"></a>Granskningsloggar

Du kan visa granskningsloggar för åtgärder som vidtas i Min personal i Azure Active Directory-portalen. Om en granskningslogg genererades av en åtgärd som vidtagits i Min personal visas detta under YTTERLIGARE INFORMATION i granskningshändelsen.

## <a name="next-steps"></a>Nästa steg

[Dokumentation av min personalanvändare](../user-help/my-staff-team-manager.md)
[Administrativa enheter](directory-administrative-units.md)
