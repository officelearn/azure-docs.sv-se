---
title: Förstå neka-tilldelningar för Azure-resurser
description: Lär dig mer om att neka tilldelningar i rollbaserad åtkomst kontroll (RBAC) för Azure-resurser.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80372487"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Förstå neka-tilldelningar för Azure-resurser

I likhet med en roll tilldelning bifogar en *neka-tilldelning* en uppsättning neka-åtgärder till en användare, grupp eller tjänstens huvud namn i ett visst omfång för att neka åtkomst. Neka tilldelningar blockera användare från att utföra vissa åtgärder för Azure-resurser även om en roll tilldelning ger dem åtkomst.

I den här artikeln beskrivs hur neka-tilldelningar definieras.

## <a name="how-deny-assignments-are-created"></a>Hur neka-tilldelningar skapas

Neka-tilldelningar skapas och hanteras av Azure för att skydda resurser. Azure-ritningar och Azure-hanterade appar använder neka-tilldelningar för att skydda systemhanterade resurser. Azure-ritningar och Azure-hanterade appar är det enda sättet som nekar tilldelningar kan skapas. Du kan inte skapa egna neka-tilldelningar direkt. Mer information om hur ritningar använder neka-tilldelningar för att låsa resurser finns i [förstå resurs låsning i Azure-ritningar](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Du kan inte skapa egna neka-tilldelningar direkt.

## <a name="compare-role-assignments-and-deny-assignments"></a>Jämför roll tilldelningar och neka tilldelningar

Neka tilldelningar följer ett liknande mönster som roll tilldelningar, men har också vissa skillnader.

| Funktion | Rolltilldelning | Neka tilldelning |
| --- | --- | --- |
| Bevilja åtkomst | :heavy_check_mark: |  |
| Neka åtkomst |  | :heavy_check_mark: |
| Kan skapas direkt | :heavy_check_mark: |  |
| Tillämpa i ett omfång | :heavy_check_mark: | :heavy_check_mark: |
| Exkludera huvud konton |  | :heavy_check_mark: |
| Förhindra arv av underordnade omfattningar |  | :heavy_check_mark: |
| Tillämpa på [Administratörs](rbac-and-directory-admin-roles.md) tilldelningar för klassisk prenumeration |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Egenskaper för neka tilldelning

 En neka-tilldelning har följande egenskaper:

> [!div class="mx-tableFixed"]
> | Egenskap | Krävs | Typ | Beskrivning |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ja | Sträng | Visnings namnet för neka-tilldelningen. Namn måste vara unika för ett angivet omfång. |
> | `Description` | Nej | Sträng | Beskrivningen av neka-tilldelningen. |
> | `Permissions.Actions` | Minst en åtgärd eller en DataActions | Sträng [] | En sträng mat ris som anger vilka hanterings åtgärder som neka tilldelning blockerar åtkomsten till. |
> | `Permissions.NotActions` | Nej | Sträng [] | En sträng mat ris som anger vilka hanterings åtgärder som ska undantas från neka-tilldelningen. |
> | `Permissions.DataActions` | Minst en åtgärd eller en DataActions | Sträng [] | En sträng mat ris som anger de data åtgärder som neka tilldelning blockerar åtkomsten till. |
> | `Permissions.NotDataActions` | Nej | Sträng [] | En sträng mat ris som anger de data åtgärder som ska undantas från neka-tilldelningen. |
> | `Scope` | Nej | Sträng | En sträng som anger omfattningen som neka-tilldelningen gäller för. |
> | `DoNotApplyToChildScopes` | Nej | Boolesk | Anger om neka-tilldelningen gäller för underordnade omfång. Standardvärdet är false. |
> | `Principals[i].Id` | Ja | Sträng [] | En matris med objekt-ID: n för Azure AD-huvudobjektet (användare, grupp, tjänstens huvud namn eller hanterad identitet) som neka-tilldelningen gäller. Ange ett tomt GUID `00000000-0000-0000-0000-000000000000` som representerar alla huvud konton. |
> | `Principals[i].Type` | Nej | Sträng [] | En matris med objekt typer som representeras av huvud konton [i]. ID `SystemDefined` . anges till som representerar alla huvud konton. |
> | `ExcludePrincipals[i].Id` | Nej | Sträng [] | En matris med objekt-ID: n för Azure AD-huvudobjektet (användare, grupp, tjänstens huvud namn eller hanterad identitet) som neka-tilldelningen inte gäller för. |
> | `ExcludePrincipals[i].Type` | Nej | Sträng [] | En matris med objekt typer som representeras av ExcludePrincipals [i]. ID. |
> | `IsSystemProtected` | Nej | Boolesk | Anger om den här nekande tilldelningen skapades av Azure och inte kan redige ras eller tas bort. För närvarande är alla nekade tilldelningar skyddade av systemet. |

## <a name="the-all-principals-principal"></a>Huvudobjektet alla huvud konton

För att stödja neka-tilldelningar har ett Systemdefinierat objekt med namnet *alla huvud konton* introducerats. Detta huvud konto representerar alla användare, grupper, tjänstens huvud namn och hanterade identiteter i en Azure AD-katalog. Om ägar-ID: t är noll `00000000-0000-0000-0000-000000000000` GUID och huvud typen är `SystemDefined`, representerar huvudobjektet alla huvud konton. I Azure PowerShell utdata ser alla huvud konton ut så här:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Alla huvud konton kan kombineras med `ExcludePrincipals` för att neka alla huvud konton förutom vissa användare. Alla huvud konton har följande begränsningar:

- Kan endast användas i `Principals` och kan inte användas i. `ExcludePrincipals`
- `Principals[i].Type`måste anges till `SystemDefined`.

## <a name="next-steps"></a>Nästa steg

* [Självstudie: skydda nya resurser med resurs lås för Azure-ritningar](../governance/blueprints/tutorials/protect-new-resources.md)
* [Visa lista över nekade tilldelningar för Azure-resurser med hjälp av Azure Portal](deny-assignments-portal.md)
