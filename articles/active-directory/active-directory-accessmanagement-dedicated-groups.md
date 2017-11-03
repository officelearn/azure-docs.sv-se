---
title: Dedikerade grupper i Azure Active Directory | Microsoft Docs
description: "Översikt över hur dedikerade grupper resurser i Azure Active Directory och hur de skapas."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 86158909-083a-41fe-8090-955e96ad1865
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;oldportal
ms.openlocfilehash: 992f4563064d7a292cf4fdd90a9a3c84cdec91c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="dedicated-groups-in-azure-active-directory"></a>Dedikerade grupper i Azure Active Directory
I Azure Active Directory (Azure AD), funktionen dedikerade grupper skapas automatiskt och fyller medlemskap för Azure AD fördefinierade grupper. Medlemmar i dedikerade grupper kan inte läggas till eller tas bort med hjälp av den klassiska Azure-portalen, Windows PowerShell-cmdlets eller programmässigt.

> [!NOTE]
> Dedikerade grupper kräver att en Azure AD Premium-licens har tilldelats
>
> * Administratören som hanterar regeln för en grupp
> * alla användare som är markerade som regeln ska vara medlem i gruppen
>
>

**Så här aktiverar du dedikerade grupper**

1. I den [Azure-portalen](https://portal.azure.com)väljer **Active Directory**, och sedan öppna din organisations katalog.
2. Välj den **grupper** och sedan öppna den grupp du vill redigera.
3. Välj den **konfigurera** fliken och ange sedan **aktivera särskilda grupper** till **Ja**.

När du aktiverar dedikerade grupper växeln har angetts till **Ja**, ytterligare kan du aktivera katalogen för att automatiskt skapa gruppen Alla användare dedikerade genom att ange den **aktiverar ”alla användare” grupp** växla till  **Ja**. Du kan sedan också redigera namnet på den här dedikerade gruppen genom att skriva det i den **visningsnamn för ”alla användare” grupp** fältet.

Gruppen Alla användare kan användas för att tilldela samma behörigheter för alla användare i din katalog. Du kan till exempel ge alla användare i din katalogåtkomst till ett SaaS-program genom att tilldela behörighet för gruppen Alla användare dedikerade till det här programmet.

Dedikerad alla användare gruppen innehåller alla användare i katalogen, inklusive gäster och externa användare. Om du behöver en grupp som omfattar inte externa användare, så du kan göra detta genom att skapa en grupp med en attributbaserad dynamisk regel till exempel följande:

                (user.userPrincipalName -notContains "#EXT#@")

För en grupp som inte omfattar alla gäster, använder du en regel till exempel följande:

                (user.userType -ne "Guest")

Mer information om hur du skapar *avancerade* regler (regler som kan innehålla flera jämförelser) för dynamiska gruppmedlemskap finns i [Använda attribut för att skapa avancerade regler](active-directory-accessmanagement-groups-with-advanced-rules.md).

### <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Vad är Azure Active Directory?](active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
