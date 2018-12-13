---
title: Ange RBAC-roller för Azure administrativ åtkomst i portalen – Azure Search
description: Rollbaserad administrationskontroll (RBAC) i Azure-portalen för att styra och delegera administrativa uppgifter för hantering av Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 38b8e8a0e413f367d34a4ccf5dbd87817891b8ea
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313028"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Ange RBAC-roller för administrativ åtkomst

Azure tillhandahåller en [globala rollbaserad auktoriseringsmodellen](../role-based-access-control/role-assignments-portal.md) för alla tjänster som hanteras via portalen eller Resource Manager API: er. Ägare, deltagare och läsare bestämmer säkerhetsnivån *tjänstadministration* för Active Directory-användare, grupper och säkerhetsobjekt som tilldelats varje roll. 

> [!Note]
> Det finns ingen rollbaserade åtkomstkontroller för att skydda delar av ett index eller en delmängd av dokument. För identitetsbaserad åtkomst via sökresultat, kan du skapa säkerhetsfilter för att trimma resultat av identitet, ta bort dokument som begäranden inte ska ha åtkomst. Mer information finns i [säkerhetsfilter](search-security-trimming-for-azure-search.md) och [Secure med Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Hanteringsuppgifter efter roll

Roller är associerade med behörighetsnivåer som har stöd för följande hanteringsuppgifter för Azure Search:

| Roll | Aktivitet |
| --- | --- |
| Ägare |Skapa eller ta bort tjänsten eller alla objekt på tjänsten, inklusive api-nycklar, index, indexerare, indexerare-datakällor och indexerare scheman.<p>Visa status för tjänsten, inklusive antalet och lagringsstorlek.<p>Lägg till eller ta bort rollmedlemskap (endast en ägare kan hantera medlemskap).<p>Prenumerationens administratörer och ansvariga är automatisk medlemmar i rollen ägare. |
| Deltagare |Samma nivå av åtkomst som ägare, minus RBAC rollhantering. Till exempel deltagare kan skapa eller ta bort objekt, eller visa och återskapa [api-nycklar](search-security-api-keys.md), men kan inte ändra rollmedlemskap. |
| [Search Service inbyggda deltagarrollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Motsvarar rollen deltagare. |
| Läsare |Visa service essentials och mått. Medlemmar i den här rollen kan inte visa index, indexerare, datakälla eller viktig information.  |

Roller bevilja inte åtkomsträttigheter till tjänsteslutpunkt. Search serviceåtgärder, till exempel indexhantering, indexet population och frågor på sökdata, styrs via api-nycklar, inte roller. Mer information finns i [hantera api-nycklar](search-security-api-keys.md).

## <a name="see-also"></a>Se också

+ [Hantera med PowerShell](search-manage-powershell.md) 
+ [Prestanda och optimering i Azure Search](search-performance-optimization.md)
+ [Kom igång med rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md).