---
title: Förstå neka tilldelningar för Azure-resurser | Microsoft Docs
description: Läs mer om neka tilldelningar med rollbaserad åtkomstkontroll (RBAC) för Azure-resurser.
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
ms.date: 06/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: f15d6fd81337aa4a859539e86f37a516848c9370
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165988"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Förstå neka tilldelningar för Azure-resurser

Liknar en rolltilldelning en *neka tilldelning* bifogar en uppsättning Neka-åtgärder till en användare, grupp eller tjänstens huvudnamn i ett visst omfång i syfte att åtkomst nekas. Neka tilldelningar blockera användare från att utföra åtgärder för specifika Azure-resurs, även om en rolltilldelning ger dem åtkomst.

Den här artikeln beskrivs hur neka tilldelningar har definierats.

## <a name="how-deny-assignments-are-created"></a>Hur neka tilldelningar har skapats

Neka tilldelningar skapas och hanteras av Azure för att skydda resurser. Till exempel Azure skisser och Azure-hanterade appar använda neka tilldelningar för att skydda resurser som hanteras av datorn. Mer information finns i [skydda nya resurser med Azure skisser resurslås](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="compare-role-assignments-and-deny-assignments"></a>Jämför rolltilldelningar och neka tilldelningar

Neka tilldelningar följer ett liknande mönster som rolltilldelningar, men även ha några skillnader.

| Funktion | Rolltilldelning | Neka tilldelning |
| --- | --- | --- |
| Bevilja åtkomst | :heavy_check_mark: |  |
| Neka åtkomst |  | :heavy_check_mark: |
| Kan skapas direkt | :heavy_check_mark: |  |
| Gäller för ett omfång | :heavy_check_mark: | :heavy_check_mark: |
| Exkludera huvudnamn |  | :heavy_check_mark: |
| Förhindra arv till underordnade omfattningar |  | :heavy_check_mark: |
| Gäller för [klassisk prenumerationsadministratör](rbac-and-directory-admin-roles.md) tilldelningar |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Neka rolltilldelningens egenskaper

 En neka-tilldelning har följande egenskaper:

> [!div class="mx-tableFixed"]
> | Egenskap | Krävs | Typ | Beskrivning |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ja | String | Visningsnamnet för neka tilldelningen. Namn måste vara unikt för ett givet omfång. |
> | `Description` | Nej | String | Beskrivning av neka tilldelningen. |
> | `Permissions.Actions` | Minst en åtgärder eller en DataActions | String[] | En matris med strängar som anger vilka hanteringsåtgärder som blockerar tilldelningen neka åtkomst. |
> | `Permissions.NotActions` | Nej | String[] | En matris med strängar som anger vilka hanteringsåtgärder som ska undantas från neka-tilldelning. |
> | `Permissions.DataActions` | Minst en åtgärder eller en DataActions | String[] | En matris med strängar som anger de åtgärder som blockerar tilldelningen neka åtkomst. |
> | `Permissions.NotDataActions` | Nej | String[] | En matris med strängar som anger en dataåtgärd att exkludera från tilldelningen neka. |
> | `Scope` | Nej | String | En sträng som anger den omfattning som neka tilldelningen gäller för. |
> | `DoNotApplyToChildScopes` | Nej | Boolean | Anger om neka tilldelningen gäller för underordnade omfång. Standardvärdet är FALSKT. |
> | `Principals[i].Id` | Ja | String[] | En matris med Azure AD-huvudnamn objekt ID: N (användare, grupp, tjänstens huvudnamn eller hanterad identitet) som gäller för neka tilldelningen. Inställt på ett tomt GUID `00000000-0000-0000-0000-000000000000` att representera alla säkerhetsobjekt. |
> | `Principals[i].Type` | Nej | String[] | En matris med objekt av typen representeras av säkerhetsobjekt [i] .id. Ange `SystemDefined` att representera alla säkerhetsobjekt. |
> | `ExcludePrincipals[i].Id` | Nej | String[] | En matris med Azure AD-huvudnamn objekt ID: N (användare, grupp, tjänstens huvudnamn eller hanterad identitet) som inte gäller neka tilldelningen. |
> | `ExcludePrincipals[i].Type` | Nej | String[] | En matris med objekt av typen representeras av ExcludePrincipals [i] .id. |
> | `IsSystemProtected` | Nej | Boolean | Anger om det här neka tilldelning har skapats av Azure och inte kan redigeras eller tas bort. För närvarande kan neka alla tilldelningar är systemet som skyddas. |

## <a name="the-all-principals-principal"></a>Alla säkerhetsobjekt huvudnamn

Till stöd för neka tilldelningar, ett systemdefinierade huvudnamn med namnet *alla huvudkonton* har introducerats. Den här huvudnamn representerar alla användare, grupper, tjänstens huvudnamn och hanterade identiteter i Azure AD-katalog. Om ägar-ID är ett noll GUID `00000000-0000-0000-0000-000000000000` och huvudnamn är en `SystemDefined`, huvudkontot som representerar alla säkerhetsobjekt. I Azure PowerShell-utdata, alla huvudkonton som ser ut som följande:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Alla säkerhetsobjekt kan kombineras med `ExcludePrincipals` att neka alla huvudkonton utom vissa användare. Alla huvudkonton har följande begränsningar:

- Kan användas endast i `Principals` och kan inte användas i `ExcludePrincipals`.
- `Principals[i].Type` måste anges till `SystemDefined`.

## <a name="next-steps"></a>Nästa steg

* [Lista neka tilldelningar för Azure-resurser med Azure portal](deny-assignments-portal.md)
* [Förstå rolldefinitioner för Azure-resurser](role-definitions.md)
