---
title: Dölja eller ta bort åtkomstpaket i rättighetshantering - Azure AD
description: Lär dig hur du döljer eller tar bort ett åtkomstpaket i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262001"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Dölja eller ta bort ett åtkomstpaket i Azure AD-berättigandehantering

Åtkomstpaket kan identifieras som standard. Det innebär att om en princip tillåter en användare att begära åtkomstpaketet, kommer de automatiskt att se åtkomstpaketet som anges i min åtkomstportal. Du kan dock ändra inställningen **Dold** så att åtkomstpaketet inte visas i användarens My Access-portal.

I den här artikeln beskrivs hur du döljer eller tar bort ett åtkomstpaket.

## <a name="change-the-hidden-setting"></a>Ändra inställningen Dold

Följ dessa steg för att ändra inställningen **Dold** för ett åtkomstpaket.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Redigera**på sidan Översikt.

1. Ställ in inställningen **Dold.**

    Om det är inställt på **Nej**visas åtkomstpaketet i användarens My Access-portal.

    Om det är inställt på **Ja**visas inte åtkomstpaketet i användarens My Access-portal. Det enda sättet för en användare att visa åtkomstpaketet är om de har den direkta **My Access-portallänken** till åtkomstpaketet. Mer information finns i [Dela länk för att begära ett åtkomstpaket](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Ta bort ett åtkomstpaket

Ett åtkomstpaket kan bara tas bort om det inte har några aktiva användartilldelningar. Följ dessa steg för att ta bort ett åtkomstpaket.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Tilldelningar** på den vänstra menyn och ta bort åtkomst för alla användare.

1. Klicka på **Översikt** på den vänstra menyn och sedan på **Ta bort**.

1. Klicka på **Ja**i borttagningsmeddelandet som visas .

## <a name="next-steps"></a>Nästa steg

- [Visa, lägga till och ta bort tilldelningar för ett åtkomstpaket](entitlement-management-access-package-assignments.md)
- [Visa rapporter och loggar](entitlement-management-reports.md)
