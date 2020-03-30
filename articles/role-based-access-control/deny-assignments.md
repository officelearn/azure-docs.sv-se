---
title: Förstå neka tilldelningar för Azure-resurser
description: Lär dig mer om att neka tilldelningar i rollbaserad åtkomstkontroll (RBAC) för Azure-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: db249ccde1026cd468a1c30942891119482697ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372487"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Förstå neka tilldelningar för Azure-resurser

I likhet med en rolltilldelning kopplar en *neka tilldelning* en uppsättning neka åtgärder till en användare, grupp eller tjänsthuvudnamn i ett visst scope i syfte att neka åtkomst. Neka tilldelningar blockera användare från att utföra specifika Azure-resursåtgärder även om en rolltilldelning ger dem åtkomst.

I den här artikeln beskrivs hur neka tilldelningar definieras.

## <a name="how-deny-assignments-are-created"></a>Hur neka tilldelningar skapas

Neka tilldelningar skapas och hanteras av Azure för att skydda resurser. Azure Blueprints och Azure-hanterade appar använder neka tilldelningar för att skydda systemhanterade resurser. Azure Blueprints och Azure-hanterade appar är det enda sättet att neka tilldelningar kan skapas. Du kan inte direkt skapa dina egna neka-tilldelningar. Mer information om hur Skissar använder neka tilldelningar för att låsa resurser finns [i Förstå resurslåsning i Azure Blueprints](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Du kan inte direkt skapa dina egna neka-tilldelningar.

## <a name="compare-role-assignments-and-deny-assignments"></a>Jämföra rolltilldelningar och neka tilldelningar

Neka tilldelningar följer ett liknande mönster som rolltilldelningar, men har också vissa skillnader.

| Funktion | Rolltilldelning | Neka tilldelning |
| --- | --- | --- |
| Bevilja åtkomst | :heavy_check_mark: |  |
| Neka åtkomst |  | :heavy_check_mark: |
| Kan skapas direkt | :heavy_check_mark: |  |
| Ansök i ett scope | :heavy_check_mark: | :heavy_check_mark: |
| Exkludera huvudmän |  | :heavy_check_mark: |
| Förhindra arv till underordnade scope |  | :heavy_check_mark: |
| Använd på [klassiska prenumerationsadministratörstilldelningar](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Neka tilldelningsegenskaper

 En neka tilldelning har följande egenskaper:

> [!div class="mx-tableFixed"]
> | Egenskap | Krävs | Typ | Beskrivning |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ja | String | Visningsnamnet för neka tilldelningen. Namn måste vara unika för ett visst scope. |
> | `Description` | Inga | String | Beskrivningen av neka tilldelning. |
> | `Permissions.Actions` | Minst en åtgärd eller en DataActions | Sträng[] | En matris med strängar som anger de hanteringsåtgärder som neka tilldelningen blockerar åtkomst till. |
> | `Permissions.NotActions` | Inga | Sträng[] | En matris med strängar som anger vilka hanteringsåtgärder som ska uteslutas från neka-tilldelningen. |
> | `Permissions.DataActions` | Minst en åtgärd eller en DataActions | Sträng[] | En matris med strängar som anger de dataåtgärder som neka tilldelningen blockerar åtkomst till. |
> | `Permissions.NotDataActions` | Inga | Sträng[] | En matris med strängar som anger vilka dataåtgärder som ska uteslutas från neka-tilldelningen. |
> | `Scope` | Inga | String | En sträng som anger det scope som neka tilldelningen gäller för. |
> | `DoNotApplyToChildScopes` | Inga | Boolean | Anger om neka tilldelningen gäller för underordnade scope. Standardvärdet är falskt. |
> | `Principals[i].Id` | Ja | Sträng[] | En matris med Azure AD-huvudobjekt-ID:er (användare, grupp, tjänsthuvudnamn eller hanterad identitet) som neka tilldelningen gäller. Ange till ett `00000000-0000-0000-0000-000000000000` tomt GUID för att representera alla huvudnamn. |
> | `Principals[i].Type` | Inga | Sträng[] | En matris med objekttyper som representeras av Huvudnamn[i].Id. Ange att `SystemDefined` representera alla huvudnamn. |
> | `ExcludePrincipals[i].Id` | Inga | Sträng[] | En matris med Azure AD-huvudobjekt-ID:er (användare, grupp, tjänsthuvudnamn eller hanterad identitet) som neka tilldelningen inte gäller för. |
> | `ExcludePrincipals[i].Type` | Inga | Sträng[] | En matris med objekttyper som representeras av ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Inga | Boolean | Anger om den här neka tilldelningen har skapats av Azure och inte kan redigeras eller tas bort. För närvarande är alla neka tilldelningar systemskyddade. |

## <a name="the-all-principals-principal"></a>De viktigaste rektorerna

För att stödja neka tilldelningar har ett systemdefinierat huvudnamn med namnet *Alla huvudmän introducerats.* Det här huvudobjektet representerar alla användare, grupper, tjänsthuvudnamn och hanterade identiteter i en Azure AD-katalog. Om huvud-ID:et `00000000-0000-0000-0000-000000000000` är ett noll-GUID och huvudtypen är, `SystemDefined`representerar huvudmannen alla huvudnamn. I Azure PowerShell-utdata ser alla huvudnamn ut så här:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Alla huvudmän kan `ExcludePrincipals` kombineras med för att neka alla huvudmän utom vissa användare. Alla huvudmän har följande begränsningar:

- Kan endast användas `Principals` i och `ExcludePrincipals`kan inte användas i .
- `Principals[i].Type`måste ställas `SystemDefined`in på .

## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Skydda nya resurser med Azure Blueprints-resurslås](../governance/blueprints/tutorials/protect-new-resources.md)
* [Lista neka tilldelningar för Azure-resurser med Azure-portalen](deny-assignments-portal.md)
