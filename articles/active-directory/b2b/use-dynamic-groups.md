---
title: Dynamiska grupper och Azure Active Directory B2B-samarbete | Microsoft Docs
description: Visar hur du använder dynamiska grupper i Azure AD med Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 12/14/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 18057b71415bea5d5f029db6fe311f76c1a549a1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267532"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamiska grupper och Azure Active Directory B2B-samarbete

## <a name="what-are-dynamic-groups"></a>Vad är dynamiska grupper?
Dynamisk konfiguration av medlemskap i säkerhetsgrupper för Azure Active Directory (Azure AD) finns i [Azure-portalen](https://portal.azure.com). Administratörer kan ange regler för att fylla i grupper som har skapats i Azure AD baserat på användarattribut (till exempel userType, avdelning eller land). Medlemmar kan automatiskt läggas till eller tas bort från en säkerhetsgrupp baserat på deras attribut. Dessa grupper kan ge åtkomst till program eller molnresurser (SharePoint-webbplatser, dokument) och tilldela licenser till medlemmar. Läs mer om dynamiska grupper i [särskilda grupper i Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

Rätt [licensiering Azure AD Premium P1 eller P2](https://azure.microsoft.com/pricing/details/active-directory/) krävs för att skapa och använda dynamiska grupper. Läs mer i artikeln [skapa attributbaserade regler för dynamiska gruppmedlemskap i Azure Active Directory](../active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Vad är de inbyggda dynamiska grupperna?
Den **alla användare** dynamisk grupp gör innehavaradministratörer att skapa en grupp som innehåller alla användare i klienten med en enda klickning. Som standard den **alla användare** gruppen innehåller alla användare i katalogen, inklusive medlemmar och gäster.
I den nya Azure Active Directory-administratörsportalen kan du välja att aktivera den **alla användare** i vyn inställningar.

![Visar aktivera gruppen Alla användare till Ja](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Härdning av den dynamiska gruppen som alla användare
Som standard den **alla användare** innehåller gruppen användare samt dina B2B-samarbete (Gäst). Du kan ytterligare skydda din **alla användare** gruppen med hjälp av en regel för att ta bort gästanvändare. Följande bild visar den **alla användare** gruppen ändras för att utesluta gäster.

![Visar regeln där användaren inte är lika med gäst](media/use-dynamic-groups/exclude-guest-users.png)

Du kan också vara bra att skapa en ny dynamisk grupp som innehåller endast gästanvändare, så att du kan tillämpa principer (till exempel Azure AD villkorliga åtkomstprinciper) till dem.
Hur sådan grupp kan se ut:

![Visar regeln där användaren är lika med gäst](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Nästa steg

- [Egenskaper för användare av B2B-samarbete](user-properties.md)
- [Lägger till en B2B-samarbete användare till en roll](add-guest-to-role.md)
- [Villkorlig åtkomst för användare för B2B-samarbete](conditional-access.md)

