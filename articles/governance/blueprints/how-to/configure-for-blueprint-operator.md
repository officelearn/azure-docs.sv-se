---
title: Konfigurera din miljö för Blueprint Operator
description: Lär dig hur du konfigurerar din Azure-miljö för användning med den inbyggda rollbaserade rollen (Blueprint Operator).
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873224"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Konfigurera din miljö för en skissoperatör

Hanteringen av dina skissdefinitioner och skisstilldelningar kan tilldelas olika team. Det är vanligt att en arkitekt eller styrning-grupp ansvarar för livscykelhanteringen av dina skissdefinitioner medan ett operationsteam ansvarar för att hantera tilldelningar av dessa centralt styrda skissdefinitioner.

Den inbyggda rollbaserade åtkomstkontrollen för **skissopera** (RBAC) är utformad speciellt för användning i den här typen av scenario. Rollen gör det möjligt för operationstypteam att hantera tilldelningen av organisationers skissdefinitioner, men inte möjligheten att ändra dem. Att göra det kräver viss konfiguration i din Azure-miljö och den här artikeln förklarar de nödvändiga stegen.

## <a name="grant-permission-to-the-blueprint-operator"></a>Bevilja behörighet till blueprint-operatorn

Det första steget är att ge rollen **Blueprint Operator** till kontot eller säkerhetsgruppen (rekommenderas) som kommer att tilldela ritningar. Den här åtgärden ska göras på högsta nivå i hanteringsgrupphierarkin som omfattar alla hanteringsgrupper och prenumerationer som operationsgruppen ska ha åtkomst till skisstilldelning. Vi rekommenderar att du följer principen om lägsta behörighet när du beviljar dessa behörigheter.

1. (Rekommenderas) [Skapa en säkerhetsgrupp och lägga till medlemmar](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Lägga till en rolltilldelning](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) av **Blueprint Operator** i kontot eller säkerhetsgruppen

## <a name="user-assign-managed-identity"></a>Användartilldelar hanterad identitet

En skissdefinition kan använda antingen systemtilldelade eller användartilldelade hanterade identiteter. När du använder rollen **Blueprint Operator** måste dock skissdefinitionen konfigureras för att använda en användartilldelad hanterad identitet. Dessutom måste kontot eller säkerhetsgruppen som beviljas rollen **Blueprint Operator** beviljas rollen **Hanterad identitetsoperator** på den användartilldelade hanterade identitetsidentiteten. Utan den här behörigheten misslyckas skisstilldelningar på grund av brist på behörigheter.

1. [Skapa en användartilldelad hanterad identitet](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) som ska användas av en tilldelad skiss

1. [Lägg till en rolltilldelning](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) av **Managed Identity Operator** i kontot eller säkerhetsgruppen. Begränsa rolltilldelningen till den nya användartilldelade hanterade identiteten.

1. Som **skissoperator** [tilldelar du en skiss](../create-blueprint-portal.md#assign-a-blueprint) som använder den nya användartilldelade hanterade identiteten.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [skisslivscykeln](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../concepts/resource-locking.md).
- Lös problem under tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).