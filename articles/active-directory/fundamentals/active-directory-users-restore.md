---
title: Återställa eller permanent ta bort nyligen borttagen användare - Azure AD
description: Visa återställningsbara användare, återställa en borttagen användare eller ta bort en användare permanent med Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 573269da1ca6b9ee09b493f4e758e78121d6c2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422869"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Återställa eller ta bort en nyligen borttagen användare med Azure Active Directory
När du har tagit bort en användare förblir kontot i ett pausat tillstånd i 30 dagar. Under det 30-dagarsfönstret kan användarkontot återställas, tillsammans med alla dess egenskaper. Efter att 30-dagarsfönstret passerar, användaren automatiskt, och permanent, tas bort.

Du kan visa dina återställningsbara användare, återställa en borttagen användare eller permanent ta bort en användare med Azure Active Directory (Azure AD) i Azure-portalen.

>[!Important]
>Varken du eller Microsofts kundsupport kan återställa en permanent borttagen användare.

## <a name="required-permissions"></a>Nödvändiga behörigheter
Du måste ha en av följande roller för att återställa och permanent ta bort användare.

- Global administratör

- Support för partnernivå1

- Support för partnernivå2

- Användaradministratör

## <a name="view-your-restorable-users"></a>Visa dina återställningsbara användare
Du kan se alla användare som togs bort för mindre än 30 dagar sedan. Dessa användare kan återställas.

### <a name="to-view-your-restorable-users"></a>Så här visar du dina återställningsbara användare
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett globalt administratörskonto för organisationen.

2. Välj **Azure Active Directory**, välj **Användare**och välj sedan **Borttagna användare**.

    Granska listan över användare som är tillgängliga för återställning.

    ![Användare - Borttagna användare sida, med användare som fortfarande kan återställas](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Återställa en nyligen borttagen användare

När ett användarkonto tas bort från organisationen är kontot i ett pausat tillstånd och all relaterad organisationsinformation bevaras. När du återställer en användare återställs även den här organisationsinformationen.

> [!Note]
> När en användare har återställts återställs även licenser som tilldelats användaren vid tidpunkten för borttagningen, även om det inte finns några tillgängliga platser för dessa licenser. Om du sedan konsumerar fler licenser mer än du har köpt kan din organisation vara tillfälligt utanför överensstämmelse för licensanvändning.

### <a name="to-restore-a-user"></a>Så här återställer du en användare
1. På sidan **Användare - Borttagna användare** söker du efter och väljer en av de tillgängliga användarna. Till exempel _Mary Parker_.

2. Välj **Återställ användare**.

    ![Sidan Användare - Borttagna användare, med alternativet Återställ användare markerat](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Ta bort en användare permanent
Du kan ta bort en användare från organisationen permanent utan att vänta på 30 dagar för automatisk borttagning. En permanent borttagen användare kan inte återställas av dig, en annan administratör eller av Microsofts kundsupport.

>[!Note]
>Om du tar bort en användare permanent av misstag måste du skapa en ny användare och ange all tidigare information manuellt. Mer information om hur du skapar en ny användare finns i [Lägga till eller ta bort användare](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Så här tar du bort en användare permanent

1. På sidan **Användare - Borttagna användare** söker du efter och väljer en av de tillgängliga användarna. Till _exempel, Rae Huff_.

2. Välj **Ta bort permanent**.

    ![Sidan Användare - Borttagna användare, med alternativet Återställ användare markerat](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Nästa steg
När du har återställt eller tagit bort användarna kan du utföra följande grundläggande processer:

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)

- [Lägga till gästanvändare från en annan organisation](../b2b/what-is-b2b.md)

Om du vill ha mer information om andra tillgängliga användarhanteringsuppgifter finns [dokumentation för Azure AD-användarhantering](../users-groups-roles/index.yml).
