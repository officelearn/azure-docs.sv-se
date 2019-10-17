---
title: Visa, lägga till och ta bort tilldelningar för ett Access-paket i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig att visa, lägga till och ta bort tilldelningar för ett Access-paket i Azure Active Directory rättighets hantering (för hands version).
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
ms.openlocfilehash: 694c8866a69d8488511be1670ba3d3013a4a3423
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392519"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Visa, lägga till och ta bort tilldelningar för ett Access-paket i Azure AD-hantering av rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I hantering av Azure AD-rättigheter kan du se vem som har tilldelats åtkomst till paket, deras principer och status. Om ett Access-paket har en lämplig princip kan du också direkt tilldela användare till ett Access-paket. Den här artikeln beskriver hur du visar, lägger till och tar bort tilldelningar för ett åtkomst paket.

## <a name="view-who-has-an-assignment"></a>Visa vem som har en tilldelning

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **tilldelningar** för att visa en lista över aktiva tilldelningar.

    ![Lista över tilldelningar till ett Access-paket](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Klicka på en uppgift om du vill visa mer information.

1. Om du vill se en lista över tilldelningar som inte har alla resurs roller korrekt etablerade klickar du på filter status och väljer **leverera**.

    Du kan se ytterligare information om leverans fel genom att leta upp användarens motsvarande begäran på sidan **begär Anden** .

1. Om du vill se förfallna tilldelningar klickar du på filter status och väljer **upphör att gälla**.

1. Klicka på **Hämta**om du vill ladda ned en CSV-fil för den filtrerade listan.

## <a name="directly-assign-a-user"></a>Tilldela en användare direkt

I vissa fall kanske du vill tilldela vissa användare direkt till ett Access-paket så att användarna inte behöver gå igenom processen för att begära åtkomst paketet. För att kunna tilldela användare direkt måste åtkomst paketet ha en princip som tillåter administratörs direkta tilldelningar.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **tilldelningar**på den vänstra menyn.

1. Klicka på **ny tilldelning** för att öppna Lägg till användare för att komma åt paketet.

    ![Tilldelningar – Lägg till användare till Access-paket](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Klicka på **Lägg till användare** för att välja de användare som du vill tilldela åtkomst paketet till.

1. I listan **Välj princip** väljer du en princip som har inställningen [Ingen (endast administratörers direkta tilldelningar)](entitlement-management-access-package-request-policy.md#none-administrator-direct-assignments-only) .

    Om det här åtkomst paketet inte har den här typen av princip kan du klicka på **Skapa ny princip** för att lägga till ett.

1. Ange datum och tid då du vill att de valda användarnas tilldelning ska starta och sluta. Om inget slutdatum anges används principens livs cykel inställningar.

1. Du kan också ange en motivering för den direkta tilldelningen för post hållning.

1. Klicka på **Lägg till** för att tilldela de valda användarna direkt till åtkomst paketet.

    Efter en liten stund klickar du på **Uppdatera** för att se användarna i listan tilldelningar.

## <a name="next-steps"></a>Nästa steg

- [Ändringsbegäran och inställningar för ett Access-paket](entitlement-management-access-package-request-policy.md)
- [Visa rapporter och loggar](entitlement-management-reports.md)
