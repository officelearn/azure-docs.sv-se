---
title: Hantering av administrativa enheter (förhandsversion) – Azure AD | Microsoft-dokument
description: Använda administrativa enheter för mer detaljerad delegering av behörigheter i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028420"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Hantering av administrativa enheter i Azure Active Directory (förhandsversion)

I den här artikeln beskrivs administrativa enheter i Azure Active Directory (Azure AD). En administrativ enhet är en Azure AD-resurs som kan vara en behållare för andra Azure AD-resurser. I den här förhandsversionen kan dessa resurser bara vara användare. En administratör för ett administrativt enhetsomfattat användarkonto kan till exempel uppdatera profilinformation, återställa lösenord och tilldela licenser endast för användare i deras administrativa enhet.

Du kan använda administrativa enheter för att delegera administratörsbehörigheter över undergrupper av användare och tillämpa principer på en delmängd användare. Du kan använda administrativa enheter för att delegera behörigheter till regionala administratörer eller för att ange principen på detaljerad nivå.

## <a name="deployment-scenario"></a>Distributionsscenario

Administrativa enheter kan vara användbara i organisationer med oberoende avdelningar. Tänk på exemplet med ett stort universitet som består av många autonoma skolor (School of Business, School of Engineering och så vidare) som var och en har sina egna IT-administratörer som styr åtkomst, hantera användare och ange principer för sin skola. En central administratör skulle kunna skapa en administrativ enhet för Handelshögskolan och befolka den med endast affärsskoleelever och personal. Sedan kan den centrala administratören lägga till Business school IT-personal i en begränsad roll som ger administratörsbehörighet över endast Azure AD-användare i business school administrativa enheten.

## <a name="license-requirements"></a>Licenskrav

För att kunna använda administrativa enheter krävs en Azure Active Directory Premium-licens för varje administratör för administrativa enheter. Mer information finns i [Komma igång med Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Hantera administrativa enheter

I den här förhandsversionen är det enda sättet du kan skapa och hantera administrativa enheter att använda Azure Active Directory Module för Windows PowerShell-cmdlets enligt beskrivningen i [Arbeta med administrativa enheter](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Mer information om programvarukrav och installation av Azure AD-modulen och referensinformation om Azure AD-modulens cmdlets för hantering av administrativa enheter, inklusive syntax, parameterbeskrivningar och exempel, finns i [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Nästa steg

[Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md)
