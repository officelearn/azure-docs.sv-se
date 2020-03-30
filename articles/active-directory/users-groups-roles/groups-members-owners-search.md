---
title: Sök och filtrera grupper medlemmar och ägare (förhandsversion) - Azure Active Directory | Microsoft-dokument
description: Sök och filtrera grupper medlemmar och ägare i Azure-portalen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206120"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Sökgrupper och medlemmar (förhandsversion) i Azure Active Directory

I den här artikeln beskrivs hur du söker efter medlemmar och ägare till en grupp och hur du använder sökfilter som en del av förhandsgranskningen av gruppförbättringar i Azure Active Directory-portalen (Azure AD). Det finns massor av förbättringar i gruppernas upplevelser som hjälper dig att hantera dina grupper, inklusive medlemmar och ägare, snabbt och enkelt. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ändringar i den här förhandsgranskningen inkluderar:

- Sökfunktioner för nya grupper, till exempel sökfunktioner för delsträngar i gruppnamn
- Nya filtrerings- och sorteringsalternativ på medlems- och ägarlistor
- Nya sökfunktioner för medlems- och ägarlistor
- Mer exakta gruppantal för stora grupper

## <a name="enabling-and-managing-the-preview"></a>Aktivera och hantera förhandsgranskningen

Vi har gjort det enkelt att gå med i förhandsversionen:

  1. Logga in på [Azure AD-portalen](https://portal.azure.com)och välj **Grupper**.
  2. På sidan Grupper – Alla grupper väljer du banderollen högst upp på sidan för att gå med i förhandsgranskningen.

Du kan också kolla in de senaste funktionerna och förbättringarna genom att välja **Förhandsgranska information** på sidan **Alla grupper.** När du har gått med i förhandsgranskningen kan du se förhandsgranskningstaggen på alla gruppsidor som har förbättringar och som ingår i förhandsgranskningen. Alla grupper har inte uppdaterats som en del av den här förhandsgranskningen.

Om du har några problem kan du växla tillbaka den äldre upplevelsen genom att välja banderollen högst upp på sidan **Alla grupper.** Vi uppskattar din feedback så att vi kan förbättra vår upplevelse.

## <a name="group-search-and-sorting"></a>Gruppsökning och sortering

Grupplistesökningen har förbättrats så att sökningen automatiskt utför en `startswith` och delsträngssökning i listan med gruppnamn när du kan ange en söksträng. Delsträngssökningen utförs endast på hela ord och innehåller inte specialtecken. Substring sökning är skiftlägeskänslig.

![nya delsträngssökningar på sidan Alla grupper](./media/groups-members-owners-search/groups-search-preview.png)

Till exempel returnerar en sökning efter "princip" nu både "MDM-principen – väst" och "Policy-grupp". En grupp med namnet "New_policy" skulle inte returneras.

- Du kan också utföra samma sökning i gruppmedlemskapslistor.
- Du kan nu sortera grupplistan efter namn med hjälp av pilarna till höger om namnkolumnrubriken för att sortera listan i stigande eller fallande ordning.

## <a name="group-member-search-and-filtering"></a>Gruppmedlemssökning och filtrering

### <a name="search-group-member-and-owner-lists"></a>Sökgruppmedlem och ägarlistor

Du kan nu söka i medlemmarna i en viss grupp efter namn och utföra samma sökning i listan över gruppens ägare också. I den nya upplevelsen, om du anger en sträng i sökrutan, utförs en startswith-sökning automatiskt. En sökning efter "Scott" returnerar till exempel Scott Wilkinson.

![nya delsträngssökningar på gruppmedlemmarna och ägarlistorna](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Lista över filtermedlem och ägare

Förutom sökning kan du nu filtrera medlems- och ägarlistor efter användartyp. Det här är informationen i kolumnen Användartyp i listan. Så kan du filtrera listan efter medlemmar och gäster för att avgöra om det finns några gäster i gruppen.

### <a name="view-and-manage-membership"></a>Visa och hantera medlemskap

Förutom att visa direktmedlemmar i en viss grupp kan du nu visa listan över alla medlemmar i gruppen på sidan Medlemmar. Medlemslistan innehåller alla unika medlemmar i gruppen, inklusive alla transitiva medlemmar.

Du kan också söka och filtrera listan över direkta medlemmar och listan över alla medlemmar individuellt. Filtrering av listan över alla medlemmar påverkar inte de filter som tillämpas i listan över direkta medlemmar.

## <a name="improved-group-member-counts"></a>Antal förbättrade gruppmedlemmar

Vi har förbättrat sidan **Gruppöversikt** för att ge gruppmedlemsantal för grupper av alla storlekar. Du kan se antalet medlemmar även för grupper med fler än 1 000 medlemmar. Du kan nu se det totala antalet direktmedlemmar för en grupp och det totala antalet medlemmar (alla unika medlemmar i gruppen inklusive transitiva medlemmar) på **översiktssidan.**

![Högre noggrannhet i antalet gruppmedlemskap](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om hur du arbetar med grupper i Azure AD.

- [Visa dina grupper och medlemmar](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Hantera gruppmedlemskap](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-create-rule.md)
- [Redigera dina gruppinställningar](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera åtkomst till resurser med grupper](../fundamentals/active-directory-manage-groups.md)
- [Hantera åtkomst till SaaS-appar med grupper](groups-saasapps.md)
- [Hantera grupper med PowerShell-kommandon](groups-settings-v2-cmdlets.md)
- [Lägga till en Azure-prenumeration i Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
