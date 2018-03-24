---
title: Ange RBAC-roller för Azure Search administrativ åtkomst i portal | Microsoft Docs
description: Rollbaserad administrativ styrning i Azure-portalen.
services: search
documentationcenter: ''
author: HeidiSteen
manager: cgronlun
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: a5f6395a0160402b3b0dfe95dc12b866854e70d9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="set-rbac-roles-for-administrative-access"></a>Ange RBAC-roller för administrativ åtkomst

Azure tillhandahåller en [globala rollbaserad auktoriseringsmodellen](../active-directory/role-based-access-control-configure.md) hanteras via portalen eller Resource Manager API: er för alla tjänster. Ägare, deltagare och läsare roller att fastställa säkerhetsnivån *tjänsten administration* för Active Directory-användare, grupper och säkerhetsobjekt som tilldelats till varje roll. 

> [!Note]
> Det finns ingen rollbaserade åtkomstkontroller för att skydda delar av ett index eller en delmängd av dokument. För identitetsbaserad åtkomst via sökresultat, kan du skapa säkerhetsfilter Beskär resultat av identitet, tar bort dokument som begäranden inte ska ha åtkomst. Mer information finns i [säkerhetsfilter](search-security-trimming-for-azure-search.md) och [Secure med Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Uppgifter för hantering av roll

Roller är associerade med behörighetsnivåer som har stöd för följande administrativa uppgifter för Azure Search:

| Roll | Aktivitet |
| --- | --- |
| Ägare |Skapa eller ta bort tjänsten eller ett objekt på tjänsten, inklusive api-nycklar, index, indexerare, indexeraren datakällor och indexeraren scheman.<p>Visa status för tjänsten, inklusive antalet och lagringsstorlek.<p>Lägg till eller ta bort rollmedlemskap (endast en ägare kan hantera medlemskap).<p>Prenumerationsadministratörer och tjänstens ägare är automatisk medlemmar i rollen som ägare. |
| Deltagare |Samma åtkomstnivå som ägare, minus RBAC rollhantering. Till exempel en deltagare kan skapa eller ta bort objekt, eller visa och återskapa [api-nycklar](search-security-api-keys.md), men det går inte att ändra medlemskap i roller. |
| [Sök tjänsten inbyggda deltagarrollen](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#search-service-contributor) | Motsvarar deltagarrollen. |
| Läsare |Visa service essentials och mått. Medlemmar i den här rollen kan inte visa index indexerare, datakälla eller viktig information.  |

Roller bevilja inte åtkomsträttigheter till tjänstslutpunkten. Sök tjänståtgärder, till exempel indexhantering, ifyllning av indexet och frågor på sökdata styrs via api-nycklar, inte roller. Mer information finns i [hantera api-nycklar](search-security-api-keys.md).

## <a name="see-also"></a>Se också

+ [Hantera med PowerShell](search-manage-powershell.md) 
+ [Prestanda och optimering i Azure Search](search-performance-optimization.md)
+ [Kom igång med rollbaserad åtkomstkontroll i Azure portal](../active-directory/role-based-access-control-what-is.md).