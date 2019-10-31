---
title: Dölja eller ta bort ett Access-paket i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig att dölja eller ta bort ett Access-paket i Azure Active Directory rättighets hantering.
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
ms.openlocfilehash: 4391cd20f3919e8def28fa3c1d5007f91b2d9f0b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174759"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Dölja eller ta bort ett Access-paket i hantering av Azure AD-rättigheter

Åtkomst paketen kan identifieras som standard. Det innebär att om en princip tillåter att en användare begär åtkomst paketet, ser de automatiskt Access-paketet som anges i deras åtkomst Portal. Du kan dock ändra den **dolda** inställningen så att Access-paketet inte visas i användarens min åtkomst Portal.

I den här artikeln beskrivs hur du döljer eller tar bort ett Access-paket.

## <a name="change-the-hidden-setting"></a>Ändra den dolda inställningen

Följ dessa steg om du vill ändra den **dolda** inställningen för ett Access-paket.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. På sidan Översikt klickar du på **Redigera**.

1. Ange den **dolda** inställningen.

    Om värdet är **Nej**visas Access-paketet i användarens Mina åtkomst Portal.

    Om det är inställt på **Ja**visas inte Access-paketet i användarens min åtkomst Portal. Det enda sättet som en användare kan visa åtkomst paketet är om de har länken Direct **My Access Portal** till Access-paketet. Mer information finns i [Dela länk för att begära ett Access-paket](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Ta bort ett Access-paket

Ett Access-paket kan bara tas bort om det inte har några aktiva användar tilldelningar. Följ dessa steg om du vill ta bort ett Access-paket.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **tilldelningar** på den vänstra menyn och ta bort åtkomsten för alla användare.

1. I den vänstra menyn klickar du på **Översikt** och sedan på **ta bort**.

1. Klicka på **Ja**i det borttagnings meddelande som visas.

## <a name="next-steps"></a>Nästa steg

- [Visa, lägga till och ta bort tilldelningar för ett Access-paket](entitlement-management-access-package-assignments.md)
- [Visa rapporter och loggar](entitlement-management-reports.md)
