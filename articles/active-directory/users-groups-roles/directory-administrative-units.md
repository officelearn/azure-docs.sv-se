---
title: Förhandsversionen av administrativa enheter management i Azure Active Directory
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
ms.openlocfilehash: 0884726e59d9ab3f5a5cfe7bb0608f6b5a5da250
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450307"
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Hantering av administrativa enheter i Azure AD - förhandsversion
Den här artikeln beskriver administrativa enheter – en ny Azure Active Directory-behållare för resurser som kan användas för att delegera administrativa behörigheter över delmängder av användare och tillämpa principer för att en del av användarna. I Azure Active Directory aktivera administrativa enheter centrala administratörer att delegera behörigheter till regionadministratörer eller ange principen på en detaljerad nivå.

Detta är användbart i organisationer med oberoende avdelningar, till exempel en stor university som består av många autonoma skolor (Business skola, tekniker skola och så vidare) som är oberoende av varandra. Sådana avdelningar har sina egna IT-administratörer som åtkomst, hantera användare och ange principer specifikt för deras avdelning. Central Administratörer vill kunna bevilja dessa avdelningar administratörer behörighet över användare i deras specifika avdelningar. Mer specifikt kan i det här exemplet en central administratör, till exempel skapa en administrativ enhet för en viss skola (Business skola) och fyller den med endast företagsanvändare för skolan. Sedan en central administratör kan lägga till i företag skola IT-personal till en begränsad roll, med andra ord bevilja IT-personal behörigheter för företag school endast med school administrativa affärsenhet.

> [!IMPORTANT]
> Om du vill använda administrativa enheter kräver att administrativ enhet-omfattande administratören har en Azure Active Directory Premium-licens och Azure Active Directory Basic-licenser för alla användare i den administrativa enheten. Mer information finns i [komma igång med Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>


Från den centrala administratörs synsätt är en administrativ enhet ett katalogobjekt som kan skapas och fylls i med resurser. **Dessa resurser kan vara endast användare i den här förhandsversionen.** När skapas och konfigureras kan den administrativa enheten användas som omfattning för att begränsa behörigheten beviljade endast med resurser som ingår i den administrativa enheten.

## <a name="managing-administrative-units"></a>Hantera administrativa enheter
I den här förhandsversionen kan du skapa och hantera administrativa enheter med hjälp av Azure Active Directory-modulen för Windows PowerShell-cmdletar. Mer information om hur du gör finns i [arbeta med administrativa enheter](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Mer information om programvarukrav och installera Azure AD-modulen, samt information om Azure AD-modulen-cmdletar för att hantera administrativa enheter, inklusive syntax, beskrivningar och exempel finns i [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Nästa steg
[Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md)
