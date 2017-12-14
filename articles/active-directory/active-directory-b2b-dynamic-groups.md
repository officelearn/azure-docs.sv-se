---
title: Dynamiska grupper och Azure Active Directory B2B-samarbete | Microsoft Docs
description: "Azure Active Directory B2B-samarbete kan användas med dynamiska grupper i Azure AD"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.reviewer: sasubram
ms.openlocfilehash: ed0c8c271f8db5e5d17fd578317a04679df7987d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamiska grupper och Azure Active Directory B2B-samarbete

## <a name="what-are-dynamic-groups"></a>Vad är dynamiska grupper?
Dynamisk konfiguration av medlemskap i säkerhetsgrupper för Azure Active Directory (Azure AD) finns i [Azure-portalen](https://portal.azure.com). Administratörer kan ange regler för att fylla i grupper som har skapats i Azure Active Directory baserat på användarattribut (till exempel userType, avdelning eller land). Medlemmar kan automatiskt läggas till eller tas bort från en säkerhetsgrupp baserat på deras attribut. Dessa grupper kan ge åtkomst till program eller molnresurser (SharePoint-webbplatser, dokument) och tilldela licenser till medlemmar. Läs mer om dynamiska grupper i [särskilda grupper i Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Rätt [licensiering Azure AD Premium P1 eller P2](https://azure.microsoft.com/pricing/details/active-directory/) krävs för att skapa och använda dynamiska grupper. Läs mer i artikeln [skapa attributbaserade regler för dynamiska gruppmedlemskap i Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Vad är de inbyggda dynamiska grupperna?
Den **alla användare** dynamisk grupp gör innehavaradministratörer att skapa en grupp som innehåller alla användare i klienten med en enda klickning. Som standard den **alla användare** gruppen innehåller alla användare i katalogen, inklusive medlemmar och gäster.
I den nya Azure Active Directory-administratörsportalen kan du välja att aktivera den **alla användare** i vyn inställningar.

![inbyggda grupper](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Härdning av den dynamiska gruppen som alla användare
Som standard den **alla användare** innehåller gruppen användare samt dina B2B-samarbete (Gäst). Du kan ytterligare skydda din **alla användare** gruppen med hjälp av en regel för att ta bort gästanvändare. Följande bild visar den **alla användare** gruppen ändras för att utesluta gäster.

![Aktivera gruppen för alla användare](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

Du kan också vara bra att skapa en ny dynamisk grupp som innehåller endast gästanvändare, så att du kan tillämpa principer (till exempel Azure AD villkorliga åtkomstprinciper) till dem.
Hur sådan grupp kan se ut:

![Exkludera gästanvändare](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Egenskaper för användare av B2B-samarbete](active-directory-b2b-user-properties.md)
* [Lägger till en B2B-samarbete användare till en roll](active-directory-b2b-add-guest-to-role.md)
* [Delegera B2B-samarbete inbjudningar](active-directory-b2b-delegate-invitations.md)
* [B2B-samarbete kod och PowerShell-exempel](active-directory-b2b-code-samples.md)
* [Konfigurera SaaS-appar för B2B-samarbete](active-directory-b2b-configure-saas-apps.md)
* [Användartoken för B2B-samarbete](active-directory-b2b-user-token.md)
* [B2B-samarbete användaranspråk mappning](active-directory-b2b-claims-mapping.md)
* [Extern delning av Office 365](active-directory-b2b-o365-external-user.md)
* [Aktuella begränsningar för B2B-samarbete](active-directory-b2b-current-limitations.md)
