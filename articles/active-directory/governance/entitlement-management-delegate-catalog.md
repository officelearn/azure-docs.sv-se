---
title: Delegera åtkomststyrning till katalogskapare i Azure AD-rättighetshantering - Azure Active Directory
description: Lär dig hur du delegerar åtkomststyrning från IT-administratörer till katalogskapare och projektledare så att de kan hantera åtkomsten själva.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120190"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegera åtkomststyrning till katalogskapare i Azure AD-berättigandehantering

En katalog är en behållare med resurser och åtkomstpaket. Du skapar en katalog när du vill gruppera relaterade resurser och åtkomstpaket. Som standard kan en global administratör eller en användaradministratör [skapa en katalog](entitlement-management-catalog-create.md)och lägga till ytterligare användare som katalogägare.

Om du vill delegera till användare som inte är administratörer, så att de kan skapa sina egna kataloger, kan du lägga till dessa användare i rollen hantering av Azure AD-berättigande. Du kan lägga till enskilda användare eller lägga till en grupp vars medlemmar sedan kan skapa kataloger.  När du har skapat en katalog kan de sedan lägga till resurser som de äger i sin katalog.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Som IT-administratör delegerar du till en katalogskapare

Följ dessa steg för att tilldela en användare till rollen som skapare av katalogen.

**Viktig roll:** Global administratör eller användaradministratör

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Inställningar**i avsnittet **Rättighetshantering** i den vänstra menyn .

1. Klicka på **Redigera**.

    ![Inställningar för att lägga till katalogskapare](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. I avsnittet **Delegera berättigandehantering** klickar du på **Lägg till katalogskapare** för att välja de användare eller grupper som du vill delegera rollen för rättighetshantering till.

1. Klicka på **Markera**.

1. Klicka på **Spara**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Tillåt delegerade roller att komma åt Azure-portalen

Om du vill tillåta delegerade roller, till exempel katalogskapare och åtkomstpakethanterare, för att komma åt Azure-portalen för att hantera åtkomstpaket bör du kontrollera inställningen för administrationsportalen.

**Viktig roll:** Global administratör eller användaradministratör

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Användare**.

1. Klicka på **Användarinställningar**på den vänstra menyn .

1. Se till **att Begränsa åtkomsten till Azure AD-administrationsportalen** är inställd på **Nr**.

    ![Azure AD-användarinställningar - Administrationsportal](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera en katalog med resurser](entitlement-management-catalog-create.md)
- [Delegera åtkomststyrning till åtkomstpakethanterare](entitlement-management-delegate-managers.md)

