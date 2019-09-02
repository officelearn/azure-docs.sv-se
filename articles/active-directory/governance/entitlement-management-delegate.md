---
title: Delegera aktiviteter i hantering av Azure AD-rättigheter (för hands version) – Azure Active Directory
description: Lär dig mer om de roller som du kan tilldela för att delegera aktiviteter i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b631f078240821e79513c4bd944a33b4725bc52
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207149"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Delegera aktiviteter i hantering av Azure AD-rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Globala administratörer och användar administratörer kan som standard skapa och hantera alla aspekter av hantering av Azure AD-rättigheter. Användare i dessa roller kanske inte känner till alla scenarier där åtkomst paket krävs. Vanligt vis är det användare inom avdelningar som vet vem som behöver samar beta. 

I stället för att ge användarna obegränsad behörighet till icke-administratörer, kan du ge användarna minst de behörigheter de behöver för att utföra sitt jobb och undvika att skapa motstridiga eller olämpliga åtkomst rättigheter. Den här artikeln beskriver de roller som du kan tilldela för att delegera olika aktiviteter i hantering av rättigheter. 

## <a name="delegate-example-for-departmental-adoption"></a>Ombuds exempel för att införa en avdelning

För att förstå hur du kan delegera aktiviteter i hantering av rättigheter, kan det vara ett exempel. 

Anta att din organisation har följande fem användare:

| Användare | Avdelning | Anteckningar |
| --- | --- | --- |
| Alice | it | Global administratör |
| Bob | Forskning | Bob är också ägare av en Research grupp |
| Carole | Forskning |  |
| Dave | Marknadsföring |  |
| Elisa | Marknadsföring | Elisa är också ägare till ett marknadsförings program |

Både forskar-och marknadsförings avdelningen vill använda rättighets hantering för sina användare. Alice är ännu inte redo för andra avdelningar att använda rättighets hantering. Här är ett sätt som Alice kan delegera uppgifter till forskar-och marknadsförings avdelningarna.

1. Alice skapar en ny Azure AD-säkerhetsgrupp för katalog skapare och lägger till Bob, Carol, Dave och Elisa som medlemmar i gruppen.

1. Alice använder inställningarna för rättighets hantering för att lägga till den gruppen i katalogens skapare roll.

1. Carol skapar en **forsknings** katalog och lägger till Bob som en medägare till katalogen. Bob lägger till den Research grupp han äger till katalogen som en resurs, så att den kan användas i ett Access-paket för forsknings samarbete.

1. Dave skapar en **marknadsförings** katalog och lägger till Elisa som en del ägare av katalogen. Elisa lägger till marknadsförings programmet hon äger till katalogen som en resurs, så att den kan användas i ett Access-paket för marknadsförings samarbete.

Nu kan forsknings-och marknadsförings avdelningen använda hantering av rättigheter. Bob, Carol, Dave och ELISA kan skapa och hantera åtkomst paket i sina respektive kataloger.

![Ombuds exempel för rättighets hantering](./media/entitlement-management-delegate/elm-delegate.png)


## <a name="entitlement-management-roles"></a>Rättighets hanterings roller

Rättighets hantering har följande roller som är speciella för rättighets hantering.

| Role | Beskrivning |
| --- | --- |
| Katalog skapare | Skapa och hantera kataloger. Vanligt vis en IT-administratör som inte är en global administratör eller en resurs ägare för en samling resurser. Personen som skapar en katalog blir automatiskt katalogens första katalog ägare och kan lägga till ytterligare katalog ägare. |
| Katalog ägare | Redigera och hantera befintliga kataloger. Vanligt vis en IT-administratör eller resurs ägare, eller en användare som har tilldelats katalog ägaren. |
| Åtkomst till paket hanteraren | Redigera och hantera alla befintliga åtkomst paket i en katalog. |

Dessutom har en utsedd god kännare och en beställare av ett Access-paket även rättigheter, även om de inte är roller.
 
* God kännare Auktoriserad av en princip för att godkänna eller neka begär Anden om åtkomst till paket, även om de inte kan ändra definitioner för åtkomst paket.
* Begär Ande Auktoriserad av en princip för ett Access-paket för att begära det Access-paketet.

I följande tabell visas de aktiviteter som dessa roller kan utföra.

| Uppgift | Katalog skapare | Katalog ägare | Åtkomst till paket hanteraren | Godkännare |
| --- | :---: | :---: | :---: | :---: |
| [Skapa en ny katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Lägga till en resurs i en katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Redigera en katalog](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Ta bort en katalog](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Lägg till en katalog ägare eller en Access Package Manager i en katalog](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Skapa ett nytt Access-paket i en katalog](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Hantera resurs roller i ett Access-paket](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Skapa och redigera principer](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Tilldela en användare direkt till ett Access-paket](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Visa vem som har en tilldelning till ett Access-paket](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Visa ett Access-pakets begär Anden](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Visa leverans fel för en begäran](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Avbryt en väntande begäran](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Dölj ett Access-paket](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Ta bort ett Access-paket](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Godkänn en åtkomstbegäran](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Nödvändiga roller för att lägga till resurser i en katalog

En global administratör kan lägga till eller ta bort alla grupper (molnbaserade säkerhets grupper eller molnbaserade Office 365-grupper), program eller SharePoint Online-platser i en katalog. En användar administratör kan lägga till eller ta bort valfri grupp eller program i en katalog.

För en användare som inte är en global administratör eller en användar administratör, för att lägga till grupper, program eller SharePoint Online-webbplatser till en katalog, måste användaren ha *både* den nödvändiga Azure AD-katalog rollen och katalog ägaren rättighets hanterings roll. I följande tabell visas de roll kombinationer som krävs för att lägga till resurser i en katalog. Om du vill ta bort resurser från en katalog måste du ha samma roller.

| Azure AD-katalog roll | Rättighets hanterings roll | Kan lägga till säkerhets grupp | Kan lägga till Office 365-gruppen | Kan lägga till app | Kan lägga till SharePoint Online-webbplats |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Global administratör](../users-groups-roles/directory-assign-admin-roles.md) | Saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Användar administratör](../users-groups-roles/directory-assign-admin-roles.md) | Saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  | :heavy_check_mark: |  |  |
| [Team tjänst administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  | :heavy_check_mark: |  |  |
| [SharePoint-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Program administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  |  | :heavy_check_mark: |  |
| [Moln program administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalog ägare |  |  | :heavy_check_mark: |  |
| Användare | Katalog ägare | Endast om grupp ägare | Endast om grupp ägare | Endast om app-ägare |  |

Om du vill fastställa den lägsta privilegierade rollen för en aktivitet kan du också referera till [Administratörs roller efter administratörs uppgift i Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="add-a-catalog-creator"></a>Lägg till en katalog skapare

Om du vill delegera skapandet av katalogen lägger du till användare i katalogens skapare roll.  Du kan lägga till enskilda användare eller så kan du lägga till en grupp, vars medlemmar sedan kan skapa kataloger. Följ dessa steg om du vill tilldela en användare rollen som katalog skapare.

**Nödvändig roll:** Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. På den vänstra menyn i avsnittet **rättighets hantering** klickar du på **Inställningar**.

1. Klicka på **Redigera**.

1. I avsnittet **delegera rättighets hantering** klickar du på **Lägg till katalog skapare** för att välja de användare eller grupper som ska vara medlemmar i den här rättighets hanterings rollen.

1. Klicka på **Välj**.

1. Klicka på **Spara**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Lägg till en katalog ägare eller en Access Package Manager

Om du vill delegera hantering av en katalog eller åtkomst paket i katalogen lägger du till användare i katalog ägaren eller Access Package Manager-roller. Vem som skapar en katalog blir den första katalog ägaren. Följ de här stegen för att tilldela en användare rollen som katalog ägare eller Access Package Manager.

**Nödvändig roll:** Global administratör, användar administratör eller katalog ägare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill lägga till administratörer i.

1. På den vänstra menyn klickar du på **roller och administratörer**.

1. Klicka på **Lägg till ägare** eller **Lägg till paket hanterare** för att välja medlemmar för dessa roller.

1. Klicka på **Välj** för att lägga till dessa medlemmar.

## <a name="next-steps"></a>Nästa steg

- [Lägg till god kännare](entitlement-management-access-package-edit.md#policy-request)
- [Lägga till resurser i en katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
