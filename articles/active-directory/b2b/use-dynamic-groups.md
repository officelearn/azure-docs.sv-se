---
title: Dynamiska grupper och B2B - samarbete i Azure Active Directory | Microsoft Docs
description: Visar hur du använder dynamiska grupper i Azure AD med Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17f701eb9ad3c083712ddf9e774e6ddbefbe91fd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65227961"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamiska grupper och Azure Active Directory B2B-samarbete

## <a name="what-are-dynamic-groups"></a>Vad är dynamiska grupper?
Dynamisk konfiguration av medlemskap i säkerhetsgrupper för Azure Active Directory (Azure AD) är tillgänglig i [Azure-portalen](https://portal.azure.com). Administratörer kan ange regler för att fylla i grupper som har skapats i Azure AD baserat på användarattribut (till exempel userType, avdelning eller land/region). Medlemmar kan automatiskt läggas till eller tas bort från en grupp baserat på deras attribut. Dessa grupper kan ge åtkomst till program eller molnresurser (SharePoint-webbplatser, dokument) och tilldela licenser till medlemmar. Läs mer om dynamiska grupper i [dedikerade grupper i Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

Den aktuella [Azure AD Premium P1 eller P2 licensiering](https://azure.microsoft.com/pricing/details/active-directory/) krävs för att skapa och använda dynamiska grupper. Mer information finns i artikeln [skapa attributbaserade regler för dynamiskt gruppmedlemskap i Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Vad är de inbyggda dynamiska grupperna?
Den **alla användare** dynamisk grupp gör det möjligt för administratörer av klientorganisationer att skapa en grupp som innehåller alla användare i klientorganisationen med ett enda klick. Som standard den **alla användare** gruppen omfattar alla användare i katalogen, inklusive medlemmar och gäster.
I den nya Azure Active Directory-administrationsportalen, kan du aktivera den **alla användare** i vyn gruppinställningar.

![Visar aktivera gruppen Alla användare som ställts in på Ja](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Härdning av den dynamiska gruppen som har alla användare
Som standard den **alla användare** gruppen innehåller användare samt dina B2B-samarbete (Gäst). Du kan ytterligare skydda din **alla användare** gruppen med hjälp av en regel för att ta bort gästanvändare. I följande bild visas den **alla användare** grupp har ändrats för att exkludera gäster.

![Visar regeln där användaren inte är lika med gästen](media/use-dynamic-groups/exclude-guest-users.png)

Det kan också vara bra att skapa en dynamisk grupp som innehåller endast gästanvändare, så att du kan tillämpa principer (till exempel Azure AD villkorliga åtkomstprinciper) till dem.
Hur sådan grupp kan se ut:

![Visar regeln där användartyp är lika med gästen](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Nästa steg

- [Användaregenskaper för B2B-samarbete](user-properties.md)
- [Att lägga till en B2B-användare till en roll](add-guest-to-role.md)
- [Villkorlig åtkomst för användare i B2B-samarbetet](conditional-access.md)

