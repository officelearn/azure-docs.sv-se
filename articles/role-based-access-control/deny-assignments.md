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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 53716fa343df25026dcc668ed8483673d934d1ad
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339132"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Förstå neka tilldelningar för Azure-resurser

Liknar en rolltilldelning en *neka tilldelning* bifogar en uppsättning Neka-åtgärder till en användare, grupp eller tjänstens huvudnamn i ett visst omfång i syfte att åtkomst nekas. Neka tilldelningar blockera användare från att utföra åtgärder för specifika Azure-resurs, även om en rolltilldelning ger dem åtkomst. Vissa resource providers i Azure innehåller nu neka tilldelningar. För närvarande kan neka-tilldelningar **skrivskyddad** och kan bara ställas in av Microsoft.

Neka tilldelningar skiljer sig från rolltilldelningar på vissa sätt. Neka tilldelningar kan utesluta huvudnamn och förhindra arvet till underordnade omfång. Neka tilldelningar gäller även för [klassisk prenumerationsadministratör](rbac-and-directory-admin-roles.md) tilldelningar.

Den här artikeln beskrivs hur neka tilldelningar har definierats.

## <a name="deny-assignment-properties"></a>Neka rolltilldelningens egenskaper

 En neka-tilldelning har följande egenskaper:

> [!div class="mx-tableFixed"]
> | Egenskap  | Krävs | Typ | Beskrivning |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ja | String | Visningsnamnet för neka tilldelningen. Namn måste vara unikt för ett givet omfång. |
> | `Description` | Nej | String | Beskrivning av neka tilldelningen. |
> | `Permissions.Actions` | Minst en åtgärder eller en DataActions | String[] | En matris med strängar som anger vilka hanteringsåtgärder som blockerar tilldelningen neka åtkomst. |
> | `Permissions.NotActions` | Nej | String[] | En matris med strängar som anger vilka hanteringsåtgärder som ska undantas från neka-tilldelning. |
> | `Permissions.DataActions` | Minst en åtgärder eller en DataActions | String[] | En matris med strängar som anger de åtgärder som blockerar tilldelningen neka åtkomst. |
> | `Permissions.NotDataActions` | Nej | String[] | En matris med strängar som anger en dataåtgärd att exkludera från tilldelningen neka. |
> | `Scope` | Nej | String | En sträng som anger den omfattning som neka tilldelningen gäller för. |
> | `DoNotApplyToChildScopes` | Nej | Boolesk | Anger om neka tilldelningen gäller för underordnade omfång. Standardvärdet är FALSKT. |
> | `Principals[i].Id` | Ja | String[] | En matris med Azure AD-huvudnamn objekt ID: N (användare, grupp, tjänstens huvudnamn eller hanterad identitet) som gäller för neka tilldelningen. Inställt på ett tomt GUID `00000000-0000-0000-0000-000000000000` att representera alla säkerhetsobjekt. |
> | `Principals[i].Type` | Nej | String[] | En matris med objekt av typen representeras av säkerhetsobjekt [i] .id. Ange `SystemDefined` att representera alla säkerhetsobjekt. |
> | `ExcludePrincipals[i].Id` | Nej | String[] | En matris med Azure AD-huvudnamn objekt ID: N (användare, grupp, tjänstens huvudnamn eller hanterad identitet) som inte gäller neka tilldelningen. |
> | `ExcludePrincipals[i].Type` | Nej | String[] | En matris med objekt av typen representeras av ExcludePrincipals [i] .id. |
> | `IsSystemProtected` | Nej | Boolesk | Anger om det här neka tilldelning har skapats av Azure och inte kan redigeras eller tas bort. För närvarande kan neka alla tilldelningar är systemet som skyddas. |

## <a name="system-defined-principal"></a>System-Defined Principal

Till stöd för neka tilldelningar, den **systemdefinierade huvudnamn** har introducerats. Den här huvudnamn representerar alla användare, grupper, tjänstens huvudnamn och hanterade identiteter i Azure AD-katalog. Om ägar-ID är ett noll GUID `00000000-0000-0000-0000-000000000000` och huvudnamn är en `SystemDefined`, huvudkontot som representerar alla säkerhetsobjekt. `SystemDefined` kan kombineras med `ExcludePrincipals` att neka alla huvudkonton utom vissa användare. `SystemDefined` har följande begränsningar:

- Kan användas endast i `Principals` och kan inte användas i `ExcludePrincipals`.
- `Principals[i].Type` måste anges till `SystemDefined`.

## <a name="next-steps"></a>Nästa steg

* [Lista neka tilldelningar för Azure-resurser med hjälp av REST-API](deny-assignments-rest.md)
* [Förstå rolldefinitioner för Azure-resurser](role-definitions.md)
