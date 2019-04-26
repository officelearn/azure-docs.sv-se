---
title: Återställ eller ta bort en nyligen borttagna användare – Azure Active Directory permanent | Microsoft Docs
description: Så här att visa återställningsbara användare, Återställ en borttagen användare eller permanent ta bort en användare med Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d69bd931f2f8c72fd1e6fc79c16662ea367617d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60248220"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Återställa eller ta bort en nyligen borttagna användare som använder Azure Active Directory
När du tar bort en användare kan fortfarande kontot i ett väntetillstånd i 30 dagar. Under den 30-dagars fönstret kan användarkontot återställas, tillsammans med alla dess egenskaper. När 30-dagars fönstret skickar bort användaren automatiskt och permanent.

Du kan visa dina återställningsbara användare, Återställ en borttagen användare eller permanent ta bort en användare med Azure Active Directory (AD Azure) i Azure-portalen.

>[!Important]
>Varken du eller Microsofts kundsupport kan återställa en användare som har tagits bort permanent.

## <a name="required-permissions"></a>Nödvändiga behörigheter
Du måste ha något av följande roller för att återställa och ta bort användarna permanent.

- Global administratör

- Partnersupport, nivå 1

- Partnersupport, nivå 2

- Användaradministratör

## <a name="view-your-restorable-users"></a>Visa dina återställningsbara användare
Du kan se alla användare som har tagits bort mindre än 30 dagar sedan. Dessa användare kan återställas.

### <a name="to-view-your-restorable-users"></a>Visa dina återställningsbara användare
1. Logga in på den [Azure-portalen](https://portal.azure.com/) med hjälp av ett globalt administratörskonto för organisationen.

2. Välj **Azure Active Directory**väljer **användare**, och välj sedan **borttagna användare**.

    Granska listan över användare som är tillgängliga för återställning.

    ![Användare – sidan borttagen användare med användare som fortfarande kan återställas](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Återställa en nyligen borttagna användare

När ett användarkonto tas bort från organisationen, kontot är i ett väntetillstånd och alla relaterade organisationsinformationen bevaras. När du återställer en användare kan återställs den här organisationen också.

> [!Note]
> När en användare har återställts, återställs även licenser som har tilldelats till användaren vid tidpunkten för tas bort även om det finns inga platser för dessa licenser. Om du sedan förbrukar mer licenser mer än vad du har köpt, din organisation kan vara tillfälligt uppfyller kraven för användningen av licenser.

### <a name="to-restore-a-user"></a>Att återställa en användare
1. På den **användare – borttagna användare** , söka efter och välj en av de tillgängliga användarna. Till exempel _Mary Parker_.

2. Välj **återställning användaren**.

    ![Användare – sidan borttagna användare med återställning användare alternativet är markerat](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Ta bort en användare permanent
Du kan permanent ta bort en användare från din organisation utan att vänta i 30 dagar för automatisk borttagning. En permanent borttagen användare kan inte återställas av dig, en annan administratör eller av Microsoft kundsupport.

>[!Note]
>Om du permanent ta bort en användare av misstag, måste du skapa en ny användare och manuellt ange all information som tidigare. Mer information om hur du skapar en ny användare finns i [Lägg till eller ta bort användare](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Permanent ta bort en användare

1. På den **användare – borttagna användare** , söka efter och välj en av de tillgängliga användarna. Till exempel _Rae Huff_.

2. Välj **ta bort permanent**.

    ![Användare – sidan borttagna användare med återställning användare alternativet är markerat](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Nästa steg
När du har återställt eller ta bort användarna, kan du utföra följande basic-processer:

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)

- [Lägga till gästanvändare från en annan organisation](../b2b/what-is-b2b.md)

Mer information om andra tillgängliga hanteringsuppgifter, [dokumentation om Azure AD user management](../users-groups-roles/index.yml).
