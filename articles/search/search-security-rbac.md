---
title: Ange RBAC-roller för azure-administrativ åtkomst
titleSuffix: Azure Cognitive Search
description: Rollbaserad administrativ kontroll (RBAC) i Azure-portalen för att kontrollera och delegera administrativa uppgifter för Azure Cognitive Search-hantering.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112552"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Ange RBAC-roller för administrativ åtkomst till Azure Cognitive Search

Azure tillhandahåller en [global rollbaserad auktoriseringsmodell](../role-based-access-control/role-assignments-portal.md) för alla tjänster som hanteras via portal- eller Resurshanterarens API:er. Rollerna Ägare, Deltagare och Läsare bestämmer nivån på *tjänstadministration* för Active Directory-användare, grupper och säkerhetsobjekt som tilldelats varje roll. 

> [!Note]
> Det finns inga rollbaserade åtkomstkontroller för att skydda delar av ett index eller en delmängd av dokument. För identitetsbaserad åtkomst via sökresultat kan du skapa säkerhetsfilter för att trimma resultat efter identitet och ta bort dokument som beställaren inte ska ha åtkomst till. Mer information finns i [Säkerhetsfilter](search-security-trimming-for-azure-search.md) och [Säker med Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Hanteringsuppgifter efter roll

För Azure Cognitive Search associeras roller med behörighetsnivåer som stöder följande hanteringsuppgifter:

| Roll | Aktivitet |
| --- | --- |
| Ägare |Skapa eller ta bort tjänsten eller ett objekt på tjänsten, inklusive api-nycklar, index, indexerare, indexerare datakällor och indexerarscheman.<p>Visa tjänststatus, inklusive antal och lagringsstorlek.<p>Lägga till eller ta bort rollmedlemskap (endast en ägare kan hantera rollmedlemskap).<p>Prenumerationsadministratörer och tjänstägare har automatiskt medlemskap i rollen Ägare. |
| Deltagare |Samma åtkomstnivå som ägare, minus RBAC-rollhantering. En deltagare kan till exempel skapa eller ta bort objekt eller visa och återskapa [api-nycklar](search-security-api-keys.md), men kan inte ändra rollmedlemskap. |
| [Söktjänstdeltagare inbyggd roll](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Motsvarar rollen Deltagare. |
| Läsare |Visa viktiga servicevärden och mått. Medlemmar i den här rollen kan inte visa index-, indexerare, datakälla eller nyckelinformation.  |

Roller ger inte åtkomsträttigheter till tjänstslutpunkten. Söktjänståtgärder, till exempel indexhantering, indexpopulation och frågor om sökdata, styrs via api-nycklar, inte roller. Mer information finns i [Hantera api-nycklar](search-security-api-keys.md).

## <a name="see-also"></a>Se även

+ [Hantera med PowerShell](search-manage-powershell.md) 
+ [Prestanda och optimering i Azure Cognitive Search](search-performance-optimization.md)
+ [Kom igång med rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md).