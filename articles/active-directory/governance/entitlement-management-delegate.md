---
title: Delegera uppgifter i Azure AD-rättigheten hantering (förhandsversion) – Azure Active Directory
description: Läs mer om de roller som du kan tilldela för att delegera aktiviteter i Azure Active Directory rättigheten management.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa0be8e2af7644564ba27e6d58fda09b1ae7bc7
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191504"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Delegera uppgifter i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Globala administratörer och användaradministratörer kan skapa och hantera alla aspekter av Azure AD rättigheten management som standard. Användare i rollerna kanske inte vet alla scenarier där åtkomst paket krävs. Det är vanligtvis användare inom avdelningar som vet som behöver samarbeta.

I stället för att ge obegränsad behörighet till icke-administratörer, kan du ge användarna lägsta behörigheter som de behöver för att utföra sitt arbete och undvika att skapa i konflikt eller olämplig åtkomsträttigheter. Den här artikeln beskrivs de roller som du kan tilldela till delegera olika uppgifter i rätt management.

## <a name="delegate-example-for-departmental-adoption"></a>Delegera exempel för avdelning

För att förstå hur du kan delegera uppgifter i rätt management, hjälper det att tänka på ett exempel. Anta att organisationen har följande fem användare:

| Användare | Avdelning | Anteckningar |
| --- | --- | --- |
| Alice | IT | Global administratör |
| Bob | Forskning | Bob är också ägare till en forskningsgrupp |
| Christina | Forskning |  |
| Dave | Marknadsföring |  |
| Elisa | Marknadsföring | ELISA är också ägare till ett program för marknadsföring |

Forskningen och marknadsföringsavdelningen du vill använda hantering av behörighet för sina användare. Alice är ännu inte klar för andra avdelningar som använder rättigheten management. Här är ett sätt att Alice kan delegera uppgifter så att forskningen och marknadsföringsavdelningen.

1. Alice skapar en ny Azure AD-säkerhetsgrupper för skapare av katalogen och lägger till Bob, Carol, Dave och Elisa som medlemmar i gruppen.

1. Alice använder hanteringsinställningar rättigheten att lägga till den gruppen till rollen catalog skapare.

1. Carol skapar en **Research** katalogisera och lägger till Bob som Medägare av katalogen. Bob lägger till gruppen research han äger till katalogen som en resurs, så att den kan användas i ett paket för åtkomst för research collaboration.

1. Dave skapar en **marknadsföring** katalogisera och lägger till Elisa som Medägare av katalogen. ELISA lägger till marknadsföring programmet hon äger till katalogen som en resurs, så att den kan användas i ett åtkomst-paket för marknadsföring samarbete.

Forskning och marknadsföring avdelningar kan nu använda rätt management. Bob, Carol, Dave och Elisa kan skapa och hantera åtkomst-paket i deras respektive kataloger.

![Berättigande management ombud exempel](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>Berättigande ledningsroller

Berättigande management har följande roller som är specifika för hantering av rättigheten.

| Roll | Beskrivning |
| --- | --- |
| Katalogens skapare | Skapa och hantera kataloger. Vanligtvis en IT-administratör som inte är en Global administratör eller en resursägare för en samling resurser. Den person som skapar en katalog automatiskt blir katalogens första catalog ägare och kan lägga till ytterligare catalog ägare. |
| Katalogen ägare | Redigera och hantera befintliga kataloger. Vanligtvis en IT-administratör eller resursägare, eller en användare som katalogen ägaren har definierat. |
| Åtkomst-Pakethanteraren | Redigera och hantera alla befintliga åtkomst-paket i en katalog. |

Dessutom kan ha en utsedda användare och en begärande av ett paket för åtkomst också rättigheter, även om de inte är roller.
 
* Godkännare: Behörighet för en princip för att godkänna eller neka förfrågningar om åtkomst till paket, men de kan inte ändra åtkomst paketet definitioner.
* Begärande: Behörighet för en princip för en åtkomst-paketet för att begära åtkomst till paketet.

I följande tabell visas de uppgifter som rollerna kan utföra.

| Aktivitet | Katalogens skapare | Katalogen ägare | Åtkomst-Pakethanteraren | Godkännare |
| --- | :---: | :---: | :---: | :---: |
| [Skapa en ny katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Lägg till en resurs till en katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Redigera en katalog](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Ta bort en katalog](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Lägga till en katalog ägare eller en pakethanterare för åtkomst till en katalog](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Skapa ett nytt åtkomst-paket i en katalog](entitlement-management-access-package-create.md) |  | :heavy_check_mark: |  |  |
| [Hantera resursroller i ett paket för åtkomst](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ange vem som kan begära ett åtkomst-paket](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Tilldela en användare direkt till ett åtkomst-paket](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Visa vem som har en tilldelning till ett åtkomst-paket](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Visa ett åtkomst-paket förfrågningar](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Visa en förfrågan leverans fel](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Avbryt en väntande begäran](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Dölj ett åtkomst-paket](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ta bort ett paket för åtkomst](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Godkänna en åtkomstbegäran](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Nödvändiga roller för att lägga till resurser i en katalog

En Global administratör kan lägga till eller ta bort valfri grupp (molnet – skapat säkerhetsgrupper eller molnet – skapat Office 365-grupper), programmet eller SharePoint Online-webbplats i en katalog. En användare med rollen kan lägga till eller ta bort gruppen eller programmet i en katalog.

För en användare som inte är en Global administratör eller en Användaradministratör för att lägga till grupper, program eller SharePoint Online-platser till en katalog som användaren måste ha *både* den krävs för Azure AD directory rollen och katalogisera ägare berättigande hanteringsroll. I följande tabell visas de roll kombinationer som krävs för att lägga till resurser i en katalog. Du måste ha samma roller för att ta bort resurser från en katalog.

| Azure AD-katalogroll | Roll för berättigande | Lägga till säkerhetsgruppen | Lägga till Office 365-grupp | Kan lägga till app | Lägga till SharePoint Online-webbplats |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Global administratör](../users-groups-roles/directory-assign-admin-roles.md) | Saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Användaradministratör](../users-groups-roles/directory-assign-admin-roles.md) | Saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogen ägare | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogen ägare |  | :heavy_check_mark: |  |  |
| [Team-tjänstadministratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogen ägare |  | :heavy_check_mark: |  |  |
| [SharePoint-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogen ägare |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Programadministratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogen ägare |  |  | :heavy_check_mark: |  |
| [Molnprogramadministratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogen ägare |  |  | :heavy_check_mark: |  |
| Användare | Katalogen ägare | Endast om gruppen ägare | Endast om gruppen ägare | Endast om appägare |  |

## <a name="add-a-catalog-creator"></a>Lägg till en katalog-skapare

Om du vill delegera skapar kataloger kan du lägga till användare till rollen catalog skapare.  Du kan lägga till enskilda användare, eller för bekvämlighet kan lägga till en grupp vars medlemmar kan sedan skapa kataloger. Följ dessa steg om du vill tilldela en användare till rollen catalog skapare.

**Nödvändiga roll:** Global administratör eller Användaradministratör

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn i den **rättigheten management** klickar du på **inställningar**.

1. Klicka på **Redigera**.

1. I den **delegera behörighet management** klickar du på **lägger du till katalogen skapare** att välja användare eller grupper som är medlemmar i den här rättigheten hanteringsroll.

1. Klicka på **Välj**.

1. Klicka på **Spara**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Lägga till en katalog ägare eller en åtkomst-Pakethanteraren

Om du vill delegera hanteringen av en katalog eller åtkomst paket i katalogen kan du lägga till användare till katalogen-ägare eller åtkomst package manager-roller. Den skapar en katalog blir den första katalog-ägaren. Följ dessa steg om du vill tilldela en användare till katalogen ägaren eller åtkomst package manager-roll.

**Nödvändiga roll:** Användaradministratör eller katalogen ägare

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **kataloger** och öppna sedan den katalog som du vill lägga till administratörer.

1. I den vänstra menyn klickar du på **roller och administratörer**.

1. Klicka på **lägga till ägare** eller **Lägg till åtkomst pakethanterare** välja medlemmar för dessa roller.

1. Klicka på **Välj** att lägga till dessa medlemmar.

## <a name="next-steps"></a>Nästa steg

- [Lägg till godkännare](entitlement-management-access-package-edit.md#policy-request)
- [Lägg till resurser till en katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
