---
title: Förstå resursåtkomst i Azure | Microsoft Docs
description: Det här avsnittet beskriver begrepp om att använda prenumerationsadministratörer för att styra åtkomst till resurser i den kompletta Azure-portalen
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro;
ms.openlocfilehash: 3be2026e480f33a7b8d403d375614310695613da
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266728"
---
# <a name="understanding-resource-access-in-azure"></a>Förstå resursåtkomst i Azure

Åtkomstkontroll i Azure startar ur faktureringsinformation. Ägaren till ett Azure-konto som nås genom att besöka den [Azure Kontocenter](https://account.azure.com), är kontot Administratör (AA). Prenumerationer är en behållare för fakturering, men de fungerar som en säkerhetsgräns: varje prenumeration har en Service systemadministratörskontot (SA) som kan lägga till, ta bort och ändra Azure-resurser i den prenumerationen genom att använda den [Azure-portalen](https://portal.azure.com/). Standard-SA för en ny prenumeration är AA, men AA kan ändra SA i Azure Kontocenter.

<br><br>![Azure-konton][1]

Prenumerationer har också en association med en katalog. Katalogen definierar en uppsättning användare. Det kan vara användare från arbetet eller skolan som skapade katalogen eller de kan vara externa gästanvändare. Prenumerationer är tillgängliga för en delmängd av de directory-användare som har tilldelats som tjänsten systemadministratörskontot (SA) eller Medadministratör (CA); Det enda undantaget är att äldre skäl Microsoft-konton (tidigare Windows Live ID) kan tilldelas som SA eller Certifikatutfärdare utan att vara närvarande i katalogen.

<br><br>![Åtkomstkontroll i Azure][2]

Funktioner i Azure-portalen kan SA som är inloggad med ett Account för att ändra den katalog som är kopplad till en prenumeration. Den här åtgärden har effekter på åtkomstkontroll i den aktuella prenumerationen.

<br><br>![Enkel inloggning flöde][3]

Vid en enkel, en organisation (till exempel Contoso) genomdriva fakturering och åtkomstkontroll på samma uppsättning prenumerationer. Katalogen är kopplad till prenumerationer som ägs av en enda Azure-konto. Användarna ser två uppsättningar resurser (visas med orange färg i den föregående bilden) efter genomförd inloggning till Azure portal:

* Kataloger där användarkontot finns (källkod eller läggas till som ett huvudnamn för sekundärnyckel). Observera att katalogen som används för inloggning inte är relevanta för den här beräkningen så att dina kataloger visas alltid oavsett om du har loggat in.
* Resurser som är en del av prenumerationer som är kopplade till den katalog som används för inloggning och användaren kan komma åt (där de är en SA eller CA).

<br><br>![Användare med flera prenumerationer och kataloger][4]

Användare med prenumerationer i flera kataloger har möjlighet att växla aktuell kontext för Azure-portalen genom att använda prenumerationsfiltret. Detta resulterar i en separat inloggning till en annan katalog under försättsbladen, men detta kan åstadkommas sömlöst med enkel inloggning (SSO).

Åtgärder som till exempel flytta resurser mellan prenumerationer kan vara svårare på grund av den här vyn för en katalog över prenumerationer. För att genomföra överföringen resurs, kan det vara nödvändigt att första gången den **redigera katalog** på sidan prenumerationer i **inställningar** associera prenumerationer i samma katalog.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du ändrar administratörer för en Azure-prenumeration finns i [Lägga till eller ändra Azure-administratörsroller](../billing/billing-add-change-azure-subscription-administrator.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns [hur Azure-prenumerationer är associerade med Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md)
* Mer information om hur du tilldelar roller i Azure AD finns i [Tilldela administratörsroller i Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/rbac-and-directory-admin-roles/IC707931.png
[2]: ./media/rbac-and-directory-admin-roles/IC707932.png
[3]: ./media/rbac-and-directory-admin-roles/IC707933.png
[4]: ./media/rbac-and-directory-admin-roles/IC707934.png
