---
title: Ange Azure-roller för administrativ åtkomst till Azure
titleSuffix: Azure Cognitive Search
description: Rollbaserad åtkomst kontroll (RBAC) i Azure Portal för att kontrol lera och delegera administrativa uppgifter för hantering av Azure-Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: f0c8fe6b8df5efef0cf3948c8d628d20c79502ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928691"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Ange Azure-roller för administrativ åtkomst till Azure Kognitiv sökning

Azure tillhandahåller en [Global rollbaserad verifierings modell](../role-based-access-control/role-assignments-portal.md) för alla tjänster som hanteras via portalen eller Resource Manager-API: er. Rollerna ägare, deltagare och läsare bestämmer vilken *tjänst administrations* nivå som Active Directory användare, grupper och säkerhets objekt som tilldelas till varje roll. 

> [!Note]
> Det finns ingen rollbaserad åtkomst kontroll (RBAC) för att skydda delar av ett index eller en delmängd av dokument. För identitets-baserad åtkomst över Sök resultat kan du skapa säkerhets filter för att trimma resultat efter identitet, ta bort dokument som beställaren inte ska ha åtkomst till. Mer information finns i [säkerhets filter](search-security-trimming-for-azure-search.md) och [säkra med Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Hanterings aktiviteter efter roll

För Azure Kognitiv sökning associeras roller med behörighets nivåer som stöder följande hanterings aktiviteter:

| Roll | Uppgift |
| --- | --- |
| Ägare |Skapa eller ta bort tjänsten eller ett objekt på tjänsten, inklusive API-nycklar, index, indexerare, data källor för indexerare och index scheman.<p>Visa tjänst status, inklusive antal och lagrings storlek.<p>Lägg till eller ta bort roll medlemskap (endast ägaren kan hantera roll medlemskap).<p>Prenumerations administratörer och tjänst ägare har automatiskt medlemskap i rollen ägare. |
| Deltagare |Samma åtkomst nivå som ägare, minus hantering av Azure-roller. En deltagare kan till exempel skapa eller ta bort objekt, eller Visa och återskapa [API-nycklar](search-security-api-keys.md), men kan inte ändra roll medlemskap. |
| [Den inbyggda rollen Search Service Contributor](../role-based-access-control/built-in-roles.md#search-service-contributor) | Motsvarar deltagar rollen. |
| Läsare |Visa service Essentials och Metrics. Medlemmar i den här rollen kan inte Visa index, indexerare, data källa eller viktig information.  |

Roller beviljar inte åtkomst behörighet till tjänstens slut punkt. Sök tjänst åtgärder, till exempel index hantering, index ifyllning och frågor om Sök data, kontrol leras via API-nycklar, inte roller. Mer information finns i [Hantera API-nycklar](search-security-api-keys.md).

## <a name="permissions-table"></a>Behörighets tabell

I följande tabell sammanfattas de åtgärder som tillåts i Azure Kognitiv sökning och vilken nyckel som låser upp åtkomsten till en viss åtgärd.

| Åtgärd | Behörigheter |
|-----------|-------------------------|
| Skapa en tjänst | Azure-prenumerations innehavare |
| Skala en tjänst | Administratörs nyckel, RBAC-ägare eller deltagare på resursen  |
| Ta bort en tjänst | Administratörs nyckel, RBAC-ägare eller deltagare på resursen |
| Skapa, ändra och ta bort objekt på tjänsten: <br>Index och komponent delar (inklusive analys definitioner, bedömnings profiler, CORS-alternativ), indexerare, data källor, synonymer, förslag | Administratörs nyckel, RBAC-ägare eller deltagare på resursen |
| Fråga ett index | Administratör eller frågegrupp (RBAC-ej tillämpligt) |
| Fråga system information, t. ex. returnera statistik, antal och listor över objekt | Administratörs nyckel, RBAC på resursen (ägare, deltagare, läsare) |
| Hantera administratörs nycklar | Administratörs nyckel, RBAC-ägare eller deltagare på resursen |
| Hantera frågeinställningar |  Administratörs nyckel, RBAC-ägare eller deltagare på resursen  |

## <a name="see-also"></a>Se även

+ [Hantera med hjälp av PowerShell](search-manage-powershell.md) 
+ [Prestanda och optimering i Azure Kognitiv sökning](search-performance-optimization.md)
+ [Kom igång med Role-Based Access Control i Azure Portal](../role-based-access-control/overview.md).