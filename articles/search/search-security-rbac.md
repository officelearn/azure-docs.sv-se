---
title: Ange RBAC-roller för administrativ åtkomst i Azure i portalen – Azure Search
description: Rollbaserad administrativ kontroll (RBAC) i Azure Portal för att kontrol lera och delegera administrativa uppgifter för Azure Search hantering.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 924d2529e3477c299d4a90c076fe9e6c8faf11f3
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647395"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Ange RBAC-roller för administrativ åtkomst

Azure tillhandahåller en [Global rollbaserad verifierings modell](../role-based-access-control/role-assignments-portal.md) för alla tjänster som hanteras via portalen eller Resource Manager-API: er. Rollerna ägare, deltagare och läsare bestämmer vilken *tjänst administrations* nivå som Active Directory användare, grupper och säkerhets objekt som tilldelas till varje roll. 

> [!Note]
> Det finns inga rollbaserade åtkomst kontroller för att skydda delar av ett index eller en delmängd av dokument. För identitets-baserad åtkomst över Sök resultat kan du skapa säkerhets filter för att trimma resultat efter identitet, ta bort dokument som beställaren inte ska ha åtkomst till. Mer information finns i [säkerhets filter](search-security-trimming-for-azure-search.md) och [säkra med Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Hanterings aktiviteter efter roll

För Azure Search är roller kopplade till behörighets nivåer som stöder följande hanterings aktiviteter:

| Role | Uppgift |
| --- | --- |
| Ägare |Skapa eller ta bort tjänsten eller ett objekt på tjänsten, inklusive API-nycklar, index, indexerare, data källor för indexerare och index scheman.<p>Visa tjänst status, inklusive antal och lagrings storlek.<p>Lägg till eller ta bort roll medlemskap (endast ägaren kan hantera roll medlemskap).<p>Prenumerations administratörer och tjänst ägare har automatiskt medlemskap i rollen ägare. |
| Deltagare |Samma åtkomst nivå som ägare, minus RBAC-roll hantering. En deltagare kan till exempel skapa eller ta bort objekt, eller Visa och återskapa [API-nycklar](search-security-api-keys.md), men kan inte ändra roll medlemskap. |
| [Den inbyggda rollen Search Service Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Motsvarar deltagar rollen. |
| Läsare |Visa service Essentials och Metrics. Medlemmar i den här rollen kan inte Visa index, indexerare, data källa eller viktig information.  |

Roller beviljar inte åtkomst behörighet till tjänstens slut punkt. Sök tjänst åtgärder, till exempel index hantering, index ifyllning och frågor om Sök data, kontrol leras via API-nycklar, inte roller. Mer information finns i [Hantera API-nycklar](search-security-api-keys.md).

## <a name="see-also"></a>Se också

+ [Hantera med PowerShell](search-manage-powershell.md) 
+ [Prestanda och optimering i Azure Search](search-performance-optimization.md)
+ [Kom igång med rollbaserad Access Control i Azure Portal](../role-based-access-control/overview.md).