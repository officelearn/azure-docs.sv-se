---
title: Delegera åtkomststyrning till åtkomst till pakethanterare i Azure AD-berättigandehantering - Azure Active Directory
description: Lär dig hur du delegerar åtkomststyrning från IT-administratörer för att komma åt paketansvariga och projektledare så att de kan hantera åtkomsten själva.
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
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174370"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegera åtkomststyrning till åtkomstpakethanterare i Azure AD-berättigandehantering

Om du vill delegera skapandet och hanteringen av åtkomstpaket i en katalog lägger du till användare i rollen för åtkomstpakethanteraren. Åtkomstpakethanterare måste känna till behovet för användare att begära åtkomst till resurser i en katalog. Om till exempel en katalog används för ett projekt kan en projektledare vara en åtkomstpakethanterare för den katalogen.  Åtkomstpakethanterare kan inte lägga till resurser i en katalog, men de kan hantera åtkomstpaket och principer i en katalog.  När du delegerar till en pakethanterare för åtkomst kan den personen sedan ansvara för:

- Vilka roller en användare ska ha för resurserna i en katalog
- Vem behöver tillgång
- Vem behöver godkänna åtkomstbegäranden
- Hur länge projektet kommer att pågå

Den här videon innehåller en översikt över hur du delegerar åtkomststyrning från katalogägaren till åtkomst till pakethanteraren.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Som katalogägare delegerar du till en åtkomstpakethanterare

Följ dessa steg för att tilldela en användare till rollen för åtkomstpakethanteraren:

**Viktig roll:** Global administratör, användaradministratör eller katalogägare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Kataloger** på den vänstra menyn och öppna sedan den katalog som du vill lägga till administratörer i.

1. Klicka på Roller **och administratörer**på den vänstra menyn.

    ![Katalogroller och administratörer](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klicka på **Lägg till pakethanterare** för åtkomst om du vill välja medlemmar för dessa roller.

1. Klicka på **Markera** om du vill lägga till dessa medlemmar.

## <a name="remove-an-access-package-manager"></a>Ta bort en åtkomstpakethanterare

Så här tar du bort en användare från rollen för åtkomstpakethanteraren:

**Viktig roll:** Global administratör, användaradministratör eller katalogägare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Kataloger** på den vänstra menyn och öppna sedan den katalog som du vill lägga till administratörer i.

1. Klicka på Roller **och administratörer**på den vänstra menyn.

1. Lägg till en bock bredvid en åtkomstpakethanterare som du vill ta bort.

1. Klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett nytt åtkomstpaket](entitlement-management-access-package-create.md)
