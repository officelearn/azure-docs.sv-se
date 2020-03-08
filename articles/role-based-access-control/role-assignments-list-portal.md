---
title: Lista roll tilldelningar med hjälp av Azure RBAC och Azure Portal
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvud namn eller hanterade identiteter har åtkomst till med hjälp av rollbaserad åtkomst kontroll i Azure (RBAC) och Azure Portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 63aef8d2036e442659145e031d384e9677c779a1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393986"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Lista roll tilldelningar med hjälp av Azure RBAC och Azure Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] den här artikeln beskriver hur du visar roll tilldelningar med hjälp av Azure Portal.

> [!NOTE]
> Om din organisation har funktioner som har hanterats av en tjänst leverantör som använder [Azure-delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md), visas inte roll tilldelningar som har auktoriserats av tjänste leverantören här.

## <a name="list-role-assignments-for-a-user-or-group"></a>Lista roll tilldelningar för en användare eller grupp

Det enklaste sättet att se de roller som har tilldelats en användare eller grupp i en prenumeration är att använda fönstret **Azure-resurser** .

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan **användare** eller **grupper**.

1. Klicka på den användare eller grupp som du vill visa en lista över roll tilldelningarna för.

1. Klicka på **Azure-resurser**.

    Du ser en lista över roller som har tilldelats den valda användaren eller gruppen vid olika omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs. Den här listan innehåller alla roll tilldelningar som du har behörighet att läsa.

    ![Rolltilldelningar för en användare](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Klicka på listan **prenumerationer** om du vill ändra prenumerationen.

## <a name="list-owners-of-a-subscription"></a>Lista ägare för en prenumeration

Användare som har tilldelats [ägar](built-in-roles.md#owner) rollen för en prenumeration kan hantera allt i prenumerationen. Följ dessa steg om du vill visa en lista över ägare till en prenumeration.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **Prenumerationer**.

1. Klicka på den prenumeration som du vill lista ägarna till.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

1. Bläddra till avsnittet **ägare** om du vill se alla användare som har tilldelats ägar rollen för den här prenumerationen.

   ![Åtkomst kontroll för prenumeration – fliken roll tilldelningar](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Lista roll tilldelningar i ett omfång

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper**eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

   ![Åtkomst kontroll – fliken roll tilldelningar](./media/role-assignments-list-portal/access-control-role-assignments.png)

   På fliken roll tilldelningar kan du se vem som har åtkomst till det här omfånget. Observera att vissa roller är begränsade till **den här resursen** medan andra är **(Ärvda)** från ett annat omfång. Åtkomst tilldelas antingen specifikt till den här resursen eller ärvts från en tilldelning till det överordnade omfånget.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Lista roll tilldelningar för en användare i ett omfång

Om du vill visa en lista över åtkomsten för en användare, grupp, tjänstens huvud namn eller en hanterad identitet anger du roll tilldelningarna. Följ dessa steg om du vill visa roll tilldelningarna för en enskild användare, grupp, tjänstens huvud namn eller hanterad identitet i ett visst omfång.

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper**eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **Kontrollera åtkomst**.

    ![Åtkomstkontroll – fliken Kontrollera åtkomst](./media/role-assignments-list-portal/access-control-check-access.png)

1. I listan **Hitta** väljer du den typ av säkerhetsprincip som du vill kontrollera åtkomst för.

1. I sökrutan anger du en sträng för att söka i katalogen efter visningsnamn, e-postadresser eller objektidentifierare.

    ![Välj lista för Kontrollera åtkomst](./media/role-assignments-list-portal/check-access-select.png)

1. Klicka på säkerhetsprincipen för att öppna fönsterrutan **tilldelningar**.

    ![fönsterrutan tilldelningar](./media/role-assignments-list-portal/check-access-assignments.png)

    I den här fönsterrutan kan du se de roller som tilldelats till den valda säkerhetsprincipen och omfånget. Om det finns några nekande tilldelningar i det här omfånget eller som ärvts till det här omfånget visas de.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Lista roll tilldelningar för en systemtilldelad hanterad identitet

1. I Azure Portal öppnar du en systemtilldelad hanterad identitet.

1. Klicka på **identitet**i den vänstra menyn.

    ![Systemtilldelad hanterad identitet](./media/role-assignments-list-portal/identity-system-assigned.png)

1. Under **roll tilldelningar**klickar du på **Visa de Azure RBAC-roller som tilldelats den här hanterade identiteten**.

    Du ser en lista över roller som har tilldelats den valda systemtilldelade hanterade identiteten vid olika omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs. Den här listan innehåller alla roll tilldelningar som du har behörighet att läsa.

    ![Roll tilldelningar för en systemtilldelad hanterad identitet](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Lista roll tilldelningar för en användardefinierad hanterad identitet

1. Öppna en användardefinierad hanterad identitet i Azure Portal.

1. Klicka på **Azure-resurser**.

    Du ser en lista över roller som har tilldelats den valda användarspecifika hanterade identiteten vid olika omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs. Den här listan innehåller alla roll tilldelningar som du har behörighet att läsa.

    ![Roll tilldelningar för en systemtilldelad hanterad identitet](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Klicka på listan **prenumerationer** om du vill ändra prenumerationen.

## <a name="list-number-of-role-assignments"></a>Lista antal roll tilldelningar

Du kan ha upp till **2000** roll tilldelningar i varje prenumeration. För att hjälpa dig att hålla koll på dessa begränsningar innehåller fliken **roll tilldelningar** ett diagram som visar det aktuella antalet roll tilldelningar.

![Åtkomst kontroll-antal roll tilldelnings diagram](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Om du får nära det högsta antalet och du försöker lägga till fler roll tilldelningar visas en varning i fönstret **Lägg till roll tilldelning** . Du kan minska antalet roll tilldelningar genom att ta bort roll tilldelningar som inte längre behövs eller genom att tilldela roller till grupper i stället för enskilda användare.

![Åtkomst kontroll – Lägg till Roll tilldelnings varning](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och Azure Portal](role-assignments-portal.md)
- [Felsöka RBAC för Azure-resurser](troubleshooting.md)
