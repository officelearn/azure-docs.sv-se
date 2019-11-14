---
title: Hantering av administrativa enheter (för hands version) – Azure AD | Microsoft Docs
description: Använd administrativa enheter för mer detaljerad delegering av behörigheter i Azure Active Directory
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028420"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Hantering av administrativa enheter i Azure Active Directory (för hands version)

I den här artikeln beskrivs administrativa enheter i Azure Active Directory (Azure AD). En administrativ enhet är en Azure AD-resurs som kan vara en behållare för andra Azure AD-resurser. I den här för hands versionen kan de här resurserna endast vara användare. Till exempel kan en administrativ enhet med ett användar konto med administratör uppdatera profil information, återställa lösen ord och tilldela licenser enbart för användare i deras administrativa enheter.

Du kan använda administrativa enheter för att delegera administrativa behörigheter över del mängder av användare och tillämpa principer på en delmängd av användare. Du kan använda administrativa enheter för att delegera behörigheter till regionala administratörer eller ange en princip på en detaljerad nivå.

## <a name="deployment-scenario"></a>Distributions scenario

Administrativa enheter kan vara användbara i organisationer med oberoende avdelningar. Överväg exemplet på en stor University som består av många autonoma skolor (skola, skola och så vidare) att var och en har sina egna IT-administratörer som kontrollerar åtkomst, hanterar användare och anger principer för deras skola. En central administratör kan skapa en administrativ enhet för skolan och fylla den med endast affärs skol elever och personal. Sedan kan den centrala administratören lägga till företagets IT-personal till en omfattnings roll som ger administrativa behörigheter över enbart Azure AD-användare i affärs skolans administrativa enhet.

## <a name="license-requirements"></a>Licenskrav

Om du vill använda administrativa enheter måste du ha en Azure Active Directory Premium-licens för varje administratörs enhets administratör. Mer information finns i [komma igång med Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Hantera administrativa enheter

I den här för hands versionen är det enda sättet att skapa och hantera administrativa enheter att använda Azure Active Directory-modulen för Windows PowerShell-cmdlet: ar enligt beskrivningen i [arbeta med administrativa enheter](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Mer information om program varu krav och installation av Azure AD-modulen, och referensinformation om cmdlets för Azure AD-modulen för hantering av administrativa enheter, inklusive syntax, parameter beskrivningar och exempel finns i [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Nästa steg

[Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md)
