---
title: Delegera åtkomst styrning för åtkomst till paket hanterare i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du delegerar åtkomst styrning från IT-administratörer till att komma åt paket hanterare och projekt hanterare så att de kan hantera åtkomst själva.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73174370"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegera åtkomst styrning för att få åtkomst till paket hanterare i Azure AD-hantering av rättigheter

För att delegera skapandet och hanteringen av åtkomst paket i en katalog lägger du till användare till Access Package Manager-rollen. Åtkomst till paket hanterare måste vara bekant med användarnas behov av att begära åtkomst till resurser i en katalog. Om en katalog till exempel används för ett projekt kan ett projekt lead vara en Access Package Manager för katalogen.  Åtkomst till paket hanterare kan inte lägga till resurser i en katalog, men de kan hantera åtkomst paket och principer i en katalog.  När du delegerar till en Access Package Manager kan den personen ansvara för:

- Vilka roller som en användare kommer att ha till resurserna i en katalog
- Vem som behöver åtkomst
- Vem som behöver godkänna åtkomst begär Anden
- Hur länge projektet ska vara sist

Den här videon ger en översikt över hur du delegerar åtkomst styrning från katalog ägare till Access Package Manager.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Som katalog ägare delegerar du till en Access Package Manager

Följ de här stegen för att tilldela en användare rollen Access Package Manager:

**Nödvändig roll:** Global administratör, användar administratör eller katalog ägare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill lägga till administratörer i.

1. På den vänstra menyn klickar du på **roller och administratörer**.

    ![Katalog roller och administratörer](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klicka på **Lägg till Access paket hanterare** för att välja medlemmar för dessa roller.

1. Klicka på **Välj** för att lägga till dessa medlemmar.

## <a name="remove-an-access-package-manager"></a>Ta bort en Access Package Manager

Följ dessa steg om du vill ta bort en användare från Access Package Manager-rollen:

**Nödvändig roll:** Global administratör, användar administratör eller katalog ägare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **kataloger** i den vänstra menyn och öppna den katalog som du vill lägga till administratörer i.

1. På den vänstra menyn klickar du på **roller och administratörer**.

1. Lägg till en bock bredvid en Access Package Manager som du vill ta bort.

1. Klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett nytt åtkomstpaket](entitlement-management-access-package-create.md)
