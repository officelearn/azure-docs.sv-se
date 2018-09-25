---
title: Förstå neka tilldelningar i Azure RBAC | Microsoft Docs
description: Läs mer om neka tilldelningar med rollbaserad åtkomstkontroll (RBAC) för resurser i Azure.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980176"
---
# <a name="understand-deny-assignments"></a>Förstå neka tilldelningar

Liknar en rolltilldelning en *neka tilldelning* binds en uppsättning Neka-åtgärder till en användare, grupp eller tjänstens huvudnamn i ett visst omfång i syfte att åtkomst nekas. En tilldelning av neka kan också utesluta huvudnamn och förhindra arv till underordnade omfattningar som skiljer sig från rolltilldelningar. För närvarande kan neka-tilldelningar **skrivskyddad** och kan bara ställas in av Azure. Den här artikeln beskrivs hur neka tilldelningar har definierats.

## <a name="deny-assignment-properties"></a>Neka rolltilldelningens egenskaper

 En neka-tilldelning har följande egenskaper:

> [!div class="mx-tableFixed"]
> | Egenskap  | Krävs | Typ | Beskrivning |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ja | Sträng | Visningsnamnet för neka tilldelningen. Namn måste vara unikt för ett givet omfång. |
> | `Description` | Nej | Sträng | Beskrivning av neka tilldelningen. |
> | `Permissions.Actions` | Minst en åtgärder eller en DataActions | String[] | En matris med strängar som anger vilka hanteringsåtgärder som blockerar tilldelningen neka åtkomst. |
> | `Permissions.NotActions` | Nej | String[] | En matris med strängar som anger vilka hanteringsåtgärder som ska undantas från neka-tilldelning. |
> | `Permissions.DataActions` | Minst en åtgärder eller en DataActions | String[] | En matris med strängar som anger de åtgärder som blockerar tilldelningen neka åtkomst. |
> | `Permissions.NotDataActions` | Nej | String[] | En matris med strängar som anger en dataåtgärd att exkludera från tilldelningen neka. |
> | `Scope` | Nej | Sträng | En sträng som anger den omfattning som neka tilldelningen gäller för. |
> | `DoNotApplyToChildScopes` | Nej | Boolesk | Anger om neka tilldelningen gäller för underordnade omfång. Standardvärdet är FALSKT. |
> | `Principals[i].Id` | Ja | String[] | En matris med Azure AD-huvudnamn objekt ID: N (användare, grupp eller tjänstens huvudnamn) som gäller för neka tilldelningen. Inställt på ett tomt GUID `00000000-0000-0000-0000-000000000000` att representera alla. |
> | `Principals[i].Type` | Nej | String[] | En matris med objekt av typen representeras av säkerhetsobjekt [i] .id. Ange `Everyone` att representera alla. |
> | `ExcludePrincipals[i].Id` | Nej | String[] | En matris med Azure AD-huvudnamn objekt ID: N (användare, grupp eller tjänstens huvudnamn) som inte gäller neka tilldelningen. |
> | `ExcludePrincipals[i].Type` | Nej | String[] | En matris med objekt av typen representeras av ExcludePrincipals [i] .id. |
> | `IsSystemProtected` | Nej | Boolesk | Anger om det här neka tilldelning har skapats av Azure och inte kan redigeras eller tas bort. För närvarande kan neka alla tilldelningar är systemet som skyddas. |

## <a name="everyone-principal"></a>Alla huvudnamn

Till stöd för neka tilldelningar, gruppen Alla huvudnamn har introducerats. Alla huvudnamn representerar alla användare, grupper och tjänstens huvudnamn i Azure AD-katalog. Om ägar-ID är ett noll GUID `00000000-0000-0000-0000-000000000000` och huvudnamn är en `Everyone`, huvudkontot som representerar alla. Alla huvudnamn kan kombineras med `ExcludePrincipals` att neka alla utom vissa användare. Alla huvudnamn har följande begränsningar:

- Kan användas endast i `Principals` och kan inte användas i `ExcludePrincipals`.
- `Principals[i].Type` måste anges till `Everyone`.

## <a name="next-steps"></a>Nästa steg

* [Lista neka tilldelningar med RBAC och REST-API](deny-assignments-rest.md)
* [Förstå rolldefinitioner](role-definitions.md)
