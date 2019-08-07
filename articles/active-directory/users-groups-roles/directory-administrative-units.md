---
title: Hantering av administrativa enheter (för hands version) – Azure Active Directory | Microsoft Docs
description: Använd administrativa enheter för mer detaljerad delegering av behörigheter i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b4bdced50f806367a53881d5ef0abd0a3710496
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736787"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Hantering av administrativa enheter i Azure Active Directory (offentlig för hands version)

I den här artikeln beskrivs administrativa enheter i Azure Active Directory (Azure AD), som är en behållare med resurser som kan användas för att delegera administrativa behörigheter över del mängder av användare och tillämpa principer på en delmängd av användare. I Azure AD gör administrativa enheter det möjligt för centrala administratörer att delegera behörigheter till regionala administratörer eller att ange en princip på en detaljerad nivå.

Detta är användbart i organisationer med oberoende avdelningar, till exempel ett stort universitet som består av många autonoma skolor (affärs skola, teknik skola och så vidare) som är oberoende av varandra. Dessa avdelningar har sina egna IT-administratörer som kontrollerar åtkomst, hanterar användare och anger principer specifikt för deras delning. Centrala administratörer vill kunna ge dessa avdelnings administratörer behörigheter över användarna i sina specifika avdelningar. Mer specifikt, i det här exemplet, kan en central administratör t. ex. skapa en administrativ enhet för en viss skola (affärs skola) och fylla den med endast affärs skolans användare. Sedan kan en central administratör lägga till affärs skolans IT-personal till en begränsad roll, med andra ord, ge IT-personalen i affärsskola administrativ behörighet enbart över den administrativa enheten i Business School.

> [!IMPORTANT]
> För att kunna använda administrativa enheter måste administratören av administratörs enheten ha en Azure Active Directory Premium-licens. Mer information finns i [komma igång med Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>

Från den centrala administratörens sikt är en administrativ enhet ett katalog objekt som kan skapas och fyllas i med resurser. **I den här för hands versionen kan de här resurserna endast vara användare.** När du har skapat och fyllt i den kan den administrativa enheten användas som ett omfång för att begränsa den beviljade behörigheten endast för de resurser som finns i den administrativa enheten.

## <a name="managing-administrative-units"></a>Hantera administrativa enheter

I den här för hands versionen kan du skapa och hantera administrativa enheter med hjälp av Azure Active Directory-modulen för Windows PowerShell-cmdletar. Mer information om hur du gör finns i [arbeta med administrativa enheter](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Mer information om program varu krav och installation av Azure AD-modulen och information om Azure AD-modulen cmdlets för att hantera administrativa enheter, inklusive syntax, parameter beskrivningar och exempel finns i [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Nästa steg

[Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md)
