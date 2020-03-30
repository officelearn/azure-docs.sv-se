---
title: Ändra licensplaner för användare och grupper – Azure AD | Microsoft-dokument
description: Migrera användare inom en grupp till olika tjänstplaner med grupplicensiering i Azure Active Directory
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025895"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Ändra licenstilldelningar för en användare eller grupp i Azure Active Directory

I den här artikeln beskrivs hur du flyttar användare och grupper mellan tjänstlicensplaner i Azure Active Directory (Azure AD). Målet Azure AD: s metod är att se till att det inte finns någon förlust av tjänst eller data under licensändringen. Användare bör växla mellan tjänster sömlöst. I tilldelningsstegen för licensplaner i den här artikeln beskrivs hur du ändrar en användare eller grupp på Office 365 E1 till Office 365 E3, men stegen gäller för alla licensplaner. När du uppdaterar licenstilldelningar för en användare eller grupp görs borttagningar av licenstilldelningar och nya tilldelningar samtidigt så att användarna inte förlorar åtkomsten till sina tjänster under licensändringar eller ser licenskonflikter mellan planer.

## <a name="before-you-begin"></a>Innan du börjar

Innan du uppdaterar licenstilldelningarna är det viktigt att kontrollera att vissa antaganden är sanna för att alla användare eller grupper ska uppdateras. Om antagandena inte är sanna för alla användare i en grupp kan migreringen misslyckas för vissa. Som ett resultat kan vissa användare förlora åtkomst till tjänster eller data. Se till att:

- Användarna har det aktuella licensabonnemanget (i det här fallet Office 365 E1) som har tilldelats en grupp och ärvts av användaren och inte tilldelas direkt.

- Du har tillräckligt med tillgängliga licenser för licensplanen som du tilldelar. Om du inte har tillräckligt med licenser kanske vissa användare inte har tilldelats den nya licensplanen. Du kan kontrollera antalet tillgängliga licenser.

- Användare har inte andra tilldelade tjänstlicenser som kan stå i konflikt med den önskade licensen eller förhindra borttagning av den aktuella licensen. Till exempel en licens från en tjänst som Workplace Analytics eller Project Online som är beroende av andra tjänster.

- Om du hanterar grupper lokalt och synkroniserar dem till Azure AD via Azure AD Connect lägger du till eller tar bort användare med hjälp av ditt lokala system. Det kan ta lite tid innan ändringarna synkroniseras med Azure AD som ska hämtas av grupplicensiering.

- Om du använder dynamiska azure AD-gruppmedlemskap lägger du till eller tar bort användare genom att ändra deras attribut, men uppdateringsprocessen för licenstilldelningar förblir densamma.

## <a name="change-user-license-assignments"></a>Ändra tilldelningar av användarlicenser

Om du ser att vissa kryssrutor inte är tillgängliga på sidan **Uppdatera licenstilldelningar** anger den tjänster som inte kan ändras eftersom de ärvs från en grupplicens.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett licensadministratörskonto i din Azure AD-organisation.
1. Välj Azure Active**Directory-användare** **Azure Active Directory** > och öppna sedan **profilsidan** för en användare.
1. Välj **Licenser**.
1. Välj **Tilldelningar** om du vill redigera licenstilldelning för användaren eller gruppen. På sidan **Uppgifter** kan du lösa licenstilldelningskonflikter.
1. Markera kryssrutan för Office 366 E3 och se till att minst alla E1-tjänster som tilldelats användaren är markerade.
1. Avmarkera kryssrutan för Office 365 E1.

    ![sidan licenstilldelningar för en användare som visar Office 365 E1 rensad och Office 365 E3 markerat](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Välj **Spara**.

Azure AD tillämpar de nya licenserna och tar bort de gamla licenserna samtidigt för att tillhandahålla tjänstkontinuitet.

## <a name="change-group-license-assignments"></a>Ändra grupplicenstilldelningar

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett licensadministratörskonto i din Azure AD-organisation.
1. Välj **Azure Active Directory** > **Groups**och öppna sedan sidan **Översikt** för en grupp.
1. Välj **Licenser**.
1. Välj kommandot **Tilldelningar** om du vill redigera licenstilldelning för användaren eller gruppen.
1. Markera kryssrutan för Office 366 E3. För att upprätthålla kontinuiteten i tjänsten, se till att du väljer alla E1-tjänster som redan har tilldelats användaren.
1. Avmarkera kryssrutan för Office 365 E1.

    ![Välj kommandot Tilldelningar på en sida med användar- eller grupplicenser](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Välj **Spara**.

För att tillhandahålla tjänstkontinuitet tillämpar Azure AD de nya licenserna och tar bort de gamla licenserna samtidigt för alla användare i gruppen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra scenarier för licenshantering via grupper i följande artiklar:

- [Tilldela licenser till en grupp i Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
- [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Migrera enskilda licensierade användare till grupplicensiering i Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
- [Azure Active Directory-grupp licensiering ytterligare scenarier](../users-groups-roles/licensing-group-advanced.md)
- [PowerShell-exempel för grupplicensiering i Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
