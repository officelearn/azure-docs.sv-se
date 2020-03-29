---
title: Dynamiska grupper och B2B-samarbete – Azure Active Directory | Microsoft-dokument
description: Visar hur du använder dynamiska Azure AD-grupper med Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41e8b81bc3594c6a378757636f70058510a38cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226899"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamiska grupper och Azure Active Directory B2B-samarbete

## <a name="what-are-dynamic-groups"></a>Vad är dynamiska grupper?
Dynamisk konfiguration av medlemskap i säkerhetsgrupp för Azure Active Directory (Azure AD) finns i [Azure-portalen](https://portal.azure.com). Administratörer kan ange regler för att fylla i grupper som skapas i Azure AD baserat på användarattribut (till exempel userType, avdelning eller land/region). Medlemmar kan automatiskt läggas till eller tas bort från en säkerhetsgrupp baserat på deras attribut. Dessa grupper kan ge åtkomst till program eller molnresurser (SharePoint-webbplatser, dokument) och tilldela licenser till medlemmar. Läs mer om dynamiska grupper i [dedikerade grupper i Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

Lämplig [Azure AD Premium P1- eller P2-licensiering](https://azure.microsoft.com/pricing/details/active-directory/) krävs för att skapa och använda dynamiska grupper. Läs mer i artikeln [Skapa attributbaserade regler för dynamiskt gruppmedlemskap i Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Skapa en dynamisk "alla användare"-grupp
Du kan skapa en grupp som innehåller alla användare i en klient med hjälp av en medlemskapsregel. När användare läggs till eller tas bort från klienten i framtiden justeras gruppens medlemskap automatiskt.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som har tilldelats rollen Global administratör eller Användaradministratör i klienten.
1. Välj **Azure Active Directory**.
2. Under **Hantera**väljer du **Grupper**och väljer sedan **Ny grupp**.
1. Välj **Säkerhet**under **Grupptyp**på sidan **Ny grupp** . Ange ett **gruppnamn** och en **gruppbeskrivning** för den nya gruppen. 
2. Under **Medlemstyp**väljer du **Dynamisk användare**och väljer sedan Lägg till dynamisk **fråga**. 
4. Markera **Redigera**ovanför textrutan **Regelsyntax.** Skriv följande uttryck i textrutan på **syntaxsidan Redigera regel:**

   ```
   user.objectId -ne null
   ```
1. Välj **OK**. Regeln visas i syntaxrutan Regel:

   ![Regelsyntax för alla användare dynamisk grupp](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Välj **Spara**. Den nya dynamiska gruppen kommer nu att omfatta B2B gästanvändare samt medlemsanvändare.


1. Välj **Skapa** på sidan **Ny grupp** om du vill skapa gruppen.

## <a name="creating-a-group-of-members-only"></a>Skapa endast en grupp medlemmar

Om du vill att gruppen ska utesluta gästanvändare och endast inkludera medlemmar i din klientorganisation skapar du en dynamisk grupp enligt beskrivningen ovan, men i **syntaxrutan Regel** anger du följande uttryck:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

Följande bild visar regelsyntaxen för en dynamisk grupp som har ändrats så att den endast innehåller medlemmar och utesluter gäster.

![Visar regel där användartyp är lika med medlem](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Bara skapa en grupp gäster

Du kan också ha nytta av att skapa en ny dynamisk grupp som bara innehåller gästanvändare, så att du kan tillämpa principer (till exempel Azure AD-principer för villkorlig åtkomst) på dem. Skapa en dynamisk grupp enligt beskrivningen ovan, men ange följande uttryck i **syntaxrutan Regel:**

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

Följande bild visar regelsyntaxen för en dynamisk grupp som har ändrats så att den endast omfattar gäster och utesluter medlemsanvändare.

![Visar regel där användartyp är lika med gäst](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Nästa steg

- [Användaregenskaper för B2B-samarbete](user-properties.md)
- [Lägga till en B2B-samarbetsanvändare i en roll](add-guest-to-role.md)
- [Villkorlig åtkomst för B2B-samarbetsanvändare](conditional-access.md)

