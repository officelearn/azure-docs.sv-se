---
title: Förhandsgranskning för hantering av administrativa enheter i Azure Active Directory
description: Med hjälp av administrativa enheter för mer detaljerade delegeringen av behörigheter i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.component: users-groups-roles
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 4fa93e4bf64e01b44b0826b2182b125004c49609
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Hantering av administrativa enheter i Azure AD - förhandsversion
Den här artikeln beskriver administrativa enheter – en ny Azure Active Directory-behållare för resurser som kan användas för att delegera administrativa behörigheter över delmängder av användare och tillämpa principer för att en del av användarna. I Azure Active Directory aktivera administrativa enheter centrala administratörer att delegera behörigheter till regionala administratörer eller skapa princip på en detaljerad nivå.

Detta är användbart i organisationer med oberoende avdelningar, till exempel en stor university som består av många autonoma skolorna (Business skola, ingenjörer skola och så vidare) som är oberoende av varandra. Sådana avdelningar har sina egna IT-administratörer som styr åtkomsten, hantera användare och ange principer för fördelningen. Central Administratörer vill kunna ge dessa dig administratörer behörigheter till användare i deras specifika avdelningar. Mer specifikt kan i det här exemplet administratör av en central, till exempel skapa en administrativ enhet för en viss skola (Business skolan) och fylla det med bara de skola företagsanvändarna. En central administratör kan lägga till företag skola IT-personal till en roll som omfattas, med andra ord ge IT-personal Business skola behörigheter endast med administrativa affärsenhet skola.

> [!IMPORTANT]
> Om du vill använda administrativa enheter kräver administrativa enhet omfång administratören att ha en licens för Azure Active Directory Premium och Azure Active Directory Basic licenser för alla användare i administrativa enhet. Mer information finns i [komma igång med Azure AD Premium](active-directory-get-started-premium.md).
>


Från central administratörs synsätt är en administrativ enhet ett katalogobjekt som kan skapas och fylls med resurser. **Dessa resurser kan vara endast användare i den här förhandsversionen.** När skapas och konfigureras kan den administrativa enheten användas som omfattning för att begränsa den beviljade behörigheten endast över resurser som ingår i den administrativa enheten.

## <a name="managing-administrative-units"></a>Hantera administrativa enheter
I den här förhandsversionen kan du skapa och hantera administrativa enheter med hjälp av Azure Active Directory-modulen för Windows PowerShell-cmdlets. Mer information om hur du gör finns [arbeta med administrativa enheter](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Mer information om programvarukrav och installera Azure AD-modulen och information om Azure AD-modulen-cmdletar för att hantera administrativa enheter, inklusive syntax och beskrivningar av parametern exempel, se [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Nästa steg
[Azure Active Directory-versioner](active-directory-whatis.md)
