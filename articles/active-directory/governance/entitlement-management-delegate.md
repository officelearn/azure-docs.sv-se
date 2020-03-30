---
title: Delegering och roller i rättighetshantering - Azure AD
description: Lär dig hur du delegerar åtkomststyrning från IT-administratörer till avdelningschefer och projektledare så att de kan hantera åtkomsten själva.
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261845"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegering och roller i Azure AD-berättigandehantering

Som standard kan globala administratörer och användaradministratörer skapa och hantera alla aspekter av Azure AD-berättigandehantering. Användarna i dessa roller kanske dock inte känner till alla situationer där åtkomstpaket krävs. Vanligtvis är det användare inom respektive avdelningar, team eller projekt som vet vem de samarbetar med, med hjälp av vilka resurser och hur länge. I stället för att bevilja obegränsad behörighet till icke-administratörer kan du ge användarna minst behörigheter de behöver för att utföra sitt jobb och undvika att skapa motstridiga eller olämpliga åtkomsträttigheter.

Det här videoklippet innehåller en översikt över hur du delegerar åtkomststyrning från IT-administratör till användare som inte är administratörer.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Exempel på ombud

För att förstå hur du kan delegera åtkomststyrning i rättighetshantering hjälper det att överväga ett exempel. Anta att din organisation har följande administratör och chefer.

![Delegera från IT-administratör till chefer](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Som IT-administratör har Hana kontakter inom varje avdelning - Mamta i marknadsföring, Mark in Finance och Joe in Legal som ansvarar för avdelningens resurser och affärskritiska innehåll.

Med rättighetshantering kan du delegera åtkomststyrning till dessa icke-administratörer eftersom det är de som vet vilka användare som behöver åtkomst, hur länge och till vilka resurser. Detta säkerställer rätt personer hanterar åtkomst för sina avdelningar.

Här är ett sätt att Hana kan delegera tillgång styrning till marknadsföring, ekonomi och juridiska avdelningar.

1. Hana skapar en ny Azure AD-säkerhetsgrupp och lägger till Mamta, Mark och Joe som medlemmar i gruppen.

1. Hana lägger till den gruppen i rollen som skapare av katalogen.

    Mamta, Mark och Joe kan nu skapa kataloger för sina avdelningar, lägga till resurser som deras avdelningar behöver och göra ytterligare delegering i katalogen.

    Observera att Mamta, Mark och Joe inte kan se varandras kataloger.

1. Mamta skapar en **marknadsföringskatalog,** som är en behållare med resurser.

1. Mamta lägger till de resurser som hennes marknadsavdelning äger i den här katalogen.

1. Mamta kan lägga till ytterligare personer från sin avdelning som katalogägare för den här katalogen. Detta hjälper till att dela kataloghanteringsansvaret.

1. Mamta kan ytterligare delegera skapandet och hanteringen av åtkomstpaket i marknadsföringskatalogen till projektledare på marknadsavdelningen. Hon kan göra detta genom att tilldela dem till rollen åtkomstpakethanterare. En åtkomstpakethanterare kan skapa och hantera åtkomstpaket. 

I följande diagram visas kataloger med resurser för marknadsförings-, ekonomi- och juridiska avdelningar. Med hjälp av dessa kataloger kan projektledare skapa åtkomstpaket för sina team eller projekt.

![Exempel på ombud för berättigandehantering](./media/entitlement-management-delegate/elm-delegate.png)

Efter delegering kan marknadsavdelningen ha roller som liknar följande tabell.

| Användare | Jobbroll | Azure AD-roll | Rollen för hantering av rättigheter |
| --- | --- | --- | --- |
| Hana | IT-administratör | Global administratör eller användaradministratör |  |
| Mamta (ort i) | Marknadschef | Användare | Katalog skapare och katalog ägare |
| Bob | Marknadsföring bly | Användare | Katalogägare |
| Jessica | Marknadsprojektledare | Användare | Pakethanterare för åtkomst |

## <a name="entitlement-management-roles"></a>Roller för hantering av berättigande

Rättighetshantering har följande roller som är specifika för rättighetshantering.

| Rollen för hantering av rättigheter | Beskrivning |
| --- | --- |
| Skapare av katalog | Skapa och hantera kataloger. Vanligtvis en IT-administratör som inte är global administratör eller resursägare för en samling resurser. Den person som skapar en katalog blir automatiskt katalogens första katalogägare och kan lägga till ytterligare katalogägare. En katalogskapare kan inte hantera eller se kataloger som de inte äger och kan inte lägga till resurser som de inte äger i en katalog. Om katalogskaparen behöver hantera en annan katalog eller lägga till resurser som de inte äger kan de begära att bli delägare i den katalogen eller resursen. |
| Katalogägare | Redigera och hantera befintliga kataloger. Vanligtvis en IT-administratör eller resursägare, eller en användare som katalogägaren har utsett. |
| Pakethanterare för åtkomst | Redigera och hantera alla befintliga åtkomstpaket i en katalog. |

Dessutom har en utsedd godkännare och en beställare av ett åtkomstpaket också rättigheter, även om de inte är roller.

| Höger | Beskrivning |
| --- | --- |
| Approver | Auktoriserat av en princip för att godkänna eller neka begäranden om åtkomstpaket, även om de inte kan ändra definitionerna för åtkomstpaket. |
| Requestor | Auktoriseras av en princip för ett åtkomstpaket för att begära det åtkomstpaketet. |

I följande tabell visas de uppgifter som berättigandehanteringsrollerna kan utföra.

| Aktivitet | Admin | Skapare av katalog | Katalogägare | Pakethanterare för åtkomst |
| --- | :---: | :---: | :---: | :---: |
| [Delegera till en katalogskapare](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Lägga till en ansluten organisation](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Skapa en ny katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Lägga till en resurs i en katalog](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Lägga till en katalogägare](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Redigera en katalog](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Ta bort en katalog](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegera till en åtkomstpakethanterare](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Ta bort en åtkomstpakethanterare](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Skapa ett nytt åtkomstpaket i en katalog](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Ändra resursroller i ett åtkomstpaket](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Skapa och redigera principer](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Tilldela en användare direkt till ett åtkomstpaket](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visa vem som har en tilldelning till ett åtkomstpaket](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visa ett åtkomstpakets begäranden](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Visa leveransfel för en begäran](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Bearbeta om en begäran](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Avbryta en väntande begäran](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Dölja ett åtkomstpaket](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Ta bort ett åtkomstpaket](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Roller krävs för att lägga till resurser i en katalog

En global administratör kan lägga till eller ta bort en grupp (molnskapade säkerhetsgrupper eller molnskapade Office 365-grupper), program eller SharePoint Online-webbplats i en katalog. En användaradministratör kan lägga till eller ta bort en grupp eller ett program i en katalog.

För en användare som inte är global administratör eller användaradministratör, för att lägga till grupper, program eller SharePoint Online-webbplatser i en katalog, måste den användaren ha *både* den azure AD-katalogroll som krävs och rollen för hantering av katalogägares berättigande. I följande tabell visas de rollkombinationer som krävs för att lägga till resurser i en katalog. Om du vill ta bort resurser från en katalog måste du ha samma roller.

| Azure AD-katalogroll | Rollen för hantering av rättigheter | Kan lägga till säkerhetsgrupp | Kan lägga till Office 365-grupp | Kan lägga till app | Kan lägga till SharePoint Online-webbplats |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Global administratör](../users-groups-roles/directory-assign-admin-roles.md) | Saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Användaradministratör](../users-groups-roles/directory-assign-admin-roles.md) | Saknas |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogägare | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogägare |  | :heavy_check_mark: |  |  |
| [Administratör för teamtjänsten](../users-groups-roles/directory-assign-admin-roles.md) | Katalogägare |  | :heavy_check_mark: |  |  |
| [SharePoint-administratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogägare |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Programadministratör](../users-groups-roles/directory-assign-admin-roles.md) | Katalogägare |  |  | :heavy_check_mark: |  |
| [Administratör för molnprogram](../users-groups-roles/directory-assign-admin-roles.md) | Katalogägare |  |  | :heavy_check_mark: |  |
| Användare | Katalogägare | Endast om gruppägaren | Endast om gruppägaren | Endast om appens ägare |  |

Om du vill bestämma vilken minst privilegierad roll en uppgift som är mest privilegierad kan du också referera till [administratörsroller efter administratörsuppgift i Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Nästa steg

- [Delegera åtkomststyrning till katalogskapare](entitlement-management-delegate-catalog.md)
- [Skapa och hantera en katalog med resurser](entitlement-management-catalog-create.md)
